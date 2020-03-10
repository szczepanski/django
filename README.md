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

## migrations from sqlite3 to PostreSQL 12(Mac ver)

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
    ```
    export DATABASE_NAME='core_db' 
    export DATABASE_USR='postgres' 
    export DATABASE_HOST='localhost' 
    export DATABASE_PORT='5432'
    
    export DATABASE_PSD='**************' 

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


### psql commands

- resetting schema / dropping all tables
```
DROP SCHEMA public CASCADE;
CREATE SCHEMA public;
```
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
- checking selected table

```
core_db=# \d+ app_blog_post
# sample output


                                                        Table "public.app_blog_post"
 Column  |          Type          | Collation | Nullable |                  Default                  | Storage  | Stats target | Description 
---------+------------------------+-----------+----------+-------------------------------------------+----------+--------------+-------------
 id      | integer                |           | not null | nextval('app_blog_post_id_seq'::regclass) | plain    |              | 
 title   | character varying(200) |           | not null |                                           | extended |              | 
 date    | date                   |           | not null |                                           | plain    |              | 
 brief   | text                   |           | not null |                                           | extended |              | 
 content | text                   |           | not null |                                           | extended |              | 
Indexes:
    "app_blog_post_pkey" PRIMARY KEY, btree (id)
Access method: heap
```


# Django CMS - Wagtail
## initial setup

- pre-req
```
mkdir django-wag
cd django-wag
python3.8 -m venv django-wag-venv
source django-wag-venv/bin/activate
pip3.8 install wagtail
# to disable venv
deactivate



# verify wagtail
pip show wagtail
# verify packeges installed in venv
pip3.8 freeze
```
- init project

```
#init in current dir without creating separate directory - directly within current working directory
wagtail start wag_play .

#or

#init all project files inside separate wag_play subdirectory 
wagtail start wag_play

pip3.8 install -r requirements_dev.txt
```

- init run
```
# discover migrations and migrate DB
python3.8 manage.py makemigrations
python3.8 manage.py migrate
# init server
python3.8 manage.py runserver 0.0.0.0:8000
```

# Python/Django/Wagtail workflow (semi-detailed)

## init
- pre_req and init wagtail project
  - after wag proj init run
  ```pip install -r requirements_dev.txt```

## migrate sqlite3 to psql
  - set db env vars
  - change base.py setting in .../wag_1/wag_1/settings/base.py
  
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

- makemigrations and migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- verify migration to psql
```
core_db=# \dt+
```
- define user - for app gui admin accees
```python3.8 manage.py createsuperuser```
- run server 
```python3.8 manage.py runserver 0:80```
- check admin access
http://0.0.0.0/admin/
- change any default styling (css, js, images, etc. ) in .../wag_1/wag_1/static if needed
- remove db.sqlite3 as db hosted in psql
- edit home/models.py - ensure content_panels is also added
- makemigrations and migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- go to gui and add content under home page
- edit home page home/templates/home/home_page.html
- edit base page wag_1/templates/base.html
- start new app
``` 
python3.8 manage.py startapp services
```
## new app - services
- add new app (services) to INSTALLED_APPS in setting in wag_1/settings/base.py
- in wag_1/services rempve files that are not needed when using wagtail:
  - tests.py
  - views.py
  - admin.py
- edit models in services/models.py and makemigrations and migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- add child page to home page --> services
  - home/services
- add child page to home/services/ --> coding
  - home/services/coding
- edit templates for services (listing page) and codin (detail page)
  - wag_1/templates/services/service_listing_page.html
  - wag_1/templates/services/service_page.html

- added clean function to handle potential validation errors in services/models.py
- added {% include "includes/header.html" %} and {% include "includes/footer.html" %} in wag_1/templates/base.html
- added wag_1/templates/includes/footer.html and wag_1/templates/includes/header.html

## deboug tools

- added /wag_1/requirements_dev.txt file to specify development requirements that also inherits production i.e. requirements.txt
  - requirements_dev.txt
```
-r requirements.txt
django-debug-toolbar==2.2
pudb==2019.2
```
- install debugging tools

  - DjDT - Django Debugging Tool
  
    - added to dev setting in wag_1/settings/dev.py
```
INSTALLED_APPS += [
    'debug_toolbar',
]

