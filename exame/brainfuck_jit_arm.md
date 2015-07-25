# 請實現ARM版本的Brainf*ck JIT compiler

此題為課堂作業，題目是根據 [jserv/jit-construct](https://github.com/jserv/jit-construct.git) 以及[Interpreter, Compiler, JIT from scratch](http://wiki.csie.ncku.edu.tw/embedded/summer2015/jit-compiler.pdf) 這份投影片來實作ARM版本的Brainf*ck JIT compiler，由於最新的commit已經把ARM版本的加進去了，我們可以使用以下命令來切換到commit `0d9eb7a` 這一版本去實現我們自己的ARM版的Brainf*ck JIT compiler。


```
    git clone https://github.com/jserv/jit-construct.git
    cd jit-construct
    git checkout -b homework 0d9eb7a
```

## 實現 jit0-arm.c

為了可以更加理解所謂的 Self-Modifying code 的運作，我們先嘗試去實現ARM版本的
jit0-arm，那我們要怎樣去取得ARM組合語言的十六進制表示法呢？我們可以先寫一個簡單
的C程式，命名為 simple.c

```c
    #include <stdio.h>

    int main(int argc, char *argv[])
    {

            return 0;
    }
```

接著使用 `arm-linux-gnueabihf -O2 simple.c -o simple` 來進行編譯，編譯完成後我們
就可以使用 `objdump` 去取得我們要用的十六進制命令，節錄如下

```
$ arm-linux-gnueabihf-objdump -D simple | less

    Disassembly of section .text:

    000082e0 <main>:
    82e0:        e3a00000         mov       r0, #0
    82e4:        e12fff1e         bx        lr
```

於是我們就可以完成我們的雛形，注意到ARM預設是little-endian 的，因此我們最後的程式應該要寫成這樣:

```c
    int main(int argc, char *argv[]) {
      char code[] = {
        0x00, 0x00, 0xa0, 0xe3, // 0xe3a00000
        0x1e, 0xff, 0x2f, 0xe1  // 0xe12fff1e
      };

      // skip ....
      memcpy(&code[0], &num, 2);
      // skip ....

      return func();
    }
```

## 參考資料

1. [ARM Connected Community - Caches and Self-Modifying Code](http://community.arm.com/groups/processors/blog/2010/02/17/caches-and-self-modifying-code)
