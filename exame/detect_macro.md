# 請問以下的 DETECT 巨集用途是什麼 ?

此題主要測試對位元運算的理解，那到底以下的 `DETECT` 巨集具有怎樣的用途呢?

```c
    #if LONG_MAX == 2147483647L
    #define DETECT(X) (((X) - 0x01010101) & ~(X) & 0x80808080)
    #else
    #if LONG_MAX == 9223372036854775807L
    #define DETECT(X) (((X) - 0x0101010101010101) & ~(X) & 0x8080808080808080)
    #else
    #error long int is not a 32bit or 64bit type.
    #endif
    #endif
```


## 解答

這個巨集上是用來測試輸入內容是否為0，此巨集很常使用在`strlen`或是`strcap`等字串處理的函式上，我們可以從Apple的[strlen](http://opensource.apple.com/source/Libc/Libc-583/arm/string/strlen.s)或是其他libc中看到這巨集的影子。

```armasm
Laligned_loop:
    /* ((x - 0x01010101) & ~x & 0x80808080) == hasnull(word) */
    sub     r3, r2, r1      /* x - 0x01010101 */
    bic     r3, r3, r2      /* above & ~x */
    tst     r3, r1, lsl #7  /* above & 0x80808080 */
    ldreq   r2, [r0], #4    /* load next word */
    beq     Laligned_loop

```

所以到底為什麼這個巨集可以用來偵測`Null char`，使用他到底能夠為`strlen`等函式增加怎樣的優點？我們可以自己用C語言來試著去實作`strlen`這個函式看看，於是我們很快的完成了這個函式：

```c
unsigned int strlen (const char *s)
{
        char *p = s;

        while (*p != '\0') {
                p++;
        }

        return (p - s);
}
```

這樣的實作到底有什麼問題呢？首先注意到我們每一次只能檢查`一個byte`大小的資訊，想想看在32-bit系統上，CPU一次是處理4-byte大小的資訊，不覺得有點浪費嗎?(用在64-bit上面問題就更嚴重了）



## 參考資料

1. Hacker's Delight

2. [http://www.hackersdelight.org/corres.txt](http://www.hackersdelight.org/corres.txt)

3. [FreeBSD 的 strlen(3)](https://blog.delphij.net/2012/04/freebsd-strlen3.html)

4. Bug 60538  - [SH] improve support for cmp/str insn

5. CMSC 311 Answers-Draft
