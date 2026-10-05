Стартуем проект внутри энва
```bash
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ django-admin startproject admin .
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ ls
admin  manage.py  poetry.lock  pyproject.toml
```

Создаем приложение
```bash
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ python3 manage.py startapp gyminator
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ ls
admin  gyminator  manage.py  poetry.lock  pyproject.toml
```

Делаем в `settings.py`
```python
TIME_ZONE = 'Europe/Moscow'
```

Делаем миграции
```bash
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying sessions.0001_initial... OK
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ ls
admin  db.sqlite3  gyminator  manage.py  poetry.lock  pyproject.toml
```

Делаем суперюзера
```bash
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ python manage.py createsuperuser
Username (leave blank to use 'nikita'): admin
Email address:      
Password: 
Password (again): 
This password is too short. It must contain at least 8 characters.
This password is too common.
This password is entirely numeric.
Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully.
```

Данные
```bash
admin
111111
```

Создание миграций
```bash
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ python manage.py makemigrations
Migrations for 'gyminator':
  gyminator/migrations/0001_initial.py
    + Create model Exercises
```

Применение миграций
```bash
(admin-for-me-py3.10) nikita@computer:~/project/admin_for_me$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, gyminator, sessions
Running migrations:
  Applying gyminator.0001_initial... OK
```

