# 💎Django
## 🙈Tech stack
- [x] **Server OS:** Microsoft Windows 7 Enterprise 6.1.7601 SP1 сборка 7601
- [x] **Web application engine:** Python 3.8.9 & Django 4.0.4

## 👽Создание виртуальной среды
> Виртуальная среда - это среда Python, которая создаётся отдельно для проекта и является изолированной. В этой среде есть отдельные версии Python и других зависимостей, например, Django. Благодаря виртуальной среде приложение может запускаться независимо от других. Кроме того, при обновлении или понижении версий Python или библиотек эти изменения не будут затрагивать другие проекты.

Для создания виртуальной среды определим каталог. Затем выполним команду
```
python -m venv test
```
где test - это имя виртуальной среды.

Для активации виртуальной среды запустим скрипт:
```
test\Scripts\activate.bat
```
Деактивация выполняется командой `deactivate`.

В виртуальной среде установим Django.
```
python -m pip install Django
```

## 🌊Создание проекта

Также создадим отдельный каталог под проекты Django. В нём выполним команду
```
django-admin startproject site
```
где site - название проекта.

Данная команда создаёт каталог вида:
> site/
> 
> &emsp; manage.py
>   
> &emsp; site/
>   
>  &emsp; &emsp; \_\_init\_\_.py
>     
>  &emsp; &emsp; settings.py
>     
>  &emsp; &emsp; urls.py
>     
>  &emsp; &emsp; asgi.py
>   
>  &emsp; &emsp; wsgi.py

## Создание приложения голосования
> Приложение – это Web-приложение, которое предоставляет определенный функционал – например, Web-блог, хранилище каких-то записей или простое приложение для голосования. Проект – это совокупность приложений и конфигурации сайта. Проект может содержать несколько приложений. Приложение может использоваться несколькими проектами.

Для создания приложения нужно выполнить команду 
```
python manage.py startapp app
```
где app - название приложения.

Команда создаст соответствующий подкаталог рядом с site/site

> app/
> 
> &emsp; \_\_init\_\_.py
>   
> &emsp; admin.py
>   
>  &emsp; migrations.py 
>     
>  &emsp; &emsp; \_\_init\_\_.py
>     
> &emsp; models.py
> 
> &emsp; tests.py
> 
> &emsp; views.py

## Первое представление
Создадим первое представление в `app/views.py`
```
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the app index.")
```

Чтобы вызвать представление, нам нужно назначить его на какой-то URL - для этого нам нужна конфигурация URL-ов.

В `app/urls.py`:
```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```
Следующий шаг – добавить ссылку на app.urls в главной конфигурации URL-ов. В ``site/urls.py`` добавим импорт django.urls.include, затем include() добавим в список urlpatterns. Вы должны получить следующий код:
```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('app/', include('app.urls')),
    path('admin/', admin.site.urls),
]
```
Теперь можно обратиться к приложению по пути `localhost/app`.

## Настройка БД
База данных настраивается в `site/settings.py`. Для прототипа будет достаточно sqlite (настроена по-умолчанию).

База данных содержит таблицы приложений, а также моделей.
Для создания таблиц нужно выполнить команду
```
python manage.py migrate
```

## Создание моделей
> Модель — это основной источник данных. Она содержит информацию о наборе полей и о поведении данных, которые вы храните.
В нашем приложении голосования, мы создадим две модели: Question и Choice. Question содержит вопрос и дату публикации. Choice содержит два поля: текст ответа и подсчёт голосов. Каждый объект Choice связан с объектом Question.

В `app/models.py`
```
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

## Активация моделей
Как было отмечено выше, с помощью миграций создаются таблицы в БД для моделей. Кроме того, создаётся Python API для доступа к моделям через веб-интерфейс.

Добавим приложение к проекту в `site/settings.py`:
```
INSTALLED_APPS = [
    'app.apps.AppConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Выполняя `makemigrations`, вы говорите Django, что внесли некоторые изменения в ваши модели (в нашем случае мы создали несколько новых) и хотели бы сохранить их в миграции:
```
python manage.py makemigrations app
```

Если необходимо, можно выполнить `python manage.py check`. Эта команда ищет проблемы в вашем проекте не применяя миграции и не изменяя базу данных.

Теперь, выполните команду `migrate` снова, чтобы создать таблицы для этих моделей в базе данных:
```
python manage.py migrate
```
> Миграции — очень мощная штука. Они позволяют изменять ваши модели в процессе развития проекта без необходимости пересоздавать таблицы в базе данных. Их задача изменять базу данных без потери данных.
> 
> Алгоритм работы с миграциями:
> 1. Внесите изменения в модели (в `models.py`).
> 2. Выполните python manage.py makemigrations чтобы создать миграцию для ваших изменений.
> 3. Выполните python manage.py migrate чтобы применить изменения к базе данных.

## Создание вопроса и ответов через API
Чтобы запустить консоль Python выполните:
```
python manage.py shell
```

> Мы используем эту команду вместо просто «python», потому что manage.py устанавливает переменную окружения `DJANGO_SETTINGS_MODULE`, которая указывает Django путь импорта для файла `site/settings.py`.

```
from app.models import Choice, Question
from django.utils import timezone
q = Question(question_text="Как дела?", pub_date=timezone.now())
q.save()
#отображает все объекты
Question.objects.all()
#<QuerySet [<Question: Question object (1)>]>
```
Для того, чтобы объекты отображались в понятном виде в `app/models.py` добавим методы:
```
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
```
Также добавим пользовательский метод:
```
import datetime
from django.db import models
from django.utils import timezone

class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

Теперь в `python manage.py shell`:
```
from app.models import Choice, Question
from django.utils import timezone
Question.objects.all()
#<QuerySet [<Question: Как дела?>]>
q = Question.objects.get(pk=1)
q.was_published_recently()
#True
q.choice_set.create(choice_text='Норм', votes=0)
q.choice_set.create(choice_text='Не норм', votes=0)
```

## Интерфейс администратора
Создадим суперпользователя:
```
python manage.py createsuperuser
#Username: admin
#Email address: admin@example.com
#Password: **********
#Password (again): *********
#Superuser created successfully.
```

Чтобы обратиться к интерфейсу администратора нужно перейти в приложение `localhost/admin/`:









      
      












## 🐀License
[![MIT License](https://img.shields.io/apm/l/atomic-design-ui.svg?)](https://opensource.org/licenses/mit-license.php)
