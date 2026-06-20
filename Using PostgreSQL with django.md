### For local development:

DB is not automatically made by django, need to create the DB first and then let Django handle the rest.

```bash
#Open postgreSQL on terminal
psql -U postgres

-- 1. Create a dedicated user for Django 
CREATE USER django_user WITH PASSWORD 'your_secure_password'; 

-- 2. Create the database and assign ownership to that user 
CREATE DATABASE my_django_db OWNER django_user;
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
GRANT ALL PRIVILEGES ON DATABASE shopping_db TO cart_admin;
```

To quit postgre on cli:
`\q`

### For development on Railway:
FIND OUT!!