---
layout: post
title:  "C to C#"
date:   2023-03-09 17:00:00 +0800
categories: c c# chatgpt
---

## 需求

解密提供的 C 语言 Source Code，界面是使用 C# 开发的。

现在要显示解密的进度，必须能提供 C 报告进度。

1. 共享信息存以文件里，有点慢
2. Socket 编程，比较麻烦
3. 把 C 封装成 dll，也要提供 IPC 通信
4. 重写 C 语言或者 C#，统一语言

## C++ to C# Converter

C++ to C# Converter produces great C# code, saving you hours of painstaking work and valuable time.

<https://www.tangiblesoftwaresolutions.com/>

语言转换的工具，这个是收费软件，免费版本一次 100 行。

![cplus-to-csharp-arrays](/assets/images/cpp2csharp/cplus-to-csharp-arrays.png)

转换的效果不太好。

## ChatGPT

问一下 ChatGPT，它应该可以实现。

> Rewrite below in C#:
> ```
> C Code
> ```

![ChatGPT-translate-code](/assets/images/cpp2csharp/chatgpt-translate-code.png)

Awesome，它翻译得很好，我修改后，就可以正常解码。

明天再把进度百分比的功能加上，就可以收工了。
