---
layout: post
tags:
- django
- python
- drf
- conditions
title: "[django] queryset Multiple Q conditions with Iterators"
categories: Django

---
장고에서는 `Q` 를 통해 쿼리문에서 조건식을 표현할 수 있다.

iterators와 함께 사용가능한 방법은 크게 3가지가 있다.

1. 오퍼레이터 `|=` 을 사용하는 방법
2. `Q.add()` 를 사용하는 방법
3. `reduce` 를 사용하는 방법

## `|=` 을 사용하여 조건식 생성

가장 보기 쉽고 편한 방법이다.

먼저, python의 제너레이터를 사용하여 원하는 조건의 Q오브젝트들을 리스트로 형성한다.

```python
from django.db.models import Q

conditions = [Q(**{f'{field}': field_value}) for field in fields]
# 여기서 **{x:y}는 참조를 넘겨주기 위함인데 이는 Q(field=field_value)와 동일하게 작동된다.
```

다음으로 최초의 Q를 가진 변수를 생성하기 위해 `conditions` 에서 `pop()` 으로 조건하나를 빼온다. (이는 for문에서 or연결을 시키기 위함이다.)

```python
query_filter = conditions.pop()
```

다음으로 for문으로 `or`  연산을 시킨뒤 이를 필터에 작성하면 복수의 조건식을 만족하는 필터가 작동된다.

```python
for condition in conditions:
	query_filter |= condition  # Q(1) | Q(2) | Q(3) | Q(4) ...

Model.objects.filter(query_filter)
```

개인적으로 이 방법이 가장 보기도 편하고 적용시키기도 좋은 방법이었다.

## `Q.add()` 를 사용하여 조건식 생성

이 방법은 Q모듈 자체에서 제공하는 기능인데 조건을 더해주는 기능을 가지고 있다.  하지만 처음부터 초기의 Q를 따로 설정해야하기도 하고 어차피 for문을 사용해야 하는데 더 간단하게 `|=` 연산자를 사용하는 방법이 있으므로 이 방법은 조금 더 복잡한 조건식을 만들때 유용할거라고 생각된다.

Ex) OR( AND(c1,c2), AND(c3,c4),OR(c5,c6) ) 와 같이 OR, AND가 복합적으로 사용되는 경우.

이것도 간단하게 Q() 오브젝트를 가진 초기 변수를 설정해준다.

```python
conditions = Q(field=field_value)
```

다음으로 for문을 사용해서 add를 누적시켜준다.

```python
for item in condition_items:
	conditions.add(item, Q.OR) # Q.OR로 조건을 지정해준다.

Model.objects.filter(conditions)
```

## `reduce()` 와 `operator.or_` 를  사용한 방법

만약 for문이 많을경우 보기 더러워지고 좀 더 간단히 표현하고 싶을 때 이 방법을 사용하면 가장 짧게 코드로 작성이 가능하다.

```python
from functools import reduce
from operator import or_
from django.db.models import Q

conditions = [Q(1), Q(2), Q(3), Q(4), ...]
filters = reduce(or_, (condition for condition in conditions))
Model.objects.filter(filters)
```

이 방법은 단순하게 `for + |=` 를 사용하여 만든 방법을 reduce를 통해서 조금 더 짧게 만든 식이다.

## 적용 : 검색필터 만들기(drf)

만약 게시판에서 검색기능을 구현할때 `body|title|tags`	중에 하나를 선택하거나 중복선택을 통해 검색하는 경우 보통 아래와 같이 구현하는 경우가 더러 있다.

```python
if search_type == 'all':
	filter = (Q(body__icontains=search_q) | Q(title__icontains=search_q) | Q(tags_icontains=search_q))
elif search_type == 'body':
	filter = Q(body__icontains=search_q)
elif search_type == 'title':
	...
	...
	...
```

이렇게 if \~ elif문으로 구현하게 되면 줄수도 길어지고 무었보다 검색할 타입의 수가 많아질때마다 코드를 수정해줘야 한다.

이때, list를 조건식으로 변경해주는 위의 방법들을 사용하면 아래와 같이 간단하게 구현 가능해지며 type의 수가 많아져도 따로 수정할 코드가 많이 없다.

```python
search_q = self.request.GET.get('search_q')
        search_types = self.request.GET.get('search_type').split('|')  # title|body|tags
        allowed_search_types = ['title', 'body', 'tags']
        conditions = [Q(**{f'{field}__icontains': search_q}) for field in search_types
                        if field in allowed_search_types]
        if conditions:
            filters = reduce(or_, (condition for condition in conditions))
            queryset = query.filter(filters)
        else:
            queryset = query
```