### For local development:

DB is not automatically made by django, need to create the DB first and then let Django handle the rest.

```bash
#Open postgreSQL on terminal
psql -U postgres

-- 1. Create a dedicated user for Django 
CREATE USER cart_admin WITH PASSWORD 'secure_pass123'; 

-- 2. Create the database and assign ownership to that user 
CREATE DATABASE shopping_db OWNER cart_admin;
```

OR

```bash
#Open postgreSQL on terminal
psql -U postgres

#Create the DB first.
CREATE DATABASE shopping_db;
```

To create user for your django to use:

```bash
CREATE USER cart_admin WITH PASSWORD 'secure_pass123'; 

-- Connect to DB
\c shopping_db

-- Grant all privileges on public schema to specific user
GRANT ALL ON SCHEMA public TO cart_admin;

-- or make the user the owner of the schema
ALTER SCHEMA public OWNER TO cart_admin;
```

To quit postgre on cli:
`\q`

### For development on Railway:
FIND OUT!!!

### Django Commands:

To check the DB:

```bash
-- Create a admin account:
python manage.py createsuperuser

-- Run the server
python manage.py runserver

```

After running server, go to the local django page e.g. http://127.0.0.1:8000/ and go to the admin page: http://127.0.0.1:8000/admin and log in the admin account.

In admin.py of your functions, ensure that the models are registered.

e.g.
```python
# listings/admin.py
from django.contrib import admin
from .models import Listing, Categories  # <-- Import your database models

# Tell Django to display these tables inside the admin panel UI
admin.site.register(Listing)
admin.site.register(Categories)
```

```Python
# views.py 

# 1. ALWAYS IMPORT YOUR MODELS FIRST from .models 
import Article from django.shortcuts 
import redirect, render

 # 2. NOW YOU CAN USE IT Safely 
 def delete_article(request, pk): 
	 article = Article.objects.get(pk=pk) # Python now knows what "Article" is! article.delete() return redirect('home')
```

### For getting singular records.
.get() requires you to find exactly ONE match, else throw error.

use `get_object_or_404()` so that it cleans up for you by sending a 404 page not found if it there is more than one result returned.

use `get_or_create()` if you want to fetch something, and if it is not there initialize it on the fly.
e.g:
```Python
tag, created = Tag.objects.get_or_create(name="Python") 
if created: 
	print("New tag was added to the database!")
```

### Modifying multiple records:
Use .filter().update() if want to update multiple fields instead of using .save() on each one to save on overhead.
e.g.
`Article.objects.filter(pub_date__year__lt=2025).update(status='draft')`

bulk_create:
```python
article_instances = [ 
	Article(title="First"), 
	Article(title="Second"), 
	Article(title="Third"), ]
	
#Inserts all three rows simultaneously
Article.objects.bulk_create(article_instances)
```

Sorting & Refining:
 `.order_by('-field')`: Sorts results (hyphen means descending).  
 `.exclude(field=val)`: Returns everything *except* matching records.

Performance:

To pull objects that has foreignkey relationships:
`articles = Article.objects.select_related('author').all()`