MIDDLEWARE += [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]

INTERNAL_IPS = [
    '127.0.0.1',
]
```

    - update /wag_1/wag_1/urls.py to enable DDT - django debug toolbar
  - shell_plus
    - pip install and add ```'django_extensions',``` to INSTALLED_APPS in settings in wag_1/settings/base.py

    - run
```
python3.8 manage.py shell_plus
```

  - sample debugging / testing

```
# gell al wagtail pages
Page.objects.all()
# check home page
home = HomePage.objects.first()
# check home page all possible options, methods, fields
home. <press tab>
home.id
home.live
home.slug
#verify menu
menu = Menu.objects.first()
menu.to_json()
menu.title
menu.to_json()
menu.pk
menu.panels
ServicePage.objects.all()
ServicePage.objects.live().public()
```
  
  
  
  - PUDB = python interactive debugger
    - to enable it for services listing page added the folowing to the /wag_1/services/models.py:
    ```
    import pudb; pu.db()
    ```
    insiide ServiceListingPage class/ get_context function
    - got to services url --> http://0.0.0.0/services/
    - terminal should now show pudb console, press q to quit

## Flexi(ble) service for Misc / testing pages

- add new service --> flexible for Misc / Miscellaneous / testing pages pages 
```
python3.8 manage.py startapp flexible
```
- add new app - flexible - to INSTALLED_APPS in settings in wag_1/settings/base.py
- in wag_1/flexible remove files that are not needed when using wagtail:
  - tests.py
  - views.py
  - admin.py
- add new template in wag_1/templates/flexible/flexible_page.html
- edit models in #wag_1/flexible/models.py,  makemigrations and migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- add new flexible child page in gui



## add new service django way - respecting django admin.py + use of snippet
- add new service --> testimonials
```
python3.8 manage.py startapp testimonials
```
- in wag_1/testimonials remove files that are not needed when using wagtail (leave admin.py this time):
  - tests.py
  - views.py
- add new app - testimonials - to INSTALLED_APPS in settings in wag_1/settings/base.py
- add entry in to INSTALLED_APPS in wag_1/settings/base.py
```
'wagtail.contrib.modeladmin',
```
- in wag_1/testimonials/ edit admin.py
- edit models in #wag_1/testimonials/models.py,  makemigrations and migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- in gui add new testimonial

## add streamfield - StructBlock

- add new service --> streams
```
python3.8 manage.py startapp streams
```
- add new app - streams - to INSTALLED_APPS in settings in wag_1/settings/base.py
- in wag_1/streams do the following 
  - delete -> tests.py, views.py, admin.py
  - rename models.py -> blocks.py (optional)
- edit first block in wag_1/streams/blocks.py (renamed from models.py)
- edit /wag_1/home/models.py to make new block TitleBlock available in home page
- makemigrations, migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- edit /home/templates/home/home_page.html to make new TitleBlock appear in home page
- add new template in wag_1/templates/streams/title_block.html


## add streamfield - ListBlock
- add new block CardsBlock(blocks.StructBlock) in streams/blocks.py
- edit /wag_1/home/models.py to make new block CardsBlock available in home page
- makemigrations, migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- edit /home/templates/home/home_page.html to make new CardsBlock appear in home page
- add new template in wag_1/templates/streams/cards_block.html
- to make streamfield more structred check the below
  - [optimizing streamfield](https://learnwagtail.com/wagtail-for-beginners/custom-streamfield-block-logic/)

## add streamfield - ListBlock
- add new block ImageAndTextBlock(blocks.StructBlock) in streams/blocks.py
- edit /wag_1/home/models.py to make new block ImageAndTextBlock available in home page
- makemigrations, migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- edit /home/templates/home/home_page.html to make new ImageAndTextBlock appear in home page
- add new template in wag_1/templates/streams/image_and_text_block.html
- add new ImageAndTextBlock in home page body section via gui

## call to action streamfield block
- add new block CallToActionBlock(blocks.StructBlock) in streams/blocks.py
- edit /wag_1/home/models.py to make new block CallToActionBlock available in home page
- makemigrations, migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- edit /home/templates/home/home_page.html to make new CallToActionBlock appear in home page
- add new template in wag_1/templates/streams/call_to_action_block.html
- add new CallToActionBlock in home page body section via gui

## snippets registration
snippets allows to make change to a snippet in one place that then updates in entire webpage wherever it is being used

register sample snippet (testimonial) in home / body section 
- add SnippetChooserBlock in wag_1/home/models.py -->> body (streamfield) so it is available iin home page / body gui
- add new template in wag_1/templates/streams/testimonial_block.html
- add testimonial in gui - homepage/ body section


## table streamfield block
- enable table_block in INSTALLED_APPS in settings in wag_1/settings/base.py
```
'wagtail.contrib.table_block',
```
- add new block PricingTableBlock(blocks.StructBlock) in streams/blocks.py
- edit /wag_1/home/models.py to make new block PricingTableBlock available in home page
- makemigrations, migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
- add new template in wag_1/templates/streams/pricing_table_block.html
- add new PricingTableBlock in home page body section via gui
- some resources:
  - [wagtail table](https://docs.wagtail.io/en/v2.5/reference/contrib/table_block.html)
  - [handsometable](https://handsontable.com/)

## richetext streamfield block
- copy body = StreamField(...) from home/models.py class HomePage(Page) class into
  - flexible/models.py class FlexiblePage(Page)
- makemigrations, migrate specific app model
```
python3.8 manage.py makemigrations flexible
python3.8 manage.py migrate
```
- edit wag_1/templates/flexible/flexible_page.html to include block content
- add template in wag_1/templates/streams/simple_richtext_block.html
  
## image chooser streamfield block
- edit flexible/models.py class FlexiblePage(Page) to include ImageChooserBlock
- add template in wag_1/templates/streams/large_image_block.html

## custom streamfield validation error
- clen function added to streams/blocks.py to enable validation against clashes or absence internal page and external link. 

## limiting pages
- purpose
  - avoid creating two home pages under single root directory of pages
  - avoid creating multiple identical pages of any other type than home (under single root directory of pages)
  
  - avoid creating child page of Home Page type uder current home page, or under any other pages such us about, contact etc.
      - in home/models.py add to HomePage class the following
  ```
  class HomePage(Page):
    parent_page_types = ['wagtailcore.Page']
  ```
  - limit flexible page so it can not be created under root directory, but it can in any other location (under home directory or under itself)
     - in home/models.py add to HomePage class the following
  ```
  class HomePage(Page):
    parent_page_types = ["home.HomePage", "flex.FlexPage"]
 - limit pages in 'services' app  
   - edit services/models.py
     - edit ServiceListingPage (parent page in services)
     ```
     # always live only under home page (not uder root anymore)
     parent_page_types = ["home.HomePage"]
     ```
     or, same restriction can be achieved by specifying allowed subpage_types
     ```
     subpage_types = ["services.ServicePage"]
     ```
     - edit ServicePage (child page in services)
     ```
     # always live only under ServiceListingPage (not uder root or itself)
     parent_page_types = ["services.ServiceListingPage"]
     ```
     to close the tree so no more pages can be created under Service Page add empty list - subpage_types = []
     ```
     class ServicePage(Page):
      # always live only under ServiceListingPage (not uder root anymore)
      parent_page_types = ["services.ServiceListingPage"]
      subpage_types = []
    
     ```
