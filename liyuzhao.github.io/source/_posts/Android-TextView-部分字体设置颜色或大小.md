---
title: Android TextView 部分字体设置颜色或大小
date: 2016-03-10 21:06:03
tags: [Android]
categories: Android
---
### Android TextView 部分字体设置颜色或大小 
>在某些情况下，会遇到某些关键字需要**高亮**显示，可Android自带的TextView支持很困难。

>背景来自：今天一同学问我怎么实现，给他举例两种方式，他感觉不方便，因此我才写此文章，简单介绍下。

#### 支持方式一
可以通过HTML的方式，因为TextView可以加载HTML，因此可以简单的实现，缺点是和Android自带的字体看起来有点别扭。

`````
textView4.setText(Html.fromHtml("北京市发布霾黄色预警，<font color='#ff0000'><small><small>外出携带好</small></small></font>口罩"));
`````
<!--more-->

#### 支持方式二
可以通过代码的方式，对TextView设置部分字体的颜色。

`````
Spannable span = new SpannableString(textView3.getText());
span.setSpan(new AbsoluteSizeSpan(58), 11, 16, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
span.setSpan(new ForegroundColorSpan(Color.RED), 11, 16, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
span.setSpan(new BackgroundColorSpan(Color.YELLOW), 11, 16, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
textView3.setText(span);
`````


----
以上两种方式，我感觉就可以了，不过有些同学感觉就是用起来比较麻烦，因此我们可以封装下第二种方法，变为第三种方法，也就是我要写的。

#### 支持方式三

通过自定义View的方式来封装这些代码。

##### 首先是CTextView extends TextView,当然名称无所谓，自己喜欢就好

`````
public class CTextView extends TextView {
    private float normalTextSize;
    private float highlightTextSize;
    private int normalTextColor;
    private int highlightTextColor;

    public CTextView(Context context) {
        super(context);
    }

    public CTextView(Context context, AttributeSet attrs) {
        super(context, attrs);
        TypedArray typedArray = null;
        try {
            typedArray = context.obtainStyledAttributes(attrs, R.styleable.CTextView, 0, 0);
            normalTextSize = typedArray.getDimension(R.styleable.CTextView_normal_textsize, 14);
            highlightTextSize = typedArray.getDimension(R.styleable.CTextView_highlight_textsize, 14);
            normalTextColor = typedArray.getColor(R.styleable.CTextView_normal_textcolor, Color.BLACK);
            highlightTextColor = typedArray.getColor(R.styleable.CTextView_highlight_textcolor, Color.BLACK);

        } finally {
            if (typedArray != null) {
                typedArray.recycle();
            }
        }
    }

    public void setText(String allString, String highlightString) {
        SpannableStringBuilder spannableStringBuilder = new SpannableStringBuilder();
        spannableStringBuilder.append(allString);
        spannableStringBuilder.setSpan(new AbsoluteSizeSpan((int) normalTextSize, true), 0, allString.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        spannableStringBuilder.setSpan(new ForegroundColorSpan(normalTextColor), 0, allString.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        List<Integer> list = getSubIndex(0, allString, highlightString);
        for (int i = 0; i < list.size(); i++) {
            int index = list.get(i);
            spannableStringBuilder.setSpan(new StyleSpan(Typeface.BOLD), index, index + highlightString.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            spannableStringBuilder.setSpan(new AbsoluteSizeSpan((int) highlightTextSize, true), index, index + highlightString.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            spannableStringBuilder.setSpan(new ForegroundColorSpan(highlightTextColor), index, index + highlightString.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        }
        setMovementMethod(LinkMovementMethod.getInstance());
        setText(spannableStringBuilder);
    }

    private List<Integer> getSubIndex(int startIndex, String allString, String highlightString) {
        List<Integer> list = new ArrayList<Integer>();
        int index = -1;
        if (startIndex == 0) {
            index = allString.indexOf(highlightString);
        } else {
            index = allString.indexOf(highlightString, startIndex);
        }
        if (index != -1) {
            list.add(index);
            list.addAll(getSubIndex(index + highlightString.length(), allString, highlightString));
        }
        return list;
    }

}

`````

##### 定义上面类中用到的attrs.xml

`````
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="CTextView">
        <attr name="normal_textsize" format="dimension"/>
        <attr name="highlight_textsize" format="dimension"/>
        <attr name="normal_textcolor" format="color"/>
        <attr name="highlight_textcolor" format="color"/>
    </declare-styleable>
</resources>
`````

##### 在xml中的TextView的用法

`````
<LinearLayout 
	xmlns:android="http://schemas.android.com/apk/res/android"
	xmlns:app="http://schemas.android.com/apk/res-auto"
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:orientation="vertical">
	<!-- 此处是包名+类名，请写自己的包名 -->
<com.easemob.ctextview.CTextView
    android:id="@+id/c_textview"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:normal_textsize="12sp"
    app:highlight_textsize="20sp"
    app:normal_textcolor="#000"
    app:highlight_textcolor="#f00" />
</LinearLayout>
`````

##### 在Activity中的用法

`````
CTextView cTextView = (CTextView) findViewById(R.id.c_textview);
cTextView.setText("欢迎大家看我说了这么久，大家辛苦了！","大家");
`````

##### 结果如下：
![result ctextview](http://7xpj58.com1.z0.glb.clouddn.com/img_20160310202020.png)



