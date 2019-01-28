# Codegate 2019 ���� write-up

 2018�⿡ �̾� 2019�� �ڵ����Ʈ�� �����ϰ� �Ǿ���. ���� �������� MIC check, 20000 �� ������ Ǯ�� �Ǿ���.
## ������ ����
- �����ι� : Junior
-  �г���  : leehyoreal
- �̸� : ��ȿ��	
- �Ҽ� : �������ͳݰ���б�
- �̸��� : 8891ee@naver.com
##  MIC check
>Let the hacking begins ~  
>
>Decode it :  
>9P&;gFD,5.BOPCdBl7Q+@V'1dDK?qL

`9P&;gFD,5.BOPCdBl7Q+@V'1dDK?qL` ��� ������ ���ڵ��ϴ� ��������.

��Ŵٰ� �̰����� decoding�غ��� base85(ascii85)�� ���ڵ� �� ���ڿ��̾���. 

[Cryptii](https://cryptii.com/pipes/ascii85-encoding) ���⼭ ���ڵ��ߴ�.

**![](https://lh6.googleusercontent.com/Hr7N5XtdY45KcT2ZlHnxvb1UeqF1T8ya7XGKWCXpsmFLDe-43wFuBzbKLSOes9vZDJoJs-Em3TFd0z1VgMu6kT0L7XtrPp8Mtp1VuxXU3JCSWhNhU3TXmG23j03CY_WYR73p9dpm)**

Flag : Let the hacking begins ~

## 20000
>nc 110.10.147.106 15959  
>
>[Download](http://codegate.bpsec.co.kr/__BINARY/c1e3a33d8932a4a61b0e0e0e49d6c9bc)

�� ������ 1���� elf �������ϰ� ����� 20000_so.tar.gz ������ �����Ѵ�.

20000_so.tar.gz�� �������� ���ָ� 20000���� lib_(n).so ������ �����ȴ�. 

���� elf������ ���� �м��غ���, ����ڿ��� 0~20000 ���� �� �ϳ��� �Է¹ް� �Է¹��� ���� n�� lib_(n).so ������ �ε���� .so���� ���� �ִ� test�Լ��� �����Ų��.

**![](https://lh3.googleusercontent.com/v6sS-uoxBecsJ_6I1RjEe7NFgZQIjDCoA_u4iSalZ06lW6nWP3OIOJhKzaTOr1LWU2zxsidzNcFrUn6qbUhfjWy4O02kc03Qj00O_W0syWqBNvVPW2ibw03TCXgDlEYPPUWW8M6o)**

�ƹ� .so������ load���Ѻ���(unexploitable)
>How do you find vulnerable file?

20000���� .so���� �� ����� ���̳ʸ��� ã�Ƴ��� ���� ���� �ǵ����� �� �� �ִ�.

���̳ʸ��� �� �� �м��ϴٺ��� filter1 filter2 �Լ��� ���� .so������ �ε��ϰ� �Է¹��� ������ `system("ls \"�Է�\"")`�� �����Ѵ�. �Է��� system�Լ��� ��ġ�� ������ command injection�� �����ϴ�. ������ filter1�� filter2��� �Լ� ������ `, $, &, |, ; �� �ֿ��� Ư����ȣ���� ���� ����� Ʈ���Ű� �������.


**![](https://lh4.googleusercontent.com/HNMgZxdDM4MO8zutVZEiDumTWhsMBkomOiSnZq6fZAI_HBUOXFtAMxkI8ve44_opdCZMmj6OYqVwziFVYPjq8ovf9ICpkJD-EppLLJb40-0gyigVtNKZL0f_eWlUmkAZvuZ12MZ9)**

���� exploitable�� ���̳ʸ��� ã�ƾ߰ڴٰ� ������ �ߴ�. ���� �� ���� ã�Ҵµ�, �������� tar.gz������ ���������ϰ� `������Ž����`���� ������ ��¥�� �����Ͽ�����,

**![](https://lh3.googleusercontent.com/Lja7iaAiv8qWSmCBdV-17803ssBcAVXSuzHCMyjPar4lKX-Sb93byUxs0-FthlSDUdSAgBvuB4HypOS1UTfdjMZzF6j3mvYRGRi28RFyJVJ665EviHDlIyrN_Wb1S7-vBKpNXBeh)**
 
���� ���� �ִ� lib_17394.so ���ϸ� ������ ��¥�� �ð��� �޶� �����ؼ� �м��غ��Ҵ��� ������� ���� ���̳ʸ��� �¾Ҵ�. (�Ƹ� �������� �Ǽ��� �ƴұ�ʹ�.)

�ٸ� filter�Լ��� �ٸ��� `|`�������� ���� �ʾҰ�, `sh`�̶�� ���ڿ��� ���͸����� �ʾҴ�. �׷��� ���� `sh |`��  �� ������ ���� �� �־���.

**![](https://lh6.googleusercontent.com/Kr2zBPo6SAiGHfVfidZKGzAEUelBVxQQM7YtpdyAdG1V152PSSsfdgqiUxmjOsr8JwhLCwZN0HDzFK1sVkBeZGnH1AwmFc2rwummTkZ0BQDK9RL2_UNkRGH4bHcBA96TSdYhFsC-)**

�׳� cat flag�ϸ� ��ɾ� ��� ����� ������ �Ⱥ��̱� ������ fd�� >&0���� �ٲ��ָ� ���δ�.

flag{Are_y0u_A_h@cker_in_real-word?}