- after above edits add child page option will go straight into child page type if only one availabble / allowed
    
- limit ServiceListingPage (the parent of multiple ServicePage(s)) to only one -> max_count
```
class ServiceListingPage(Page):
    parent_page_types = ["home.HomePage"]
    # same restriction can be achieved by specifying allowed subpage_types
    # subpage_types = ["services.ServicePage"]
    max_count = 1
```
- in single site wagtail / django installation root directory should be restricted so only one Home page may be created / exist uder the root, use of max_count=1
```
class HomePage(Page):
    # parent_page_types --> avoid creating child page of Home Page type uder current home page 
    # or under any other pages such us about, contact etc.  
    parent_page_types = ['wagtailcore.Page']
    subpage_types={'flexible.FlexiblePage', 'services.ServiceListingPage'}
    max_count = 1 
```

## site navigation
- add new service --> menus
```
python3.8 manage.py startapp menus
```
- in wag_1/menus remove files that are not needed when using wagtail:
  - tests.py
  - views.py
  - admin.py
- edit menus/models.py
- add new app - menus - to INSTALLED_APPS in settings in wag_1/settings/base.py
- makemigrations, migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
### wagtail_hooks
- registering menu with wagtail via hooks - making it display as left panel option in web gui
  - create menus/wagtail_hooks.py

