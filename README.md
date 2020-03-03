django

# generic

## pre-reqs - python3.8
```pip3.8 install Django
pip3.8 install pillow
```
## initial django / git (basic) workflow 

- create blank new repo on github

szczepanski/blog

- project / repo init

```django-admin startproject blog_core
mv blog_core blog
cd blog
git init
git remote add origin https://github.com/szczepanski/blog.git
git add -A
git commit -m 'new repo init'
git push -u origin master

git branch feature1
git checkout feature1
python3.8 manage.py makemigrations
python3.8 manage.py migrate
git add -A
git commit -m 'applied first db migrations'
git push origin feature1
```


- app init

```python3.8 manage.py startapp app_blog```

- add new apps to blog_core/settings.py --> INSTALLED_APPS
'app_blog',
'app_projects',


- server check
```python3.8 manage.py runserver```


- migrations - db model changes
  - discover new migrations / db model changes (models.py)
python3.8 manage.py makemigrations
  - apply the discovered migrations
python3.8 manage.py migrate


- define app gui admin credentials
python3.8 manage.py createsuperuser
- provide username and password, email - optional 
- if needed in future, change password for any admin user
python3.8 manage.py changepassword <username>


- register models to show in admin web gui 
blog/app_projects/admin.py

## migrations from sqllite3 to PostreSQL 12(Mac ver)

- download and install

https://postgresapp.com/

- db setup 

  - choose 'postgress' server to init terminal
  - \du <check users>
  - \password postgres <create psd for psql user >
  - CREATE DATABASE core_db; <new db>
  - DROP DATABASE core_db; <remove db>
  - \l <list all dbs>
  
  
  - change default sqllite3
    - set env var ``` export DATABASE_PSD='**************' ```
    
  
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```
to 
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME':'core_db',
        'USER':'postgres'
        'PASSWORD':''
        'HOST':
        'PORT':
        
    }
}
```




# tools
- online viewer to see db.sqlite3 file (if no confidential data involved)
http://inloop.github.io/sqlite-viewer/

# resources
