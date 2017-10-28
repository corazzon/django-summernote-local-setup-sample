# django-summernote에 기여하기 위한 로컬 환경 설정

* django-summernote 를 로컬 환경에 설정 하기 위한 샘플과 이를 만드는 방법입니다.

* django-summernote에 기여하기 위해서는 로컬 환경에 django-summernote를 clone 받아 설정해 주어야 합니다.

## 로컬 가상환경 설정

* virtualenv를 같은 환경으로 설정하고 django-summernote를 설치하면 클론 받아 온 로컬 django-summernote를 로드하여 사용할 수 있습니다.
* django-summernote 와 local django project 가 동일한 가상환경을 사용하도록 합니다.

가상환경 생성

```
$ python3 -m venv venv_name
```

가상환경 실행

```
$ source venv_name/bin/activate
```

## 로컬 환경에 django-summernote 설치
django-summernote에 기여하기 위해 `pip로 설치하지 않고 로컬 환경에 있는 소스를 로드해 사용합니다.`

```
# 로컬에 django-summernote를 클론 받습니다.
$ git clone git@github.com:summernote/django-summernote.git

# 로컬 환경에 django-summernote를 설치 합니다.
$ python setup.py install -f
```

## 로컬 환경에 django 설치

```
# django-summernote를 실행해 보기 위한 폴더를 생성합니다.
$ mkdir django_summernote_samples

# 생성한 폴더로 이동합니다.
$ cd django_summernote_samples

# 장고를 설치합니다.
$ pip install django

# django_summernote_samples 장고프로젝트를 생성합니다.
$ django-admin startproject django_summernote_samples .

# 기본으로 내장되어 있는 DB를 생성합니다.
$ python manage.py migrate

# 서버를 생성해 봅니다.
$ python manage.py runserver 
```

![장고설치](http://i.imgur.com/XsxCG0k.png)

```
# django-summernote를 사용해볼 앱을 생성합니다. 여기에서는 blog라는 이름으로 생성해 보겠습니다.
$ python manage.py startapp blog
```

`settings.py` 에 다음과 같이 위에서 생성한 앱을 추가해 줍니다.
그리고, 첨부파일 확인을 위해 MEDIA_URL, MEDIA_ROOT의 경로를 설정 합니다.

```python

INSTALLED_APPS = [
 ...
  ] + [
      'django_summernote_samples',
	   'blog',

  ]
  
  
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media/')  
```  

`urls.py` 에는 다음과 같이 django-summernote의 url과 첨부파일을 저장할 MEDIA_URL과 MEDIA_ROOT를 지정해 줍니다.


```python
from django.conf.urls import url, include
from django.contrib import admin
from django.conf.urls.static import static
from django.conf import settings

 urlpatterns = [
     url(r'^admin/', admin.site.urls),
+    url(r'^summernote/', include('django_summernote.urls')),
+] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

`blog/models.py` admin에서 사용할 TextField 를 생성해 줍니다.

```python
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.db import models


class Post(models.Model):
    title = models.CharField(max_length=200)
    text = models.TextField()
```

```
# django_summernote와 관련 된 DB를 생성한다.
$ python manage.py makemigrations django_summernote 

$ python manage.py migrate django_summernote

```

`blog/admin.py`에 다음과 같이 django-summernote 를 설정해 줍니다.

```python
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.contrib import admin
from django_summernote.admin import SummernoteModelAdmin
from .models import Post

# Register your models here.
class PostAdmin(SummernoteModelAdmin):
    pass

admin.site.register(Post, PostAdmin)
```


django-admin 로그인을 위해 createsuperuser 로 관리자 계정을 생성해 줍니다.

```
$ python manage.py createsuperuser
```

`http://127.0.0.1:8000/admin/` 로 관리자 페이지에 접속해서 로그인 한다.

django-summernote 가 적용 된 Post 작성 화면을 볼 수 있습니다.

![장고어드민](http://i.imgur.com/UpUnBXK.png)


위와 같이 작성 된 샘플 프로젝트를 git으로 관리하고자 한다면 `.gitignore`에 다음의 항목을 추가합니다.

`.gitignore`

```
*.py[cod]
db.sqlite3
media
```
