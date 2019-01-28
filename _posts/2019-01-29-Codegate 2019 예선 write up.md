---
layout: post
title: Codegate 2019 예선 write up
---

나는 예선에서 MIC check, 20000 두 문제를 풀게 되었다.  

닉네임 : leehyoreal  
이름 : 이효민  
소속 : 선린인터넷고등학교  
이메일 : 8891ee@naver.com

##  MIC check
>Let the hacking begins ~  
>
>Decode it :  
>9P&;gFD,5.BOPCdBl7Q+@V'1dDK?qL

`9P&;gFD,5.BOPCdBl7Q+@V'1dDK?qL` 라는 문장을 디코딩하는 문제였다.

헤매다가 이것저것 decoding해보니 base85(ascii85)로 인코딩 된 문자열이었다. 

[Cryptii](https://cryptii.com/pipes/ascii85-encoding) 여기서 디코딩했다.

**![](https://lh6.googleusercontent.com/Hr7N5XtdY45KcT2ZlHnxvb1UeqF1T8ya7XGKWCXpsmFLDe-43wFuBzbKLSOes9vZDJoJs-Em3TFd0z1VgMu6kT0L7XtrPp8Mtp1VuxXU3JCSWhNhU3TXmG23j03CY_WYR73p9dpm)**

Flag : Let the hacking begins ~

## 20000
>nc 110.10.147.106 15959  
>
>[Download](http://codegate.bpsec.co.kr/__BINARY/c1e3a33d8932a4a61b0e0e0e49d6c9bc)

이 문제는 1개의 elf 실행파일과 압축된 20000_so.tar.gz 파일을 제공한다.

20000_so.tar.gz을 압축해제 해주면 20000개의 lib_(n).so 파일이 생성된다. 

메인 elf파일을 대충 분석해보면, 사용자에게 0~20000 숫자 중 하나를 입력받고 입력받은 숫자 n의 lib_(n).so 파일을 로드시켜 .so파일 내에 있는 test함수를 실행시킨다.

**![](https://lh3.googleusercontent.com/v6sS-uoxBecsJ_6I1RjEe7NFgZQIjDCoA_u4iSalZ06lW6nWP3OIOJhKzaTOr1LWU2zxsidzNcFrUn6qbUhfjWy4O02kc03Qj00O_W0syWqBNvVPW2ibw03TCXgDlEYPPUWW8M6o)**

아무 .so파일을 load시켜보면(unexploitable)
>How do you find vulnerable file?

20000개의 .so파일 중 취약한 바이너리를 찾아내는 것이 문제 의도임을 알 수 있다.

바이너리를 몇 개 분석하다보면 filter1 filter2 함수를 가진 .so파일을 로드하고 입력받은 내용을 `system("ls \"입력\"")`로 실행한다.

입력이 system함수를 거치기 때문에 command injection이 가능하다.

하지만 filter1과 filter2라는 함수 때문에 \`, $, &, \|, ; 등 주요한 특수기호들이 막혀 취약점 트리거가 힘들었다.


**![](https://lh4.googleusercontent.com/HNMgZxdDM4MO8zutVZEiDumTWhsMBkomOiSnZq6fZAI_HBUOXFtAMxkI8ve44_opdCZMmj6OYqVwziFVYPjq8ovf9ICpkJD-EppLLJb40-0gyigVtNKZL0f_eWlUmkAZvuZ12MZ9)**

따라서 exploitable한 바이너리를 찾아야겠다고 생각을 했다. 나는 꽤 쉽게 찾았는데, 제공받은 tar.gz파일을 압축해제하고 `윈도우탐색기`에서 수정된 날짜로 정렬하였더니,

**![](https://lh3.googleusercontent.com/Lja7iaAiv8qWSmCBdV-17803ssBcAVXSuzHCMyjPar4lKX-Sb93byUxs0-FthlSDUdSAgBvuB4HypOS1UTfdjMZzF6j3mvYRGRi28RFyJVJ665EviHDlIyrN_Wb1S7-vBKpNXBeh)**
 
제일 위에 있는 lib_17394.so 파일만 수정된 날짜와 시간이 달라 수상해서 분석해보았더니 취약점을 가진 바이너리가 맞았다. (아마 제작자의 실수가 아닐까싶다.)

다른 filter함수와 다르게 `|`파이프를 막지 않았고, `sh`이라는 문자열도 필터링하지 않았다. 그래서 쉽게 `sh |`로  쉘 권한을 얻을 수 있었다.

**![](https://lh6.googleusercontent.com/Kr2zBPo6SAiGHfVfidZKGzAEUelBVxQQM7YtpdyAdG1V152PSSsfdgqiUxmjOsr8JwhLCwZN0HDzFK1sVkBeZGnH1AwmFc2rwummTkZ0BQDK9RL2_UNkRGH4bHcBA96TSdYhFsC-)**

그냥 cat flag하면 명령어 출력 결과가 나에게 안보이기 때문에 fd를 >&0으로 바꿔주면 보인다.

flag{Are_y0u_A_h@cker_in_real-word?}

