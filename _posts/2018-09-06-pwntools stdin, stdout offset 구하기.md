---
layout: post
title: pwntools에서 stdin, stdout offset 구하기

가끔 필요할 때가 있어서 정리.

```python
stdout_offset = lib.symbols['_IO_2_1_stdout_']

stdin_offset = lib.symbols['_IO_2_1_stdin_']
```


함수는 걍 이렇게

```python
system_offset =  lib.symbols['system']
```
