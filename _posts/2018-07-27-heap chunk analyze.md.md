
  
���� ���� �� �����ϱ� ���ؼ� ���� �ڵ带 �ۼ��Ͽ� malloc�� free���������� heap chunk��

������ ��ȭ�ϴ� ������ �м��Ͽ���.

gdb�� �̿��ؼ� �м��غ����� ����. ���� gdb�� ���� ������ gef�� �̿��ߴ�.

�׸��� 64bit �������� ������/�м��Ͽ���. 32��Ʈ�� �����غ��� �ٶ���.

���� ���� �ۼ��� �ڵ��̴�.

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

�ſ� ������ �ڵ��̴�.

1.  buf�� 256��ŭ malloc���ش�. �׸��� buf1�� 512��ŭ malloc���ش�.
    
2.  argv[1] �� argv[2]�� ���� chunk�� strcpy�� ���ش�.
    
3.  buf �� buf1 ���ʴ�� free���ش�.
    

�׷��� ���� �м��� �غ���.

�𽺾���� �� �ڵ��̴�.

![](https://lh5.googleusercontent.com/m-tqzAH_vDIl1WLAk2ozWvSOQYA75SbPZvgsYQvQJsjDK2tWEWMmp806gHqnLdrFUc-qw8B_BnmPbKTHoFJQnsrbKEZDn-I97tKg6MYnbJJjUQwaYWLeD5UUHvXPuTfTyQ16ANPu)

ù malloc�� 0x100 , 256��ŭ �� ���� ���� �� �� �ִ�.

���� ó�� malloc �ϰ� �� ������ main+25�� breakpoint�� �ɾ�ڴ�.

![](https://lh5.googleusercontent.com/2nwfHq5abS487wwl9BDm7Lq9de3rO3SUWMGosv0JVMDLTQt7Bz132cvek-_1Hcgj15uTCksRl8grqhfKj5gLbj3AjNAppAt36uJMF8V8R6fb7TkivbtW8RDS8KSsxxFWfgoabbGP)

r�� �����Ѵ�.

�׸��� �� ���¸� �����ڴ�. $rax - 16�ϸ� Ȯ�� �� �� �ִ�. 32��Ʈ���� $eax-8 �̴�.

�Ǵ� 0x602000�� Ȯ�� �ص� �ȴ�.

![](https://lh3.googleusercontent.com/1H8WOKc62v7xlxPtWNgs4Q_mJfbUbyPrBax-6Yj6kqCYCBCXhiMKRvV1n2BZqR9CEjRIA-Nj4OtTUamkBz--cf1Z_-_2S0sClhW4Y61Rqr9-5K-Azx_x2W0qGH59PKGHcZ_Sxmg-)

���� �� �Ҵ� �Ǿ���. 64��Ʈ�̱� ������ g�� �̿��Ͽ� 8����Ʈ�� ����Ͽ���. ���� �� ������ Ȯ���� ���ڴ�.

�� ����, �˾Ƶξ�� �� �����ִ�. heap chunk�� ������ �Ҵ�Ǿ��� ���� free�Ǿ��� ���� ������ �ٸ���.


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


�̴� malloc_chunk�� ����ü�̴�.

�Ҵ�� malloc chunk�� ������

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

�̷��� �ǰ�

free�� malloc chunk�� ������

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

�̷��� �ȴ�. �м��� �ϸ鼭 ���غ���.

�ڼ��� ������

[https://heap-exploitation.dhavalkapil.com/diving_into_glibc_heap/malloc_chunk.html](https://heap-exploitation.dhavalkapil.com/diving_into_glibc_heap/malloc_chunk.html)

�� ���� �����ϸ� ����.

�ٽ� ���ƿͼ�,

![](https://lh3.googleusercontent.com/1H8WOKc62v7xlxPtWNgs4Q_mJfbUbyPrBax-6Yj6kqCYCBCXhiMKRvV1n2BZqR9CEjRIA-Nj4OtTUamkBz--cf1Z_-_2S0sClhW4Y61Rqr9-5K-Azx_x2W0qGH59PKGHcZ_Sxmg-)

�� ���� ����� ������ ������ ���غ���.

0x602000 -> prev_size

prev_size�� ���� chunk�� free �Ǹ� �����Ǵ� ������, �÷��׸� ������ ���� ûũ�� ũ�⸦ ����Ѵ�. �̸� �̿��ؼ� ���� chunk�� ��ġ�� ���� �� �� �ִ�.

���� 8����Ʈ�� ����,

0x602008 -> size of chunk

�� 8����Ʈ������ �� chunk�� �� ũ�⸦ ����Ѵ�. ���� 1��Ʈ�� �÷��׷�, prev_inuse�̴�.

prev_inuse�� ���� chunk�� ������� �� ��ϵǴ� �÷����̴�.

�׷��ٸ� �� chunk�� ũ��� 0x110�� ���̴�.

![](https://lh4.googleusercontent.com/JXaEMxkBKlS-rY__WGSvqi8xS-GbgZtic4SLg1L9ijDaGmyuLGBxbJIjFRK_GshZkU3MHmMM0gjCK34U0IWAv4wL-OYfV36IKAhfO2ueWRrK_ZsCy2tbUQy89SwRuHPtaDEyoP4D)

272�̴�.

data(256) + prev_size(8) + size_of_chunk(8) = 272 �� �´�.

0x602010 -> data

���⼭ ���� 256����Ʈ ��ŭ�� data�̴�.

�� �̵� strcpy ���Ŀ� ���� ���� ���� Ȯ���غ��ڴ�.

0x602118 -> Top chunk

![](https://lh6.googleusercontent.com/jrOsDkYVyLHixj4pd5qAeOcl9KmXQBj1nNj6WBMArv9lv9vbb6i07caDFNQPu64PM-V3iv4yDaLUAznFskqi4iT50EIF75QBKuBMeGSxaKs4t2Y9soY4LpQ2wC6KLflm5VkYJ8cM)

0x20ef1�̶�� ���� �� �ִ�.

��� chunk�� �� ���������� Top chunk�� �����Ѵ�. Top chunk�� ��� bin���� ������ ������ �׻� heap���� �������� ��ġ�Ѵ�. ���� malloc�� �� ���� chunk size�� top chunk���� �Ҵ��ϰ� �������� �ٽ� top chunk�� �ȴ�.

�� ���� �Ȱ������� ��� �м��ϸ鼭 ���ڴ�.

���� malloc �� main+39�� breakpoint�� �ɾ���.

![](https://lh6.googleusercontent.com/sBJKVoRHr-mLM0uSWdvhEZKnIpAJlQ_o3lIpuLzHx7amaXlX120dG45MBhONzz1iOqkqZ25QB0lzHE8OMjXmAXVxHY6gPBwv1DcXaEy2fGXDMRWRdKCPtmW6pdzmI5RJqHpbnL_c)

�� ���� ��Ȳ�̴�.

![](https://lh5.googleusercontent.com/LdPLyVEOIkp48MpeeyNxiFNUg7OuA7WiDcZsGnxbDdDrcnEk3ShEkfwi2XxXPrh69Hjtd1-FcImcGVHeKS4aWzYvAjKSFMmy8144Pt9nUZmeQmP_rttYTda8DxlUU_cp_aNdnIf6)

�ſ� �ű��ϴ�.

�Ʊ� Top chunk ���� 0x602118�κ���, 2��° malloc_chunk�� size of chunk�� �ٲ��.

�׸��� ���� ������, 0x602328�� Top chunk�� �ִ� ���� Ȯ�� �� �� �ִ�.

Top chunk�� ���غ��� �Ʊ� ù ��° malloc chunk�� �ִ� Top chunk �� ���� 0x20ef1

�̰� 2��° malloc�� ���� �� �� Top chunk �� ���� 0x20ce1�̴�.

�ϴ� 2��° malloc�� size of chunk �� 0x211�̴�. ���� 1��Ʈ�� flag�̴ϱ� �����ϸ� 0x210, 10������ 528�̴�.

data(512) + prev_size(8) + size_of_chunk(8) = 528�� �´�.

�׷� Top chunk�� ��ȭ�� ���� 0x20ef1 -> 0x20ce1

![](https://lh4.googleusercontent.com/aUpAyMarMNpvy4VRnLFDf90NR5MFGMetyWjKS1MmLJTUuhSqucXGERiqFRG7dK_nn-by0SKdR3A-yx32VQOa8W9qPgGMf79BoDLI3G0ri85QQp5s0CST1m0UOMDspGlcp7OtnUnx)

����� �� �´�.

Top chunk���� ���� malloc�� size��ŭ�� Top chunk���� �Ҵ� �� ��, �������� �ٽ� Top chunk�� �ȴ�.

(��ư �ű�)

���� �Ҵ� �� malloc ������ Ȯ���� �ߴ�.

free chunk�� ���� ���� argv[1] �� argv[2] �� ���� �ְ� data�� ���� ���� Ȯ�� �غ��ڴ�.

strcpy�� ���� ����

![](https://lh5.googleusercontent.com/V5jqvq1MNbNFNwbvMjYKNUHo8zS3mTrmszbfR_CGpNnPbqyD_9PGfiLxB3O2GxqKtrEc4laCQuYULCUTe4ny6b_0c9nelrJySFpOtgHNtN5nKn0BWg3W8YNFFCdpEYHkewoZGv9d)

�� breakpoint�� �ɾ��־���.

�ٽ� r�� ���� �����ϰ� strcpy�� �� ������ �����غ��ڴ�.

![](https://lh3.googleusercontent.com/YlWlRFN-feIe0tvxQdZC8je59nyAU4JxYaJU1bSIczTM76ofmtwX3k_VPS2quxXruQS4lS_s_OEBhzOywGwPPx4vhFAPuDWWU-3nM8lNUhZfJ5s-4yUiLbcS-lpveR_PA472ksW-)

�̷��� ���ڸ� �־���.

�� ������ ���ڴ�.

![](https://lh4.googleusercontent.com/QOTgYGdQLGwzK2ZhUTN2bN0yU3-dqBqc8gdjybq5IZ-xzz_Tof6L5DzLg5S5rXDgw8lqtHX_bcEl_Cfh0iGFlPUNXM_tLzEnb6OBCx5D4Iks18HFdRreOiSbov8kugiiaGxkA2Is)

�� ����.

���� free chunk�� �м��غ��ڴ�.

������ �ɰ�, �� ������ ����.

![](https://lh4.googleusercontent.com/7Z3nQrd18v5-AivCpMggFhwtNoMpE_pqeQhfVbySoQ4o-xwRM9EGHRpL5wHkYyR8JB3IyzmWX9Zo2bI1pn-asBZU9RgTgjGdR-09gCqmMn1ke1R9UF-ZnZA-U2FiAEV22UvujpG6)

���� �߰� �ƴ�.

�Ʊ� free chunk�� ������ ��Ÿ�� ǥ�� ���غ��� fd �� bk�� free�� chunk�� �ִ� ���� Ȯ�� �� �� �ִ�.

0x602010 -> fd - forward pointer

0x602018 -> bk - backward pointer

������ nextchunk�� ����Ű�� pointer, prevchunk�� ����Ű�� pointer�̴�.

free�� �� �� �ǰ� ������

![](https://lh3.googleusercontent.com/j8Z0oZfVV4B_nysp-5bOpDiCofEwXebQzgKWPkyOUxkJVV7S_Ilsmbsip4mnZnY5Ho7r_3L2Y2sLOhTdZdDE7EE8JSbZ3gj4jczyVUzcx37XPwBsIAK7oHzttuxllKe-FcfBHgSZ)

pointer�� Top chunk ���� ����Ű�� �ִ�.

�� ��° free���� �� ������ Ȯ�� �غ���.

![](https://lh5.googleusercontent.com/AZUL2F1It8hRNIt5w57spakaShr7L6kCfMIu7eFZQx6kjgxukoAUs6yFKtrZLPJgVGtjn4wkS09Yvkc_g_8sHXnOX3ZaMRumlJLHkNZznru4hKyl1zhOagAaC2PqDL_wuvwfdMFY)

size of chunk �� 0x320 �̴� 0x110 + 0x210 �� ������ ����Ǿ���.

��.. ���α׷��� ���� ���ᰡ �ȴ�.

���ݱ��� gdb�� malloc�� free chunk�� ������ �м��Ͽ���.

������, 100�� �������� ���� �� �� �غ��°� �ξ� ���ذ� �� �ǰ� ��� �͵� ����.