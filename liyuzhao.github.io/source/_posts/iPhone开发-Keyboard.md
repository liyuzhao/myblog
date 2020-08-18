---
title: iPhone开发-Keyboard
date: 2017-11-15 21:17:18
tags: [ios]
categories: ios
---

文本经常需要和键盘打交道，现在记录一下初步的键盘相关的知识点。
键盘出现和消失促发两个消息。

UIKeyboardWillShowNotification （出现通知）
UIKeyboardWillHideNotification （隐藏通知）

NSNotificationCenter 监听两个消息即可捕捉到键盘弹出和隐藏的两个消息。

<!-- more -->

### 键盘类型

键盘类型系统提供了几种类型，文本里面只需要设置一下相应的的类型就可以呈现出对应的键盘种类

```
textField.keyboardType  = UIKeyboardTypeNumberPad;（数字类型）理财方面的产品里面可以见到这样的设置。

```

### 类型枚举
```
typedef NS_ENUM(NSInteger, UIKeyboardType) {
    UIKeyboardTypeDefault,                // Default type for the current input method.
    UIKeyboardTypeASCIICapable,           // Displays a keyboard which can enter ASCII characters, non-ASCII keyboards remain active
    UIKeyboardTypeNumbersAndPunctuation,  // Numbers and assorted punctuation.
    UIKeyboardTypeURL,                    // A type optimized for URL entry (shows . / .com prominently).
    UIKeyboardTypeNumberPad,              // A number pad (0-9). Suitable for PIN entry.
    UIKeyboardTypePhonePad,               // A phone pad (1-9, *, 0, #, with letters under the numbers).
    UIKeyboardTypeNamePhonePad,           // A type optimized for entering a person's name or phone number.
    UIKeyboardTypeEmailAddress,           // A type optimized for multiple email address entry (shows space @ . prominently).
    UIKeyboardTypeDecimalPad NS_ENUM_AVAILABLE_IOS(4_1),   // A number pad with a decimal point.
    UIKeyboardTypeTwitter NS_ENUM_AVAILABLE_IOS(5_0),      // A type optimized for twitter text entry (easy access to @ #)
    UIKeyboardTypeWebSearch NS_ENUM_AVAILABLE_IOS(7_0),    // A default keyboard type with URL-oriented addition (shows space . prominently).

    UIKeyboardTypeAlphabet = UIKeyboardTypeASCIICapable, // Deprecated

};
```

```
[[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(keyboardDisappear:) name:UIKeyboardWillHideNotification object:nil];

    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(keyboardAppear:) name:UIKeyboardWillShowNotification object:nil];

    UITextField *textField  = [[UITextField alloc] init];
    [self.view addSubview:textField];
    textField.frame  = CGRectMake(0, 160, 200, 28);

    textField.text  = @"我们来了";
    textField.backgroundColor = [UIColor greenColor];
    textField.keyboardType  = UIKeyboardTypeNumberPad;

}

- (void) keyboardDisappear:(NSNotification *) notification
{

    NSLog(@"键盘隐藏消息");

}
- (void) keyboardAppear:(NSNotification *) notification
{

    NSLog(@"键盘出现消息");

}

[[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(keyboardDisappear:) name:UIKeyboardWillHideNotification object:nil];

    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(keyboardAppear:) name:UIKeyboardWillShowNotification object:nil];

    UITextField *textField  = [[UITextField alloc] init];
    [self.view addSubview:textField];
    textField.frame  = CGRectMake(0, 160, 200, 28);

    textField.text  = @"我们来了";
    textField.backgroundColor = [UIColor greenColor];
    textField.keyboardType  = UIKeyboardTypeNumberPad;

}

- (void) keyboardDisappear:(NSNotification *) notification
{

    NSLog(@"键盘隐藏消息");

}
- (void) keyboardAppear:(NSNotification *) notification
{

    NSLog(@"键盘出现消息");

}
```
