---
layout: post
tags:
- django
- python
- signal
- drf
title: "[django] django model signal "
categories: Django

---
# [django] django model signal 

장고의 model signal은 모델이 저장될때 컨트롤 할 수 있게 도와주는 기능으로 간단하게 생각하면 hooking을 생각하면 된다.

대표적인 사용예로는 사용자에게 특정 행동이 발생되었을때 알림을 가게 하는 기능을 구현 할 수가 있다.



우선, 장고의 signal에는 8가지의 기본 내장 시그널이 존재하며 시그널을 사용자가 직접 커스터 마이징 하여 사용하는것도 가능하다.



## siganl의 종류

__`django.db.models.signals`__

| 시그널명         |                                                              |
| ---------------- | ------------------------------------------------------------ |
| `pre_init`       | django에서 model클래스의 `__init__()` 메소드가 실행될때의 시그널 |
| `post_init`      | django에서 model클래스의 `__init__()`메소드의 실행이 종료된 후 의 시그널 |
| `pre_save`       | django에서 model클래스의 `save()`메소드가 시작되는 지점의 시그널 |
| `post_save`      | django에서 model클래스의 `save()`메소드가 종료되는 지점의 시그널 |
| `pre_delete`     | django에서 model클래스의 `delete()`와 queryset의 `delete()`메소드가 시작되는 지점의 시그널 |
| `post_delete`    | django에서 model클래스의 `delete()`와 queryset의 `delete()`메소드가 종료되는 지점의 시그널 |
| `m2m_changed`    | ManyToMany필드의 값이 변경될때 보내지는 시그널. [자세히](https://docs.djangoproject.com/en/3.1/ref/signals/#m2m-changed) |
| `class_prepared` | django에서 model클래스가 준비됬을경우의 시그널 일반적으로 서드파티앱에서는 사용하지 않는다. |



## signal 적용하기

먼저, django의 manage.py로 startapp을 통해 app을 만들었다면 파일의 구조는 다음과 같을것이다.

```bash
app
├── __init__.py
├── admin.py
├── apps.py
├── migrations
│   └── __init__.py
├── models.py
└── views.py
```

여기서 `receivers.py`나 혹은 `signals.py`를 생성하여 원하는 시그널을 작성해준다.

**Example)**

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import Board

@receiver(post_save, sender=Board)
def BoardCreated(sender, instance, **kwargs):
    notice = f'{instance.author}님이 새로운 게시글을 게시하였습니다.'
    print(notice)
```



다음으로는 이제 시그널을 django에 등록해줘야 한다. apps.py의 내용을 다음과 같이 바꿔주자.

```python
from django.apps import AppConfig


class Your_App_Config(AppConfig):
    name = 'your_app_name'
    def ready(self):	# from here
        import your_app_name.signals

```





다음으로는 default App Config를 설정해주어야 한다. 생성한 app의 텅 빈 `__init__.py`파일을 열어 방금 설정한 AppConfig클래스를 default로 설정해준다.

```python
default_app_config = 'your_app_name.apps.Your_App_Config'
```

장고를 재실행해주면 끝이난다.



끝.