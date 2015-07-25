# 第一梯次 Day 1 (7/17, 7/19, 2015)


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


# 參考資料

1. Hacker's Delight  

2. [http://www.hackersdelight.org/corres.txt](http://www.hackersdelight.org/corres.txt)

3. [FreeBSD 的 strlen(3)](https://blog.delphij.net/2012/04/freebsd-strlen3.html)

4. Bug 60538  - [SH] improve support for cmp/str insn 

5. CMSC 311 Answers-Draft 