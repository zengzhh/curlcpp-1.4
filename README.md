### 一、编译静态库

1.拉取代码
```sh
git clone -b 1.4 https://github.com/zengzhihua24/curlcpp.git curlcpp-1.4
```

2.检查libcurl
```
libcurl-vc16-x86-release-static-ipv6-sspi-schannel\
  include\
  lib\
```

3.运行cmake.bat生成vs工程
```bash
mkdir build-vs2019-x86 && cd build-vs2019-x86 && cmake -G "Visual Studio 16 2019" -A Win32 .. -DCMAKE_INSTALL_PREFIX=.\cmake_install_prefix -DCURL_INCLUDE_DIR=..\libcurl-vc16-x86-release-static-ipv6-sspi-schannel\include -DCURL_LIBRARY=libcurl_a.lib
```

4.打开curlcpp.sln，配置curlcpp工程
```
预处理器定义：CURL_STATICLIB
附加库目录：$(SolutionDir)..\libcurl-vc16-x86-release-static-ipv6-sspi-schannel\lib
附加依赖项：
libcurl_a.lib
Ws2_32.lib
Wldap32.lib
winmm.lib
Crypt32.lib
Normaliz.lib
```

5.右键curlcpp工程生成curlcpp.lib

<br>

### 二、使用curlcpp

1.首先编译生成curlapp.lib

https://github.com/zengzhihua24/curlcpp/tree/1.4


2.引入curlcpp和curl的头文件、curlcpp.lib  
```
thirdparty
  include\
    curl\
      curl.h
      ...
    curlcpp\
      cookie.h
      ...
  Win32\
    lib\
      curlcpp.lib
```

3.配置工程
```
输出目录：$(ProjectDir)$(Configuration)\$(Platform)\
中间目录：$(OutDir)temp\
附加包含目录：$(SolutionDir)thirdparty\include
附加库目录：$(SolutionDir)thirdparty\$(Platform)\lib
附加依赖项：curlcpp.lib
```

4.编写运行代码
```cpp
#include "curlcpp\curl_easy.h"
using curl::curl_easy;
using curl::curl_easy_exception;
/**
* This example shows how to make a simple request with curl.
*/
int main() {
    // Easy object to handle the connection.
    curl_easy easy;
    // Add some options.
    easy.add<CURLOPT_URL>("www.baidu.com");
    easy.add<CURLOPT_FOLLOWLOCATION>(1L);
    try {
        // Execute the request.
        easy.perform();
    } catch (curl_easy_exception &error) {
        // If you want to get the entire error stack we can do:
        auto errors = error.what();
        // Otherwise we could print the stack like this:
        error.print_traceback();
    }
    return 0;
}
```
