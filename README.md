How to add social login to Django :

we will implement Facebook, Twitter and GitHub authentication using the social-auth-app-django library.

Installation

As usual, go for pip:

pip install social-auth-app-django

Add the social_django to your INSTALLED_APPS:

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'social_django',  

    'mysite.core',	   #Your Project App
]


Migrate the database:

python manage.py migrate

Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions, social_django
Running migrations:
  Applying social_django.0001_initial... OK
  Applying social_django.0002_add_related_name... OK
  Applying social_django.0003_alter_email_max_length... OK
  Applying social_django.0004_auto_20160423_0400... OK
  Applying social_django.0005_auto_20160727_2333... OK
  Applying social_django.0006_partial... OK
 

 
Configuration

Update the MIDDLEWARE_CLASSES by adding the SocialAuthExceptionMiddleware to the end of it:

MIDDLEWARE_CLASSES = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',

    'social_django.middleware.SocialAuthExceptionMiddleware',  # <--
]



Now we update the context_processors inside TEMPLATE:

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            PROJECT_DIR.child('templates'),
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',

                'social_django.context_processors.backends',  # <--
                'social_django.context_processors.login_redirect', # <--
            ]
        },
    },
]

Add the AUTHENTICATION_BACKENDS:

AUTHENTICATION_BACKENDS = (
    'social_core.backends.github.GithubOAuth2',
    'social_core.backends.twitter.TwitterOAuth',
    'social_core.backends.facebook.FacebookOAuth2',

    'django.contrib.auth.backends.ModelBackend',
)


Update the urls.py add the social-auth-app-django urls:

urls.py

from django.conf.urls import url, include
from django.contrib import admin
from django.contrib.auth import views as auth_views

from mysite.core import views as core_views

urlpatterns = [
    url(r'^$', core_views.home, name='home'),
    url(r'^login/$', auth_views.login, name='login'),
    url(r'^logout/$', auth_views.logout, name='logout'),
    url(r'^oauth/', include('social_django.urls', namespace='social')),  # <--
    url(r'^admin/', admin.site.urls),
]


GitHub Authentication :
https://github.com/settings/applications/new

The important step here is the Authorization callback URL. 
http://localhost:8000/oauth/complete/github/.

Anyhoo, let’s grab the information we need:

Client ID
44fd4145a8d85fda4ff1
Client Secret
2de7904bdefe32d315805d3b7daec7906cc0e9e7
Now we update the settings.py:

SOCIAL_AUTH_GITHUB_KEY = '44fd4145a8d85fda4ff1'
SOCIAL_AUTH_GITHUB_SECRET = '2de7904bdefe32d315805d3b7daec7906cc0e9e7'


Add on your template :
<a href="{% url 'social:begin' 'github' %}">Login with GitHub</a><br>
