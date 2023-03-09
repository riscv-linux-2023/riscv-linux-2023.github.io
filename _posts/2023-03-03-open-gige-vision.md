---
layout: post
title:  "Open GigE Vision"
date:   2023-03-03 11:06:00 +0800
categories: gvcp gige
---

## Download Open GigE Vision Source code

```
git clone https://gitorious.org/opengigevision/opengigevision.git
```

报错：

> Cloning into 'opengigevision'...
> fatal: unable to access 'https://gitorious.org/opengigevision/opengigevision.git/': server certificate verification failed. CAfile: none CRLfile: none
 
```
export GIT_SSL_NO_VERIFY=1
git clone https://gitorious.org/opengigevision/opengigevision.git
```

### Boost

```
sudo apt-get update
sudo apt-get install libboost-all-dev
```

### Build

```
mkdir build
cd build
cmake ..
cmake --build .
```

报错：

> src/Main.cpp:7:10: fatal error: vigra/stdimage.hxx: No such file or directory
>     7 | #include <vigra/stdimage.hxx>
>       |          ^~~~~~~~~~~~~~~~~~~~
> compilation terminated.
> make[2]: *** [CMakeFiles/camtest.dir/build.make:76: CMakeFiles/camtest.dir/src/Main.cpp.o] Error 1
> make[1]: *** [CMakeFiles/Makefile2:83: CMakeFiles/camtest.dir/all] Error 2
> make: *** [Makefile:91: all] Error 2

### vigra

```
wget -c https://github.com/ukoethe/vigra/releases/download/Version-1-11-1/vigra-1.11.1-src.tar.gz
tar xvf vigra-1.11.1-src.tar.gz
cd vigra-1.11.1/
mkdir build
cd build
cmake ..
make
make check
make doc
sudo make install
make examples
```

报错

> src/Gvsp.h:5:10: fatal error: boost/gil/gil_all.hpp: No such file or directory
>     5 | #include <boost/gil/gil_all.hpp>
>       |          ^~~~~~~~~~~~~~~~~~~~~~~
> compilation terminated.
> make[2]: *** [CMakeFiles/camtest.dir/build.make:76: CMakeFiles/camtest.dir/src/Main.cpp.o] Error 1
> make[1]: *** [CMakeFiles/Makefile2:83: CMakeFiles/camtest.dir/all] Error 2
> make: *** [Makefile:91: all] Error 2

```
diff --git a/src/Gvsp.h b/src/Gvsp.h
index 2112f4d..da17754 100644
--- a/src/Gvsp.h
+++ b/src/Gvsp.h
@@ -2,7 +2,8 @@
 #define GVSP_H

 #include <boost/asio.hpp>
-#include <boost/gil/gil_all.hpp>
+#include <boost/gil.hpp>
+#include <boost/array.hpp>
```

报错：

> src/Gvsp.cpp:4:10: fatal error: boost/gil/extension/io/png_io.hpp: No such file or directory
>     4 | #include <boost/gil/extension/io/png_io.hpp>
>       |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
> compilation terminated.

要使用 2009 年的 Boost 库，我在 VirtualBox 上面安装了 Ubuntu 09.04，又重复以上操作，还是无法编译。

放弃。。。
