# Django deployment



## Przechowywanie plików na AWS S3

#### 1. Logujemy się na AWS i szukamy S3

![image-20201113190249475](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201113190249475.png)

#### 2. Tworzymy nowy Bucket

![image-20201113190508752](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201113190508752.png)

Wybieramy nazwę i region, resztę zostawiamy domyślnie

![image-20201113190900141](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201113190900141.png)



#### 3. Bucket_name > Permissions > CORS 

Wklejamy:

```json
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "POST",
            "PUT"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
]
```

![image-20201113191703306](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201113191703306.png)

#### 4. Szukamy IAM

![image-20201113192121826](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201113192121826.png)

Dalej Users > Add user

![image-20201113192341280](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201113192341280.png)

>Next

![image-20201113192517714](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201113192517714.png)

Więcej nie ustawiamy, 2*next i create user

![image-20201113192720782](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201113192720782.png)

Zapisujemy **Access key ID**
```
AKIAWARP72ZHMWBZ4UE6
```
i **Secret access key**
```
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
```

#### 5. Instalacja wymaganych pakietów w Django
```python
pip install boto3
pip install django-storages
```



#### 6. W pliku settings.py dodajemy 'storages' i ustawiamy zmienne środowiskowe (w tym przypadku jawnie)

```python
INSTALLED_APPS = [
    'hackaton_test.apps.HackatonTestConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'storages'
]

# Powinno być tak 
# JAKAS_ZMIENNA = os.environ.get('NAZWA_ZMIENNEJ_ŚRODOWISKOWEJ')

# Ale zostawiamy tak (PS na samym końcu pliku)

AWS_ACCESS_KEY_ID = "AKIAWARP72ZHMWBZ4UE6"
AWS_SECRET_ACCESS_KEY = "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"
AWS_STORAGE_BUCKET_NAME = "django-dotnet-pk"

AWS_S3_REGION_NAME = 'us-east-2'
AWS_S3_SIGNATURE_VERSION = 's3v4'

AWS_S3_FILE_OVERWRITE = False
AWS_DEFAULT_ACL = None

DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'

```



## Deploy na Heroku

#### 1. Logujemy się na heroku i tworzymy nową aplikację

![image-20201114191350233](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201114191350233.png)

#### 2. Wybieramy repo i brancha

![image-20201114191818533](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201114191818533.png)

![image-20201114191854089](https://raw.githubusercontent.com/JedrzejMatuszewski/dokumentacja/main/src/img/image-20201114191854089.png)



#### 3. Przygotowanie projektu do deploya

W katalogu projektu utwórz plik "Procfile"

```python
web: gunicorn hackaton_project.wsgi
    
# web: gunicorn folder_projektu.wsgi    
```


Instalowanie zależności

```python
pip install gunicorn
pip install django-heroku

pip freeze > requirements.txt
```

###### W pliku settings.py

- dodajemy importy
- Edytujemy ALLOWED_HOSTS
- dodajemy STATIC_ROOT  i wywołujemy django_heroku.settings(locals())

```python
import os
import django_heroku

ALLOWED_HOSTS = ['*']
# Najlepiej tak ALLOWED_HOSTS = ['dotnet-pk.herokuapp.com', '127.0.0.1']

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

#bottom
django_heroku.settings(locals())
```

###### Lokalnie instalujemy Postgres
```python
https://www.enterprisedb.com/downloads/postgres-postgresql-downloads
```

###### Lokalnie instalujemy Heroku CLI
```python
https://devcenter.heroku.com/articles/heroku-cli

# W wierszu poleceń: 
heroku login
heroku run bash -a dotnet-pk
python manage.py migrate
```
