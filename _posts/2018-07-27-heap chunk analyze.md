---
layout: post
title: heap chunk analyze
---
  
보다 힙을 잘 이해하기 위해서 직접 코드를 작성하여 malloc과 free과정에서의 heap chunk의

구조와 변화하는 과정을 분석하였다.

gdb를 이용해서 분석해보도록 하자. 나는 gdb의 상위 버전인 gef를 이용했다.

그리고 64bit 기준으로 컴파일/분석하였다. 32비트는 직접해보길 바란다.

먼저 내가 작성한 코드이다.

>heapstruct.c

```c
#include  <stdio.h>

#include  <stdlib.h>

#include  <string.h>

int main(int argc, char  *argv[]){

char* buf = (char*)malloc(256);

char* buf1 = (char*)malloc(512);

strcpy(buf, argv[1]);

strcpy(buf1, argv[2]);

printf("%s\n", buf);

free(buf);

free(buf1);

}
```

매우 간단한 코드이다.

1.  buf를 256만큼 malloc해준다. 그리고 buf1은 512만큼 malloc해준다.
    
2.  argv[1] 과 argv[2]를 각각 chunk에 strcpy를 해준다.
    
3.  buf 와 buf1 차례대로 free해준다.
    

그러면 이제 분석을 해보자.

디스어셈블 한 코드이다.

