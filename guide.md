Step by step guide on how to implement the project.

```python
django-admin startproject django_react_intergration
```
## edit settings.py and add  ##

```python
INSTALLED_APPS = [
    'rest_framework',
    'corsheaders',
    'members'
]



MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
]

CORS_ORIGIN_ALLOW_ALL = True
```

## create members application

```python
python manage.py startapp members
```

## Edit the model file to add a new model

```python
from django.db import models

class Member(models.Model):
    name = models.CharField("Name", max_length=240)
    email = models.EmailField()
    document = models.CharField("Document", max_length=20)
    contact = models.CharField(max_length=20)
    registrationDate = models.DateField("Registration Date", auto_now_add=True)

    def __str__(self):
        return self.name
```

## make migrations folder to manage model changes

```python		
python manage.py makemigrations
```
## create changes to model to create table

```python
python manage.py migrate
```


### create default migration file version 1 to hold default data

```python
python manage.py makemigrations --empty --name members members
```


###  Add these lines to the newly created  members migration file

```python
def create_data(apps, schema_editor):
    Member = apps.get_model('members', 'Member')
    Member(name="John Doe", email="doe@3mail.com", document="22342342", contact="333333").save()
	

 dependencies = [
        ('members', '0001_initial'),
    ]

    operations = [
        migrations.RunPython(create_data),
    ]

```

### add the initial user above to the database

```python
python manage.py migrate
```

## navigate to the members folder and create a  file serializer.py

```python
from rest_framework import serializers
from .models import Member

class MemberSerializer(serializers.ModelSerializer):

    class Meta:
        model = Member
        fields = ('pk', 'name', 'email', 'document', 'contact', 'registrationDate')
```	

## Add endpoints to the urls.py file

```python
 re_path(r'^api/members/$', views.members_list),
 re_path(r'^api/members/([0-9])$', views.members_detail),
 ```
## Create view endpoints under members/views.py

```python

from rest_framework.response import Response
from rest_framework.decorators import api_view
from rest_framework import status

from .models import Member
from .serializer import *

@api_view(['GET', 'POST'])
def members_list(request):
    if request.method == 'GET':
        data = Member.objects.all()

        serializer = MemberSerializer(data, context={'request': request}, many=True)
        return Response(serializer.data)

    elif request.method == 'POST':
        serializer = MemberSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(status=status.HTTP_201_CREATED)
            
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

@api_view(['PUT', 'DELETE'])
def members_detail(request, pk):
    try:
        member = Member.objects.get(pk=pk)
    except Member.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == 'PUT':
        serializer = MemberSerializer(member, data=request.data,context={'request': request})
        if serializer.is_valid():
            serializer.save()
            return Response(status=status.HTTP_204_NO_CONTENT)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        member.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)

```
## Run Django API

```python
python manage.py runserver
```

## Access it using the following API
```python
python manage.py runserver
http://localhost:8000/api/members/
```


 
## Cd to parent directory to create the react frontend project

```javascript
npx create-react-app  members_frontend

```
## cd to members_frontend  and add dependencies

```javascript
npm install bootstrap reactstrap axios --save
``` 
## We use bootstrap for styling and reactstrap  is a react based component

##  Navigate to src/index.js  and import bootstrap

```javascript
import 'bootstrap/dist/css/bootstrap.min.css';
```

##  Create folder members_frontend/src/constants/
##  Add new file index.js
##  Add the following line as a constant

```jaavscript
export const API_URL = "http://localhost:8000/api/members/";

```

##  Create folder members_frontend/src/components/ to hold components
##  Add all the components under the github repository

##  CD to members_frontend and run front end application

npm start






