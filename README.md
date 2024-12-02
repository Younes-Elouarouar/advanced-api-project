Step 1: Install Django and Django REST Framework
Create the project directory:

bash
Copier le code
mkdir advanced-api-project
cd advanced-api-project
Create a virtual environment and activate it:

bash
Copier le code
python3 -m venv venv
source venv/bin/activate  # For Linux/macOS
venv\Scripts\activate     # For Windows
Install Django and Django REST Framework:

bash
Copier le code
pip install django djangorestframework
Create the Django project:

bash
Copier le code
django-admin startproject advanced_api_project .
Create the api app:

bash
Copier le code
python manage.py startapp api
Step 2: Configure the Project
Add rest_framework and api to the INSTALLED_APPS in advanced_api_project/settings.py:

python
Copier le code
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'api',  # Add your app here
]
Run the initial migrations:

bash
Copier le code
python manage.py migrate
Step 3: Define Data Models
Define the Author and Book models in api/models.py:

python
Copier le code
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=255)

    def _str_(self):
        return self.name

class Book(models.Model):
    title = models.CharField(max_length=255)
    publication_year = models.IntegerField()
    author = models.ForeignKey(Author, related_name="books", on_delete=models.CASCADE)

    def _str_(self):
        return self.title
Make and apply migrations:

bash
Copier le code
python manage.py makemigrations
python manage.py migrate
Step 4: Create Custom Serializers
Create serializers.py in the api app and define the serializers:
python
Copier le code
from rest_framework import serializers
from .models import Author, Book
from datetime import datetime

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '_all_'

    def validate_publication_year(self, value):
        current_year = datetime.now().year
        if value > current_year:
            raise serializers.ValidationError("Publication year cannot be in the future.")
        return value

class AuthorSerializer(serializers.ModelSerializer):
    books = BookSerializer(many=True, read_only=True)

    class Meta:
        model = Author
        fields = ['name', 'books']
Step 5: Document Your Model and Serializer Setup
Add comments to your files:

api/models.py:

python
Copier le code
"""
Models for the API app.

Author model: Represents an author with a name.
Book model: Represents a book with a title, publication year, and a foreign key linking to an author.
"""
api/serializers.py:

python
Copier le code
"""
Serializers for the API app.

BookSerializer: Serializes all fields of the Book model and includes custom validation for the publication_year.
AuthorSerializer: Serializes the Author model with nested BookSerializer to represent related books dynamically.
"""
Step 6: Implement and Test
Register the models in api/admin.py to test through the Django admin:

python
Copier le code
from django.contrib import admin
from .models import Author, Book

admin.site.register(Author)
admin.site.register(Book)
Run the development server:

bash
Copier le code
python manage.py runserver
Access the Django admin panel at http://127.0.0.1:8000/admin to create Author and Book entries.

Test the serializers in the Django shell:

bash
Copier le code
python manage.py shell

from api.models import Author, Book
from api.serializers import AuthorSerializer, BookSerializer

author = Author.objects.create(name="J.K. Rowling")
book = Book.objects.create(title="Harry Potter", publication_year=1997, author=author)

serializer = AuthorSerializer(author)
print(serializer.data)
Step 7: Git Workflow
Stage the changes:

bash
Copier le code
git add .
Commit the changes:

bash
Copier le code
git commit -m "Set up advanced-api-project with custom serializers and models"
Push to GitHub:

bash
Copier le code
git push origin master
Your project is now set up and ready for further development. Let me know if you encounter any issues!
