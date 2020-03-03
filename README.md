django

# generic

## pre-reqs - python3.8
```pip3.8 install Django
pip3.8 install pillow
# mac specific - path to postgres.app
export PATH=$PATH:/Applications/Postgres.app/Contents/Versions/12/bin
# verify path
which pg_config
# pip3.8 install psycopg2 #did not work
pip3.8 install psycopg2-binary  
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
  
  
  - change default sqllite3 in settings.py
    - set env var in prod
    ```
    export DATABASE_NAME='**************' 
    export DATABASE_USR='**************' 
    export DATABASE_PSD='**************' 
    export DATABASE_HOST='**************' 
    export DATABASE_PORT='**************'
    ```
    - set env var in dev
    export DATABASE_NAME='core_db' 
    export DATABASE_USR='postgres' 
    export DATABASE_PSD='**************' 
    export DATABASE_HOST='localhost' 
    export DATABASE_PORT='5432'

    
  
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
        'NAME':os.environ['DATABASE_NAME'],
        'USER':os.environ['DATABASE_USR'],
        'PASSWORD':os.environ['DATABASE_PSD'],
        'HOST':os.environ['DATABASE_HOST'],
        'PORT':os.environ['DATABASE_PORT'],
        
    }
}
```


### verify PSQL db
- list databases ``` postgres=# \l ```
- switching / connecting to database

``` 
postgres=# \c core_db 
```

```
You are now connected to database "core_db" as user "*******".
core_db=#
```

- checking tables created inside connected database

```
core_db=# \dt+
# sample output

                                 List of relations
 Schema |            Name            | Type  |  Owner   |    Size    | Description 
--------+----------------------------+-------+----------+------------+-------------
 public | app_blog_post              | table | postgres | 8192 bytes | 
 public | auth_group                 | table | postgres | 0 bytes    | 
 public | auth_group_permissions     | table | postgres | 0 bytes    | 
...
```


# tools

- online viewer to see db.sqlite3 file (if no confidential data involved)
http://inloop.github.io/sqlite-viewer/



# resources
