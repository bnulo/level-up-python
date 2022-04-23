## Django Interview Questions (Junior Developer)

[Here is the original Youtube video](https://www.youtube.com/watch?v=9ai0b1LRMQM)

### Basics

Django is a backend python based framework.
Udemy, Pinterest, Instagram, Dropbox, Youtube Use Django.

A project is like an overall environment. it's basic website in a sense.
An app is like a component of that website. An app is really where you hold the project logic. And the project itself can be composed of many apps here so really the project is the configuration and apps are components of that website. for example facebook.com is like a project and different apps would be like the news feed, groups.

### Commands

django -admin startproject PROJECT_NAME
Start a project

python manage.py startapp APP_NAME
Start an app

python manage.py runserver
Run a development server

python manage.py makemigrations
Configures and creates the basic migrations and preps the database for changes.

python manage.py migrate
This is what actually enforces those changes and applies changes to our database.

### Settings.py

Settings.py is the project configuration and is used for:

- database connections
- configuring the apps
- overal command center to the project


### MVT(Model View Template) Structure

Models are a class-based representation of database tables so they represent our database structures

The View is basically the business logic and what triggered and returns back.... Templates, responses and so on

The Template layer is just returning html template. This is where we store templates and Views typically return templates.

### Url patterns

These are url paths to configure the website's routing so per url what's being triggered like exits on a freeway. It's like connecting urls to views and it's a navigation to the website.

### Django Admin panel

A quick setup that django provides like a graphical user interface to see your data, to connect with it. It comes built in with django.

### FBV(Function-Based Views) vs CBV(Class-Based Views)

### Database systems to use with Django

Postgres, mysql, Oracle, DynamoDB, MongoDB ...

### Set up a database connections

In settings.py there is a database variable. It is actually a dictionary. This is where we can update modify our database connection like if we're connecting to postgres.

### Urls
#### Why do we add names to URL's and how do we access them dynamically?

Sometimes we don't hard coding urls and that we're using that name value if it's there we might as well use it you don't necessarily have to use it in projects.

> path('posts/', views.posts, name="posts")

How to use  
When you're adding in a link in a template you're not just using the href tag. you're not just adding in forward slash and then like login  
We're adding in the curly braces. Those are just code blocks or tags and you're just adding in the url name.

> href="{% url 'posts' %}"

Why we use the url name  
Because it makes it more dynamic this way we're not having to hard code the path and it's really good for if we happen to change the url path. so if we change something up but the name stays the same then that dynamic value changes and it keeps our code clean from having to go through and update everything.

### Templates
#### Where do we store templates?

either in the default app structure. Django apps typically tell you to store in a folder called templates in a subfolder called whatever app name is and then you can put all the templates there.  
or if we want to completely manually assign this value we can go into settings.py and there is a Templates variable and there is a variable and there's a list called Directories. 'DIRS'

> TEMPLATES = [
  {
    'DIRS': [
        os.path.join(BASE_DIR, 'frontend/build')
    ]
  }
]

All you have to do is let the project know where the templates are and then you can store them.

#### Django Template Language

- Tags
- Variables
- including
- inheriting

##### What does {} mean in \<h2>{{profile}}\</h2>?

{} is just placeholder for variables. this is how we can output dynamic data. this is how we pass data and set variables in the template

##### What do the curly braces mean with the percent signs?

> {% for order in orders %}
\<tr>
  \<td>{{order.transaction_id}}\</td>
\</tr>
{% endfor %}

Those are just code blocks. we can write python in our templates.
like for loop, if statement

##### how to include?

A good answer for including a section of another html template would be to use include tag.

##### how to inherit and extend a template?

we add in a block tag into the parent template. so for example we have a template called main.html and then we use the includes tag or extends tag in the template where we want to extend it and then add in block tags and that would just extend the template.

> {% extends 'base/main.html' %}
{% include 'base/navbar.html' %}
{% block content %}  
    \<h2>Template content\</h2>  
{% endblock content %}

### Static files
#### What are static files?

This is where we store files like css files, javascript files, images... and we typically store them in their own folder.

#### How do we serve static files during development?

in the settings.py we set the static files directories variable which is a list pointing to where we want to include our static file like templates.

> STATICFILES_DIRS = [
  os.path.join(BASE_DIR, 'static')
]

#### What is MEDIA_ROOT?

That is where we upload user-generated content.

#### What does "python manage.py collectstatic" do?

This takes all of our static files from what we set in static root in a sense it bundles them duplicates them and it preps our files for production and how we deliver them but it in a sense controls how we output our files and where we place them.

#### Serving static files during production?

Django doesn't serve static files in production. we have options like:

- aws s3
- third-party packages like Django white noise

### Common Model attributes
#### What is foreign key?

This just sets a one-to-many relationship between one model and another  
one-to-one field sets a one-to-one relationship  
many-to-many field sets many-to-many relationship  
character field attribute is just a string value or text field

### Querying the Database

all the items in a database table

> model_name.objects.all()

one item from a database

> model_name.objects.get() and querying by attribute

filter items by multiple attributes like filter a user by first name and last name

>

let say we have an article and a user how would you get a user out of an article model

> article.user

Query and get child or get parent

### CSRF tokens

This helps against cross-site forgery attacks. we use on post put delete request that we typically send these with our form. so we're just protecting our data this way.

### What are Model Forms?

This is a class-based representation so it's like a form that gets generated based off of a model. so let's say if we have a customer model we create a class inheriting from form object so when we use that form it auto-generates the all or specified fields

### What is DRF(Django Rest framework)?

We use it to build APIs. or we might want to use react, vue, angular for a frontend framework or just more javascript and it helps to build out that API.

### What are Django Signals?

Signals let us know what's going on in a different part of our application. For example if a user model is saved basically we send out a signal to a receiver and it lets us know that the user model was saved and now we can perform another action.

#### How can we set restrictions on Views?  
#### How we get an unauthenticated user from viewing certain pages?

This is done in many different ways like:

- using middleware
- using decorators
- we can custom build our own decorators
- if we're using class-based views we can set the permissions classes
- setting it by user roles or user attributes

#### What are Model Serializers and Model Serializers?

Serializing data is a way of taking python data and turning it into something like json data, xml ... so a model serializer is something like a model form where we create a class based on a model and data in that model gets turned into json format
