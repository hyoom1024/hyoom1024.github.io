---
layout: post
title: pwntools stdin, stdout offset 구하기
---


pwntools stdin, stdout offset 구하기

```python
stdout_offset = lib.symbols['_IO_2_1_stdout_']

stdin_offset = lib.symbols['_IO_2_1_stdin_']
```




```python
system_offset =  lib.symbols['system']
```
