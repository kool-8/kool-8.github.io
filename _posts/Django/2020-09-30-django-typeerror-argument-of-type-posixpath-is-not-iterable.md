---
layout: post
tags:
- django
- haystack
- drf
- error
title: "[django] TypeError: argument of type 'PosixPath' is not iterable "
categories: Django

---


해결방법:

일반적으로 장고 프로젝트를 처음 생성했을 때, 해당 에러가 뜨는 경우가 가끔씩 있는데 간단하게 django setting을 조금 손봐주면 된다.

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

이렇게 설정되어있는 데이터베이스를 

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': str(BASE_DIR / 'db.sqlite3'),
    }
}
```

요렇게 바꿔주면 정상작동한다.