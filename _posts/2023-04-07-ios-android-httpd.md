---
layout: post
title:  "Android 显示本网页"
date:   2023-04-07 02:00:00 +0800
categories: Android
---

## 需求

客户想要在 iOS/Android App 中添加客服，他们使用的是 `https://assets.salesmartly.com`。

## Android 实现

先问一下 ChatGPT。

To display a local index.html file with JavaScript in an Android app, you can use a WebView component. Here's an example code that loads a local index.html file and enables JavaScript execution:

Create an assets folder in your app's src/main/ directory if it doesn't already exist. Put your index.html and any other necessary files in this folder.
In your activity or fragment, add a WebView component to the layout XML file:

```xml
<WebView
    android:id="@+id/webView"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

In your activity or fragment, load the index.html file into the WebView and enable JavaScript execution:


### Create Assets Folder

You can create an assets folder in Android Studio using the following steps:

1. In the Project view, navigate to the app module of your project.
1. Right-click on the app module and select `New > Folder > Assets Folder`.
1. In the New Folder dialog, make sure that `src/main` is selected as the parent directory.
1. Enter assets as the directory name and click Finish.
1. The assets folder will now appear in the Project view. You can add your index.html file and any other necessary files to this folder.

### Load html

```java
WebView webView = findViewById(R.id.webView);
WebSettings webSettings = webView.getSettings();
webSettings.setJavaScriptEnabled(true);

webView.loadUrl("file:///android_asset/index.html");
```

In this code, findViewById is used to find the WebView component in the layout XML file, and getSettings is used to get the WebSettings object for the WebView. The setJavaScriptEnabled method is called on the WebSettings object to enable JavaScript execution in the WebView.

Finally, the loadUrl method is called on the WebView object to load the index.html file from the assets folder using the file:///android_asset URI scheme.

### INTERNET

Note that you may also need to add the INTERNET permission to your app's manifest file if you are using network resources in your index.html file:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Also, make sure that the file name and path used in the loadUrl method match the actual name and path of the index.html file in your assets folder.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Salesmartly</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>
<main>
    <h1>Hello World!</h1>
    <script src="https://assets.salesmartly.com/js/project_13785_14282_1680233133.js"></script>
</main>
</body>
</html>
```

### https

![https](/assets/images/webview/https.png)

In Android, loading HTTP URLs by default is not allowed for security reasons. However, if you still need to load HTTP URLs, you can use the following workaround:

Create an XML resource file named network_security_config.xml in the res/xml folder of your app with the following contents:

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
```

This configuration file allows cleartext traffic, i.e., HTTP URLs, to be loaded in your app.

In your app's manifest file, add the following lines inside the application tag:

```xml
<application
    ...
    android:networkSecurityConfig="@xml/network_security_config">
    ...
</application>
```

只显示 Hello World!，没有显示客服服务。

添加打印。

```java
webView.setWebChromeClient(new WebChromeClient() {
    @Override
    public boolean onConsoleMessage(ConsoleMessage consoleMessage) {
        Log.d("WebView", consoleMessage.message());
        return true;
    }
});
```

看一下报错信息：

```
D/NetworkSecurityConfig: Using Network Security Config from resource network_security_config debugBuild: true
D/WebView: Failed to execute 'postMessage' on 'DOMWindow': The target origin provided ('file://') does not match the recipient window's origin ('null').
I/chatty: uid=10229(com.iosdevlog.myapplication) identical 2 lines
D/WebView: Failed to execute 'postMessage' on 'DOMWindow': The target origin provided ('file://') does not match the recipient window's origin ('null').
I/hwschromium-3360: [INFO:network_service_network_delegate.cc(246)] final url https//*** error_code -3 ip address: ignored
D/WebView: Failed to execute 'postMessage' on 'DOMWindow': The target origin provided ('file://') does not match the recipient window's origin ('null').
```

看来直接显示本地 html 不行，那就创建一个 HttpServer 吧。

如果是 Python3，可以用下面的方式：

```bash
python3 -m http.server
```

访问 `http://localhost:8080`。

找一下 Android 类似的包：[nanohttpd](https://github.com/NanoHttpd/nanohttpd)，2019 年后就不更新了。

不过没有关系，我们的要求比较简单。

添加依赖：

```
implementation 'org.nanohttpd:nanohttpd:2.3.1'
```

创建 `WebServer` 继承 `NanoHTTPD`：

```java
import android.content.Context;

import java.io.IOException;
import java.io.InputStream;

import fi.iki.elonen.NanoHTTPD;

public class WebServer extends NanoHTTPD {
    private Context context;

    public WebServer() throws IOException {
        super(8080);
        start(NanoHTTPD.SOCKET_READ_TIMEOUT, false);
    }

    @Override
    public Response serve(IHTTPSession session) {
        if (session.getUri().equals("/index.html")) {
            try {
                InputStream inputStream = getContext().getAssets().open("index.html");
                byte[] buffer = new byte[inputStream.available()];
                inputStream.read(buffer);
                inputStream.close();
                return newFixedLengthResponse(new String(buffer));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return super.serve(session);
    }

    public Context getContext() {
        return context;
    }

    public void setContext(Context context) {
        this.context = context;
    }
}
```

使用 WebServer：

```java
WebServer webServer;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    try {
        webServer = new WebServer();
        webServer.setContext(this);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
    webView.loadUrl("http://localhost:8080/index.html");
}
```

查看报错信息：

```
D/WebView: TypeError: Cannot read property 'getItem' of null
I/chatty: uid=10229(com.iosdevlog.myapplication) identical 1 line
D/WebView: TypeError: Cannot read property 'getItem' of null
D/WebView: TypeError: a.getLang is not a function
D/WebView: Uncaught TypeError: Cannot read property 'getItem' of null
```

原因是 DOM 储存 API 没有打开。

```java
webSettings.setDomStorageEnabled(true);
```

![smartly](/assets/images/webview/smartly.png)

## iOS

### GCDWebServer 

<https://github.com/swisspol/GCDWebServer>

```objective-c
#import "GCDWebServer.h"
#import "GCDWebServerDataResponse.h"

@interface AppDelegate : NSObject <UIApplicationDelegate> {
  GCDWebServer* _webServer;
}
@end

@implementation AppDelegate

- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions {
  
  // Create server
  _webServer = [[GCDWebServer alloc] init];
  
  // Add a handler to respond to GET requests on any URL
  [_webServer addDefaultHandlerForMethod:@"GET"
                            requestClass:[GCDWebServerRequest class]
                            processBlock:^GCDWebServerResponse *(GCDWebServerRequest* request) {
    
    return [GCDWebServerDataResponse responseWithHTML:@"<html><body><p>Hello World</p></body></html>"];
    
  }];
  
  // Start server on port 8080
  [_webServer startWithPort:8080 bonjourName:nil];
  NSLog(@"Visit %@ in your web browser", _webServer.serverURL);
  
  return YES;
}

@end
```
