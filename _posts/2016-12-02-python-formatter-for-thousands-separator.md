---
layout: post
title: 파이썬에서 천 단위마다 구분자 표시하기
tags:
  - PEP 378
  - python
  - 파이썬
  - 콤마 구분자
---
* 정수에 천 단위마다 쉼표 구분자 추가하기

```python
>>> '{:,d}'.format(1234567890)
'1,234,567,890'
```

* 소수에 천 단위마다 쉼표 구분자 추가하기

```python
>>> '{:,.2f}'.format(1234567890.0)
'1,234,567,890.00'
```

# 참고 자료
* [PEP 378: Format Specifier for Thousands Separator](https://docs.python.org/dev/whatsnew/2.7.html#pep-378-format-specifier-for-thousands-separator)
