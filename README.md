# django_lec
django_lec
from django.shortcuts import HttpResponse, render

def index(request):
    number = 10
    name = "Michael"
    nums = [1, 2, 3, 4, 5]
    return render(request, 'index.html', {"my_num": number, "my_name": name, "my_list": nums})
4. html 문서가 완성되었으면, 메인부분의 url에서 url 링크를 지정한다.

from django.contrib import admin
from django.urls import path
from homepage.views import index

urlpatterns = [
    # view에 대한 정보 추가.
    path('', index), # 127.0.0.1/
    path('admin/', admin.site.urls), # 127.0.0.1/admin/
]
5. settings에서, installed_app 부분에 app 이름 추가, template에서 DIRS를 추가한다.

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'homepage'  # 장고 프로젝트에 설치된 앱 목록 추가 필요
]
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            # 해당 부분에 template 접근 권한을 부여해야 한다.
            os.path.join(BASE_DIR, "homepage", "template")
            ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

from django.db import models


class Coffee(models.Model):
   
    def str (self):
        return self.name

    name = models.CharField(default="", max_length=30)
    price = models.IntegerField(default=0)
    is_ice = models.BooleanField(default=False)
    """
    default를 기본적으로 잡자, null 정하지 않으면, 기본값 False
    문자 : CharField   max_length 필수
    숫자 : IntegerField
    논리형 : BooleanField
    시간/날짜 : DateTimeField
    """

    from django import forms
from .models import Coffee


class CoffeeForm(forms.ModelForm):
    class Meta:
        # Model,Coffee 연동
        model = Coffee
        fields = ('name', 'price', 'is_ice')

from django.shortcuts import render
from .models import Coffee
from .forms import CoffeeForm

def index(request):
    return render(request, 'index.html')


def coffee_view(request):
    coffee_all = Coffee.objects.all()
    # If, request = Post,
    if request.method == "POST":
        form = CoffeeForm(request.POST)
        if form.is_valid():
            form.save()
    form = CoffeeForm() 
    return render(request, 'coffee.html', {"coffee_list": coffee_all, "coffee_form": form})

<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Coffee_list</title>
    </head>
    <body>
        <h1> Coffee_list</h1>
        {% for coffee in coffee_list %}
            <p>{{ coffee.name }}, {{ coffee.price }}</p>
        {% endfor %}

        <p> data_input </p>
        <form method="POST">{% csrf_token %}
            {{ coffee_form.as_p }}
            <button type="submit">Save</button>
        </form>
    </body>
</html>
