  
malloc �� free�� �� �˾ƺ��� ���� �ڵ带 ¥�ô�.

>>heap.c

  
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
  

a, b, c�� �Ҵ����ش�.

  

�׷��� a | b | c �̷��� ���̰ڴ�.

  

�׷����� a�� b�� free �� ����

  

d�� e�� malloc���ָ�

  

e | d | c

  

first fit �˰��� ���� �̷��� ����. d�� ���� ���� �ּҿ� ���� ���� �ƴ� c �ٷ� ���� ����.

  

���� ����̴�.

  

hyomin@ubuntu:~/baobob/heap$ ./heap

0x672010

0x672030

0x672050

0x672030

0x672010