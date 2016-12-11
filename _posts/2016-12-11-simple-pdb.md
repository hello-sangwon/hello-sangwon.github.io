---
layout: post
title: pdb 간단 사용법
tags:
  - pdb
  - python
  - 파이썬
---
코드에 `pdb.set_trace()` 삽입

```python
# loop-sample.py
import pdb

a = [5, 6, 7, 8, 9]
sum_A = 0
for e in a:
    pdb.set_trace()
    sum_A += e
```

`pdb.set_trace()` 삽입된 지점에서 디버거가 실행된다.

```shell
$ python -i loop-sample.py
> /Users/sangwon/tmp/pdb/loop-sample.py(10)<module>()
-> sum_A += e
(Pdb) print(e)
5
(Pdb) print(sum_A)
0
(Pdb) continue
> /Users/sangwon/tmp/pdb/loop-sample.py(9)<module>()
-> pdb.set_trace()
(Pdb) print(e)
6
(Pdb) print(sum_A)
5
(Pdb) continue
> /Users/sangwon/tmp/pdb/loop-sample.py(10)<module>()
-> sum_A += e
(Pdb) print(e)
7
(Pdb) print(sum_A)
11
(Pdb)
```
