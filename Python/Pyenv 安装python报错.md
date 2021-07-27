---
title: Pyenv 安装python报错
tags: []
---

```
/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/mach-o/dyld.h:98:54: note: passing argument to parameter 'bufsize' here
extern int _NSGetExecutablePath(char* buf, uint32_t* bufsize)                 __OSX_AVAILABLE_STARTING(__MAC_10_2, __IPHONE_2_0);
                                                     ^
clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include   -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include   -std=c99 -Wextra -Wno-unused-result -Wno-unused-parameter -Wno-missing-field-initializers -Wstrict-prototypes -Werror=implicit-function-declaration  -I./Include/internal  -I. -I./Include -I/usr/local/opt/readline/include -I/usr/local/opt/readline/include -I/Users/life/.pyenv/versions/3.8.2/include  -I/usr/local/opt/readline/include -I/usr/local/opt/readline/include -I/Users/life/.pyenv/versions/3.8.2/include   -DPy_BUILD_CORE_BUILTIN  -DPy_BUILD_CORE_BUILTIN -I./Include/internal -c ./Modules/posixmodule.c -o Modules/posixmodule.o
./Modules/posixmodule.c:9197:15: error: implicit declaration of function 'sendfile' is invalid in C99 [-Werror,-Wimplicit-function-declaration]
        ret = sendfile(in, out, offset, &sbytes, &sf, flags);
              ^
1 error generated.
make: *** [Modules/posixmodule.o] Error 1
make: *** Waiting for unfinished jobs....
1 warning generated.
```

如果出现以上错误，请把 ~/.zshrc or ~/.bash_profile 中 

```
if command -v pyenv 1>/dev/null 2>&1; then
   eval "$(pyenv init -)"
fi
```
改成
```
eval "$(pyenv init -)"
```

本人亲测可以，至于原因暂时还未找到,`if command -v pyenv 1>/dev/null 2>&1; then  eval "$(pyenv init -)"\n ` 是官方推荐的，但是我按照官方文档设置后 使用pyenv安装就会报错，然后改成以上示例解决了。

[官方文档](https://github.com/pyenv/pyenv#homebrew-on-macos)
