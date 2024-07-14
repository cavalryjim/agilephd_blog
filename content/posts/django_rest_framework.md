+++
title = 'Django REST Framework'
tags = ["django", "python"]
date = 2020-01-18
+++

![drf](../../drf.png)

Django and the Django REST Framework are a popular combination for building and maintaining robust Web APIs.  The term API is a bit dated and stands for Application Program Interface.  The important word is "Interface" in that the ability to communicate with other applications gives our web app value.

A version of this code can be found on [GitHub](https://github.com/cavalryjim/tweeter_v6).

## HTTP
The HTTP (Hypertext Transfer Protocol) protocol provides the structure for communicating files across the Internet. HTTP contains request methods used to information. The most common requests are GET, POST, PUT, PATCH and DELETE.

| HTTP Verb | CRUD Action |
|---------|---------|
| GET  | Read  |
| POST  | Create   |
| PUT   | Update   |
| PATCH   | Update (partial)  |
| DELETE | Delete |
|=======

HTTP provides the request-response protocol for communications between two applications.  The application making the request is known as the _client_ and the application returning a response is known as the _server_.  Using an HTTP ver and an URL endpoint, the client submits the request.  After receiving the request, the server provides a response and a response code.  If the request was for data and it was processed successfully, the client usually receives data in the form of HTML or JSON.  Regardless, the server will respond with a code.

While HTTP has dozens of response codes, there are four basic outcomes to an HTTP request:

- it worked (200-level code)
- it was redirected (300-level code)
- error with the client's request (400-level code)
- error with the server processing the request (500-level code).

HTTP is a stateless protocol. Each request & response interaction is independent of the previous one.  This makes HTTP very resilient because the status of one communication packet is not dependent on previous packets.  

## REST
Representational state transfer (REST) is an architecture style for designing networked applications using the HTTP protocol.  

**More Coming Soon**

Django REST Framework (DRF) is **the** goto package for building APIs with Django. DRF is robust, testable, well-documented, and comes with plenty of customizable features. DRF mimics Django’s conventions making it easy to learn and use.

## Create API App
You could easily have a Django project that will only be accessed by APIs with no need for templates or returning HTML.  For the Tweeter project, we want both a web-version for users that want to interact via a browser and APIs for mobile apps or Progressive Web Apps (PWA).

We will create an API app inside our project for handling our API logic.

```
(tweeter_app) $ python manage.py startapp api
```

## Install DRF
Like other packages, we can install Django REST Framework via pipenv and add it to settings.py.

```
(tweeter_app) $ pipenv install djangorestframework
```

Update the `INSTALLED_APPS` list in our `tweeter_app/settings.py` file.

```python
...

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'rest_framework', # new
    'bootstrap4',
    'bootstrap_datepicker_plus',

    'users',
    'tweets',
    'api', # new
]

...

# new
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ]
}

...
```

## API Endpoints
In a RESTful architecture, an API endpoint typically refers to some object or set of objects. To be more specific, an endpoint is a reference to a URL that accepts web requests.  

For our Tweeter API, an endpoint that lists all the tweets in the app could look like this: https://tweeter.com/api/v1/tweets/

In general, we add the term 'api', or something similar, to the path and perhaps a version number.  Lets update the `tweeter_app/urls.py` file.

```python
# tweeter_app/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('tweets.urls')),
    path('users/', include('users.urls')),
    path('api/v1/', include('api.urls')), # new
    path('api-auth/', include('rest_framework.urls')), # new
]
```

Create an `api/urls.py` file that will further define two API endpoints.  One for viewing all tweets and another for interacting with single tweets.

```python
# api/urls.py
from django.urls import path
from .views import TweetList, TweetDetail

urlpatterns = [
    path('tweets/', TweetList.as_view()),
    path('tweets/<int:pk>/', TweetDetail.as_view()),
]
```


## Views
For creating the logic in our views, we will use the built-in generic views that comes with DRF.  This speeds development and makes the creation of our APIs more Django-ish.

```python
# api/views.py
from rest_framework import generics
from tweets.models import Tweet
from .serializers import TweetSerializer

class TweetList(generics.ListCreateAPIView):
    queryset = Tweet.objects.all()
    serializer_class = TweetSerializer

class TweetDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Tweet.objects.all()
    serializer_class = TweetSerializer
```


### Serializers
Most API frameworks offer conversion logic, such as serializers, that converts objects and datasets into an API friendly format, such json or xml.  DRF is no different.  Create a file called `api/serializers.py` with the following code.

```python
# api/serializers.py
from rest_framework import serializers
from tweets.models import Tweet

class TweetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Tweet
        fields = ['id', 'user', 'body', 'created_at', 'updated_at']
```


### Browsable API
Let's start the development server and take a look at the working product.

```
(tweeter_app) $ python manage.py runserver
```

You should see something similar to this.

![browseable api](../../browseable_api.png)

Notice that DRF comes with a robust front end for developers to interact with the API.

### Conclusion
Thanks to DRF, you now have a working API and can start sharing endpoints with your team!