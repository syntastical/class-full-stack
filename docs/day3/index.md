# Concepts
Up until, now we've been creating or site using one long string in the Python code, but we and
do this a better way.  We can make the html its own file, to make it easier to read and work
with.  Django even gives us a templating language that can be used to show Python variables,
create loop, import files, and more.

For more on templates see https://docs.djangoproject.com/en/4.2/ref/templates/language/

# Project
Lets update our application to start using templates and to load data from the database. 

Create a new file at the path `polls/templates/polls/index.html` with
```python
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

Update `polls/views.py`, you'll need to rename your current `index` function to `custom` and 
the other code below. 
```python
from django.shortcuts import get_object_or_404, render
from .models import Question
from django.http import HttpResponse

def custom(request):
    # rename current index to custom
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {
        'latest_question_list': latest_question_list,
    }
    return render(request, 'polls/index.html', context)

def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

Update `polls/url.py` to include our new paths.
```python
from django.urls import path

from . import views

urlpatterns = [
    path('custom/', views.custom, name='custom'),
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

## Details view for question
Now lets add a details view for our question.  Add the following to the buttom of `polls/view.py`.
Please note that the imports need to be updated to include `get_object_or_404` and a new  `detail`
function needs to be added.
```python
from django.shortcuts import get_object_or_404, render
# other fuctions 
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

Create a new file `polls/templates/polls/detail.html` with
```html
<h1>{{ question.question_text }}</h1>
<ul>
    {% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
    {% endfor %}
</ul>
```

Now update the urls for polls to include our new view.  In `polls/urls.py` updated 
`urlpatterns` to look like 
```python
from django.urls import path

from . import views

urlpatterns = [
  path('custom/', views.custom, name='custom'),
  path('', views.index, name='index'),
  path('<int:question_id>/', views.detail, name='detail'),
  path('<int:question_id>/results/', views.results, name='results'),
  path('<int:question_id>/vote/', views.vote, name='vote'),
  path('<int:question_id>/', views.detail, name='detail'),
]
```

Last, but not least, update the `polls/templates/polls/index.html` file to include a new link to
the details view.  Note the second link in the code below.
```html
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/{{ question.id }}/">{{ question.question_text }}</a> <a href="{% url 'detail' question.id %}">details</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

## Template our custom page

1. Create a new file `polls/templates/polls/custom.html` and copy the html from the page we create
on Friday into it.

In `polls/views.py`, change the custom function to be  
```python
def custom(request):
    context = {}
    return render(request, 'polls/custom.html', context)
```