![](https://lh5.googleusercontent.com/m-tqzAH_vDIl1WLAk2ozWvSOQYA75SbPZvgsYQvQJsjDK2tWEWMmp806gHqnLdrFUc-qw8B_BnmPbKTHoFJQnsrbKEZDn-I97tKg6MYnbJJjUQwaYWLeD5UUHvXPuTfTyQ16ANPu)

첫 malloc때 0x100 , 256만큼 잘 들어가는 것을 볼 수 있다.

먼저 처음 malloc 하고 난 다음인 main+25에 breakpoint를 걸어보겠다.

![](https://lh5.googleusercontent.com/2nwfHq5abS487wwl9BDm7Lq9de3rO3SUWMGosv0JVMDLTQt7Bz132cvek-_1Hcgj15uTCksRl8grqhfKj5gLbj3AjNAppAt36uJMF8V8R6fb7TkivbtW8RDS8KSsxxFWfgoabbGP)

r로 실행한다.

그리고 힙 상태를 봐보겠다. $rax - 16하면 확인 할 수 있다. 32비트에선 $eax-8 이다.

또는 0x602000을 확인 해도 된다.

![](https://lh3.googleusercontent.com/1H8WOKc62v7xlxPtWNgs4Q_mJfbUbyPrBax-6Yj6kqCYCBCXhiMKRvV1n2BZqR9CEjRIA-Nj4OtTUamkBz--cf1Z_-_2S0sClhW4Y61Rqr9-5K-Azx_x2W0qGH59PKGHcZ_Sxmg-)

힙이 잘 할당 되었다. 64비트이기 때문에 g를 이용하여 8바이트씩 출력하였다. 이제 힙 구조를 확인해 보겠다.

그 전에, 알아두어야 할 것이있다. heap chunk의 구조는 할당되었을 때와 free되었을 때와 구조가 다르다.


struct malloc_chunk {

INTERNAL_SIZE_T mchunk_prev_size; /* Size of previous chunk (if free). */

INTERNAL_SIZE_T mchunk_size; /* Size in bytes, including overhead. */

struct malloc_chunk* fd; /* double links -- used only if free. */

struct malloc_chunk* bk;

/* Only used for large blocks: pointer to next larger size. */

struct malloc_chunk* fd_nextsize; /* double links -- used only if free. */

struct malloc_chunk* bk_nextsize;

};

typedef  struct malloc_chunk* mchunkptr;


이는 malloc_chunk의 구조체이다.

할당된 malloc chunk의 구조는

chunk-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| Size of previous chunk, if unallocated (P clear) |  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| Size of chunk, in bytes |A|M|P|  
mem-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| User data starts here... .  
. .  
. (malloc_usable_size() bytes) .  
. |  
nextchunk-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| (size of chunk, but used for application data) |  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| Size of next chunk, in bytes |A|0|1|  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

이렇게 되고

free된 malloc chunk의 구조는

chunk-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| Size of previous chunk, if unallocated (P clear) |  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
`head:' | Size of chunk, in bytes |A|0|P|  
mem-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| Forward pointer to next chunk in list |  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| Back pointer to previous chunk in list |  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| Unused space (may be 0 bytes long) .  
. .  
. |  
nextchunk-> +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
`foot:' | Size of chunk, in bytes |  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+  
| Size of next chunk, in bytes |A|0|0|  
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

이렇게 된다. 분석을 하면서 비교해보자.

자세한 내용은

[https://heap-exploitation.dhavalkapil.com/diving_into_glibc_heap/malloc_chunk.html](https://heap-exploitation.dhavalkapil.com/diving_into_glibc_heap/malloc_chunk.html)

이 글을 참고하면 좋다.

다시 돌아와서,

![](https://lh3.googleusercontent.com/1H8WOKc62v7xlxPtWNgs4Q_mJfbUbyPrBax-6Yj6kqCYCBCXhiMKRvV1n2BZqR9CEjRIA-Nj4OtTUamkBz--cf1Z_-_2S0sClhW4Y61Rqr9-5K-Azx_x2W0qGH59PKGHcZ_Sxmg-)

저 위에 영어로 쓰여진 구조와 비교해보자.

0x602000 -> prev_size

prev_size는 이전 chunk가 free 되면 설정되는 값으로, 플래그를 제외한 이전 청크의 크기를 기록한다. 이를 이용해서 이전 chunk의 위치를 쉽게 알 수 있다.

다음 8바이트를 보면,

0x602008 -> size of chunk

이 8바이트에서는 이 chunk의 총 크기를 기록한다. 하위 1비트는 플래그로, prev_inuse이다.

prev_inuse는 이전 chunk가 사용중일 때 기록되는 플래그이다.

그렇다면 이 chunk의 크기는 0x110일 것이다.

![](https://lh4.googleusercontent.com/JXaEMxkBKlS-rY__WGSvqi8xS-GbgZtic4SLg1L9ijDaGmyuLGBxbJIjFRK_GshZkU3MHmMM0gjCK34U0IWAv4wL-OYfV36IKAhfO2ueWRrK_ZsCy2tbUQy89SwRuHPtaDEyoP4D)

272이다.

data(256) + prev_size(8) + size_of_chunk(8) = 272 딱 맞다.

0x602010 -> data

여기서 부터 256바이트 만큼은 data이다.

좀 이따 strcpy 이후에 값이 들어가는 것을 확인해보겠다.

0x602118 -> Top chunk

![](https://lh6.googleusercontent.com/jrOsDkYVyLHixj4pd5qAeOcl9KmXQBj1nNj6WBMArv9lv9vbb6i07caDFNQPu64PM-V3iv4yDaLUAznFskqi4iT50EIF75QBKuBMeGSxaKs4t2Y9soY4LpQ2wC6KLflm5VkYJ8cM)

0x20ef1이라는 값이 들어가 있다.

모든 chunk의 맨 마지막에는 Top chunk가 존재한다. Top chunk는 어떠한 bin에도 속하지 않으며 항상 heap영역 마지막에 위치한다. 다음 malloc할 때 다음 chunk size를 top chunk에서 할당하고 나머지는 다시 top chunk가 된다.

잘 이해 안가겠지만 계속 분석하면서 보겠다.

다음 malloc 인 main+39에 breakpoint를 걸었다.

![](https://lh6.googleusercontent.com/sBJKVoRHr-mLM0uSWdvhEZKnIpAJlQ_o3lIpuLzHx7amaXlX120dG45MBhONzz1iOqkqZ25QB0lzHE8OMjXmAXVxHY6gPBwv1DcXaEy2fGXDMRWRdKCPtmW6pdzmI5RJqHpbnL_c)

힙 영역 상황이다.

![](https://lh5.googleusercontent.com/LdPLyVEOIkp48MpeeyNxiFNUg7OuA7WiDcZsGnxbDdDrcnEk3ShEkfwi2XxXPrh69Hjtd1-FcImcGVHeKS4aWzYvAjKSFMmy8144Pt9nUZmeQmP_rttYTda8DxlUU_cp_aNdnIf6)

매우 신기하다.

아까 Top chunk 였던 0x602118부분이, 2번째 malloc_chunk의 size of chunk로 바뀌였다.

그리고 제일 마지막, 0x602328에 Top chunk가 있는 것을 확인 할 수 있다.

Top chunk를 비교해보자 아까 첫 번째 malloc chunk에 있던 Top chunk 의 값은 0x20ef1

이고 2번째 malloc을 진행 한 뒤 Top chunk 의 값은 0x20ce1이다.

일단 2번째 malloc의 size of chunk 는 0x211이다. 하위 1비트는 flag이니깐 제외하면 0x210, 10진수로 528이다.

data(512) + prev_size(8) + size_of_chunk(8) = 528이 맞다.

그럼 Top chunk의 변화를 보면 0x20ef1 -> 0x20ce1

![](https://lh4.googleusercontent.com/aUpAyMarMNpvy4VRnLFDf90NR5MFGMetyWjKS1MmLJTUuhSqucXGERiqFRG7dK_nn-by0SKdR3A-yx32VQOa8W9qPgGMf79BoDLI3G0ri85QQp5s0CST1m0UOMDspGlcp7OtnUnx)

계산이 딱 맞다.

Top chunk에서 새로 malloc할 size만큼을 Top chunk에서 할당 한 뒤, 나머지는 다시 Top chunk가 된다.

(암튼 신기)

이제 할당 된 malloc 구조는 확인을 했다.

free chunk를 보기 전에 argv[1] 과 argv[2] 에 값을 넣고 data가 들어가는 것을 확인 해보겠다.

strcpy가 끝난 후인

![](https://lh5.googleusercontent.com/V5jqvq1MNbNFNwbvMjYKNUHo8zS3mTrmszbfR_CGpNnPbqyD_9PGfiLxB3O2GxqKtrEc4laCQuYULCUTe4ny6b_0c9nelrJySFpOtgHNtN5nKn0BWg3W8YNFFCdpEYHkewoZGv9d)

에 breakpoint를 걸어주었다.

다시 r을 통해 실행하고 strcpy가 될 때까지 진행해보겠다.

![](https://lh3.googleusercontent.com/YlWlRFN-feIe0tvxQdZC8je59nyAU4JxYaJU1bSIczTM76ofmtwX3k_VPS2quxXruQS4lS_s_OEBhzOywGwPPx4vhFAPuDWWU-3nM8lNUhZfJ5s-4yUiLbcS-lpveR_PA472ksW-)

이렇게 인자를 주었다.

힙 영역을 보겠다.

![](https://lh4.googleusercontent.com/QOTgYGdQLGwzK2ZhUTN2bN0yU3-dqBqc8gdjybq5IZ-xzz_Tof6L5DzLg5S5rXDgw8lqtHX_bcEl_Cfh0iGFlPUNXM_tLzEnb6OBCx5D4Iks18HFdRreOiSbov8kugiiaGxkA2Is)

잘 들어갔다.

이제 free chunk를 분석해보겠다.

브포를 걸고, 힙 영역을 보자.

![](https://lh4.googleusercontent.com/7Z3nQrd18v5-AivCpMggFhwtNoMpE_pqeQhfVbySoQ4o-xwRM9EGHRpL5wHkYyR8JB3IyzmWX9Zo2bI1pn-asBZU9RgTgjGdR-09gCqmMn1ke1R9UF-ZnZA-U2FiAEV22UvujpG6)

뭔가 추가 됐다.

아까 free chunk의 구조를 나타낸 표와 비교해보면 fd 와 bk가 free된 chunk에 있는 것을 확인 할 수 있다.

0x602010 -> fd - forward pointer

0x602018 -> bk - backward pointer

각각은 nextchunk를 가리키는 pointer, prevchunk를 가리키는 pointer이다.

free가 한 번 되고 나서는

![](https://lh3.googleusercontent.com/j8Z0oZfVV4B_nysp-5bOpDiCofEwXebQzgKWPkyOUxkJVV7S_Ilsmbsip4mnZnY5Ho7r_3L2Y2sLOhTdZdDE7EE8JSbZ3gj4jczyVUzcx37XPwBsIAK7oHzttuxllKe-FcfBHgSZ)

pointer가 Top chunk 전을 가리키고 있다.

두 번째 free이후 힙 영역을 확인 해보자.

![](https://lh5.googleusercontent.com/AZUL2F1It8hRNIt5w57spakaShr7L6kCfMIu7eFZQx6kjgxukoAUs6yFKtrZLPJgVGtjn4wkS09Yvkc_g_8sHXnOX3ZaMRumlJLHkNZznru4hKyl1zhOagAaC2PqDL_wuvwfdMFY)

size of chunk 가 0x320 이다 0x110 + 0x210 의 값으로 변경되었다.

후.. 프로그램은 이제 종료가 된다.

지금까지 gdb로 malloc과 free chunk의 구조를 분석하였다.

느낀건, 100번 문서봐도 직접 한 번 해보는게 훨씬 이해가 잘 되고 얻는 것도 많다.
