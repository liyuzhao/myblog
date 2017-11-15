---
title: WebView调用本地的图片选择
date: 2016-05-19 16:21:20
tags: [android, WebView]
categories: android
---

## Android中WebView调用本地图片选择器

-----
默认的情况下WebView是不能调用本地图片选择器的，Android的默认浏览器是支持的，因此我可以断定这是完全能做到的

在做的过程中，确实遇到了一些问题，开始以为是特殊手机原因，后来调查才确定是AndroidSDK的版本问题

* Android 3.0 以前的版本
* Android 3.x 版本
* Android 4.x 版本
* Android 5.x 版本

<!-- more -->
> 主要区别：为 5.x版本以前为ValueCallback&lt;Uri&gt;
> 从5.x版本返回的是ValueCallback&lt;Uri[]&gt;
> 因此只用单独一种的话可能会有一些版本不能用，曾经我就犯过这样的错误

具体实现方式，请看下面的代码部分：

### MainActivity.java 代码如下：

```

public class MainActivity extends Activity {
	public  ValueCallback<Uri> mUploadMessage;
	public ValueCallback<Uri[]> mUploadMessageForAndroid5;
	public final static int FILECHOOSER_RESULTCODE = 1;
	public final static int FILECHOOSER_RESULTCODE_FOR_ANDORID_5 = 2;
	ProgressDialog progressBar;
	private WebView mWebView;
	private ImageButton btnBack;

	private static final String remoteUrl = "http://kefu.easemob.com/webim/im.html?tenantId=35";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }

    @SuppressLint("NewApi") private void initView(){
    	mWebView = (WebView) findViewById(R.id.webview);
    	btnBack = (ImageButton) findViewById(R.id.back);
    	btnBack.setOnClickListener(new View.OnClickListener() {

			@Override
			public void onClick(View v) {
				finish();
			}
		});
    	progressBar = new ProgressDialog(this);
    	progressBar.setProgressStyle(ProgressDialog.STYLE_SPINNER);
    	mWebView.getSettings().setJavaScriptEnabled(true);
    	mWebView.getSettings().setAppCacheEnabled(false);
    	mWebView.getSettings().setDomStorageEnabled(true);
    	mWebView.getSettings().setCacheMode(WebSettings.LOAD_NO_CACHE);
    	if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT){
    		mWebView.getSettings().setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
    	}
    	mWebView.loadUrl(remoteUrl);
    	mWebView.setWebViewClient(new WebViewClient(){
    		@Override
    		public boolean shouldOverrideUrlLoading(WebView view, String url) {
    			handler.sendEmptyMessage(0);
    			view.loadUrl(url);
    			return true;
    		}
    	});
    	mWebView.setWebChromeClient(new WebChromeClient(){
    		@Override
    		public void onProgressChanged(WebView view, int newProgress) {
    			if(newProgress == 100){
    				handler.sendEmptyMessage(1);//如果全部载入，隐藏进度对话框
    			}

    			super.onProgressChanged(view, newProgress);
    		}

    		//扩展支持alert事件
    		@Override
    		public boolean onJsAlert(WebView view, String url, String message,
    				JsResult result) {
    			AlertDialog.Builder builder = new AlertDialog.Builder(view.getContext());
    			builder.setTitle("提示").setMessage(message).setPositiveButton("确定", null);
    			builder.setCancelable(false);
    			builder.setIcon(R.drawable.ic_launcher);
    			AlertDialog dialog = builder.create();
    			dialog.show();
    			result.confirm();
    			return true;
    		}

    		//扩展浏览器上传文件
    		//3.0++版本
    		public void openFileChooser(ValueCallback<Uri> uploadMsg, String acceptType){
    			openFileChooserImpl(uploadMsg);
    		}
    		//3.0--版本
    		public void openFileChooser(ValueCallback<Uri> uploadMsg){
    			openFileChooserImpl(uploadMsg);
    		}

    		public void openFileChooser(ValueCallback<Uri> uploadMsg, String acceptType, String capture){
    			openFileChooserImpl(uploadMsg);
    		}

    		// For Android > 5.0
    		public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> uploadMsg, WebChromeClient.FileChooserParams fileChooserParams){
    			openFileChooserImplForAndroid5(uploadMsg);
    			return true;
    		}

    	});
    }

    private void openFileChooserImpl(ValueCallback<Uri> uploadMsg){
    	mUploadMessage = uploadMsg;
    	Intent i = new Intent(Intent.ACTION_GET_CONTENT);
    	i.addCategory(Intent.CATEGORY_OPENABLE);
    	i.setType("image/*");
    	startActivityForResult(Intent.createChooser(i, "File Chooser"), FILECHOOSER_RESULTCODE);
    }

    private void openFileChooserImplForAndroid5(ValueCallback<Uri[]> uploadMsg){
    	mUploadMessageForAndroid5 = uploadMsg;
    	Intent contentSelectionIntent = new Intent(Intent.ACTION_GET_CONTENT);
    	contentSelectionIntent.addCategory(Intent.CATEGORY_OPENABLE);
    	contentSelectionIntent.setType("image/*");

    	Intent chooserIntent = new Intent(Intent.ACTION_CHOOSER);
    	chooserIntent.putExtra(Intent.EXTRA_INTENT, contentSelectionIntent);
    	chooserIntent.putExtra(Intent.EXTRA_TITLE, "Image Chooser");
    	startActivityForResult(chooserIntent, FILECHOOSER_RESULTCODE_FOR_ANDORID_5);
    }

    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
    	if(mWebView.canGoBack() && event.getKeyCode() == KeyEvent.KEYCODE_BACK){
    		//获取历史列表
    		WebBackForwardList mWebBackForwardList = mWebView.copyBackForwardList();
    		//判断当前历史列表是否最顶端，其实canGoBack已经判断过
    		if(mWebBackForwardList.getCurrentIndex() > 0){
    			mWebView.goBack();
    			return true;
    		}
    	}
    	return super.onKeyDown(keyCode, event);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent intent) {
    	if(requestCode == FILECHOOSER_RESULTCODE){
    		if(null == mUploadMessage)
    			return;
    		Uri result = intent == null || resultCode != RESULT_OK ? null : intent.getData();
    		mUploadMessage.onReceiveValue(result);
    		mUploadMessage = null;
    	}else if(requestCode == FILECHOOSER_RESULTCODE_FOR_ANDORID_5){
    		if(null == mUploadMessageForAndroid5)
    			return;
    		Uri result = (intent == null || resultCode != RESULT_OK) ? null: intent.getData();
    		if(result != null){
    			mUploadMessageForAndroid5.onReceiveValue(new Uri[]{result});
    		}else{
    			mUploadMessageForAndroid5.onReceiveValue(new Uri[]{});
    		}
    		mUploadMessageForAndroid5 = null;
    	}
    }

    private Handler handler = new Handler(){
    	public void handleMessage(android.os.Message msg) {//定义一个Handler， 用于处理下载线程与UI间通讯
    		if(!Thread.currentThread().isInterrupted()){
    			switch(msg.what){
    			case 0:
    				progressBar.show();//显示进度框
    				break;
    			case 1:
    				progressBar.hide();//隐藏进度对话框不可使用dismiss()、cancel()，否则再次调用show()时,显示的对话框小圆圈不会动
    				break;
    			}
    		}
    		super.handleMessage(msg);
    	};
    };
}

```
### activity_main.xml的代码

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="45dip"
        android:background="@android:color/darker_gray"
        >
        <ImageButton
            android:id="@+id/back"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="@android:drawable/ic_delete"
            android:layout_centerVertical="true"
            />

        <TextView
            android:id="@+id/title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="WebViewDemo"
            android:layout_centerVertical="true"
            android:layout_centerHorizontal="true"
            />
    </RelativeLayout>
    <WebView
        android:id="@+id/webview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        />
</LinearLayout>

```

### 最后在AndroidManifest.xml中不要忘记权限

```
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
```
什么？你告诉我你不喜欢自己写？好吧，我提供源码下载。

[Demo源码下载](http://7xpj58.com1.z0.glb.clouddn.com/WebViewTest.zip)


到此结束，欢迎提供bug和意见！
