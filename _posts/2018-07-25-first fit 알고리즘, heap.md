---
layout: post
title: first fit 알고리즘, malloc
---





malloc 과 free를 잘 알아보기 위해 코드를 짜봤다.






>heap.c

  
```c
#include <stdio.h>

  

int main(){

char *a;

char *b;

char *c;

char *d;

char *e;

  

a = malloc(20);

b = malloc(20);

c = malloc(20);

  

printf("%p\n", a);

printf("%p\n", b);

printf("%p\n", c);

  

free(a);

free(b);

  

d = malloc(20);

e = malloc(20);

  

printf("%p\n", d);

printf("%p\n", e);

return 0;

}
```
  

a, b, c를 할당해준다.

  

그러면 a | b | c 이렇게 쌓이겠다.

  

그런다음 a와 b를 free 한 다음

  

d와 e를 malloc해주면

  

e | d | c

  

first fit 알고리즘에 의해 이렇게 들어간다. d가 제일 낮은 주소에 들어가는 것이 아닌 c 바로 전에 들어간다.

  

실행 결과이다.

  

hyomin@ubuntu:~/baobob/heap$ ./heap

0x672010

0x672030

0x672050

0x672030

0x672010