### custom template tag
tags can be referenced in templates such as in header.html template ```{% load static %}``` the static content tag is used

Create new custom tag --> menu_tags
- create folder --> menus/templatetags
- create file --> menus/templatetags/menu_tags.py
- add reference to new menu_tags in menwag_1/templates/includes/header.html
```
{% load static menu_tags %}
```

- more complex menus - nested, etc, use 
  - [wagtailmenus](https://github.com/rkhleics/wagtailmenus)
  
 
## contact forms

- init new app
```
python3.8 manage.py startapp contact
```
- enable contact in INSTALLED_APPS in settings in wag_1/settings/base.py
- in wag_1/contact remove files that are not needed when using wagtail:
  - tests.py
  - views.py
  - admin.py
- edit contact/models.py
- makemigrations, migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```
add 'contact.ContactPage', in home/models.py to allowed subpage_types
```
class HomePage(Page):
    # parent_page_types --> avoid creating child page of Home Page type uder current home page 
    # or under any other pages such us about, contact etc.  
    parent_page_types = ['wagtailcore.Page']
    subpage_types={'flexible.FlexiblePage', 'services.ServiceListingPage', 'contact.ContactPage',}
```
- run server and add contact page in gui under the home page
- add wag_1/templates/contact/contact_page.html template
- add wag_1/templates/contact/contact_page_landing.html

- install django-widget-tweaks
- add ``` 'widget_tweaks', ``` to INSTALLED_APPS in settings in wag_1/settings/base.py
- limite form choices in  contact/models.py 
```
FORM_FIELD_CHOICES = (
    ('singleline', _('Single line text')),
    ('multiline', _('Multi-line text')),
    ('email', _('Email')),
    ('url', _('URL')),
)
```
- makemigrations, migrate
```
python3.8 manage.py makemigrations
python3.8 manage.py migrate
```

- any submitted messages will show i gui --> forms section 

## webp image formats
.webp is a new age image format with high compression and very low quality loss
use sample - webp as primary choice and oryginal format (png or jpeg) as a fall-back in case of browser legacy / incompatibility issues

```
{% load wagtailimages_tags %}
{% image self fill-1200x775 as img %}
{% image self fill-1200x775 format-webp as webp_img %}
...
  <div class="col-sm-12">
    <picture>
      <source srcset="{{ webp_img.url }}" type="image/webp">
      <img src="{{ img.url }}" alt="{{ img.alt }}" style='width: 100%; height: auto;'>
    </picture>
  </div>
  ```











# Tips
## project structure
- name wag project root directory as backend
```
cd blog
wagtail start backend
```

- name root directory fornt end - vue js part 
``` 
ls blog
backend
frontend
```
## SMPTP or 3rd part clients to make the website send emails
use case --> in web forms / contact pages
- [postmarkapp](https://postmarkapp.com/)
- [sendgrid](https://sendgrid.com/)
- AWS SES
# tools

- online viewer to see db.sqlite3 file (if no confidential data involved)
http://inloop.github.io/sqlite-viewer/





# resources
