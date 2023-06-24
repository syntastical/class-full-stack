# Concepts

## Databases
There are many different type of databases out there that store and receive data in different
ways.  We will be working with a simple one for this class called `SQLite`.  It is great for
local development and for single user databases.

# Project
To get started with integrating our polls work with the database we need to modify 
`polls/models.py` with
```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

    def __str__(self):
        return self.question_text
    
class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

    def __str__(self):
        return self.choice_text
```

And update the `django_project/settings.py` file, to just add our app the `INSTALLED_APPS` array.
```python
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Now we can use the Django framework to get things ready to setup the database
```shell
python manage.py makemigrations polls
```

By running makemigrations, you’re telling Django that you’ve made some changes to your models (in this case, you’ve made new ones) and that you’d like the changes to be stored as a migration.

Migrations are how Django stores changes to your models (and thus your database schema) - they’re files on disk. You can read the migration for your new model if you like; it’s the file polls/migrations/0001_initial.py. Don’t worry, you’re not expected to read them every time Django makes one, but they’re designed to be human-editable in case you want to manually tweak how Django changes things.

Lets see what the SQL that Django generates looks like
```shell
python manage.py sqlmigrate polls 0001
```

Now lets run migrate to apply the changes to our database.
```shell
python manage.py migrate
```

# Django admin
The Django admin lets us view the data from the database and make changes if we want. To get 
started with Django admin we need to create an admin user by running:
```shell
python manage.py createsuperuser
```

After we fill out all the information, you can navigate to `BASE_URL/admin` to login.

To get our new models to show up in Django admin we need to
1. add data to our tables
    First run a Django shell by
    ```shell
    python manage.py shell
    ```
   
    Then paste the following into the Django shell to create a Question record in the database
    ```python
    from polls.models import Choice, Question
    from django.utils import timezone
    q = Question(question_text="What's new?", pub_date=timezone.now())
    q.save()
    ```
1. modify `polls/admin.py` with 
    ```python
    from django.contrib import admin
    from .models import Question
    
    admin.site.register(Question)
    ```