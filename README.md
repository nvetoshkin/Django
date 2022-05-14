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


      
      












## 🐀License
[![MIT License](https://img.shields.io/apm/l/atomic-design-ui.svg?)](https://opensource.org/licenses/mit-license.php)
