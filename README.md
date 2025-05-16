<div align="center">

![Truck Signs](./screenshots/Truck_Signs_logo.png)

# 🚛 Signs for Trucks – Modular Vinyl Store Platform

![Python version](https://img.shields.io/badge/Python-3.9.22-4c566a?logo=python&logoColor=white&colorB=pink&style=flat-square&colorA=4c566a)
![Django version](https://img.shields.io/badge/Django-2.2.8-4c566a?logo=django&logoColor=white&colorB=pink&style=flat-square&colorA=4c566a)
![Django-RestFramework](https://img.shields.io/badge/DRF-3.12.4-red.svg?logo=django&logoColor=white&style=flat-square&colorA=4c566a&colorB=pink)

</div>

## Table of Contents
* [Description](#description)
- [Project Overview](#project-overview)
- [Features](#features)
- [Architecture](#architecture)
- [Setup Instructions](#setup-instructions)
  - [Manual Setup (Local Development)](#manual-setup-local-development)
  - [Docker Setup](#docker-setup)
- [Environment Variables](#environment-variables)
- [Useful Commands](#useful-commands)
- [Screenshots](#screenshots)
- [Useful Links](#useful-links)

---

## Description

__Signs for Trucks__ is an online store to buy pre-designed vinyls with custom lines of letters (often call truck letterings). The store also allows clients to upload their own designs and to customize them on the website as well. Aside from the vinyls that are the main product of the store, clients can also purchase simple lettering vinyls with no truck logo, a fire extinguisher vinyl, and/or a vinyl with only the truck unit number (or another number selected by the client).

### Settings

The __settings__ folder inside the trucks_signs_designs folder contains the different setting's configuration for each environment (so far the environments are development, docker testing, and production). Those files are extensions of the base.py file which contains the basic configuration shared among the different environments (for example, the value of the template directory location). In addition, the .env file inside this folder has the environment variables that are mostly sensitive information and should always be configured before use. By default, the environment in use is the decker testing. To change between environments modify the \_\_init.py\_\_ file.

### Models

Most of the models do what can be inferred from their name. The following dots are notes about some of the models to make clearer their propose:
- __Category Model:__ The category of the vinyls in the store. It contains the title of the category as well as the basic properties shared among products that belong to a same category. For example, _Truck Logo_ is a category for all vinyls that has a logo of a truck plus some lines of letterings (note that the vinyls are instances of the model _Product_). Another category is _Fire Extinguisher_, that is for all vinyls that has a logo of a fire extinguisher. 
- __Lettering Item Category:__ This is the category of the lettering, for example: _Company Name_, _VIM NUMBER_, ... Each has a different pricing.
- __Lettering Item Variations:__ This contains a foreign key to the __Lettering Item Category__ and the text added by the client.
- __Product Variation:__ This model has the original product as a foreign key, plus the lettering lines (instances of the __Lettering Item Variations__ model) added by the client.
- __Order:__ Contains the cart (in this case the cart is just a vinyl as only one product can be purchased each time). It also contains the contact and shipping information of the client.
- __Payment:__ It has the payment information such as the time of the purchase and the client id in Stripe.

To manage the payments, the payment gateway in use is [Stripe](https://stripe.com/).

### Brief Explanation of the Views

Most of the views are CBV imported from _rest_framework.generics_, and they allow the backend api to do the basic CRUD operations expected, and so they inherit from the _ListAPIView_, _CreateAPIView_, _RetrieveAPIView_, ..., and so on.

The behavior of some of the views had to be modified to address functionalities such as creation of order and payment, as in this case, for example, both functionalities are implemented in the same view, and so a _GenericAPIView_ was the view from which it inherits. Another example of this is the _UploadCustomerImage_ View that takes the vinyl template uploaded by the clients and creates a new product based on it.


## Project Overview

**Signs for Trucks** is an e-commerce platform for purchasing pre-designed vinyl lettering for trucks, or creating customized designs by uploading templates. Customers can:
- Browse and select from predefined categories.
- Upload their own templates.
- Customize with different lettering and styling.
- Checkout via Stripe integration.

---

## Features

- Product categories with custom properties.
- Stripe payments integration.
- Admin panel for product/order/user management.
- REST API with Django Rest Framework.
- Multi-environment configuration.
- Dockerized deployment with PostgreSQL.

---

## Architecture

- **Backend:** Django 2.2.8 + DRF
- **Database:** PostgreSQL
- **Environment Management:** `.env`
- **Containerization:** Docker, Docker Compose

---

## Setup Instructions

### Manual Setup (Local Development)

#### 1. Clone the repository

```bash
git clone git@github.com:vkebkal/truck_signs_api.git
```

```bash
cd truck_signs_api
```

#### 2. Create virtual environment

Configure a virtual env and set up the database. See [Link for configuring Virtual Environment](https://docs.python-guide.org/dev/virtualenvs/) and [Link for Database setup](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-16-04).

```bash
python -m venv venv
```

```bash
source venv/bin/activate
```

#### 3. Install dependencies

```bash
pip install --upgrade pip setuptools wheel
```

```bash
pip install -r requirements.txt
```

#### 4. Set up environment variables

```bash
cd truck_signs_designs/settings
```

```bash
cp simple_env_config.env .env
```

Copy the content of the example env file that is inside the truck_signs_designs folder into a .env file:

The new .env file should contain all the environment variables necessary to run all the django app in all the environments. However, the only needed variables for the development environment to run are the following:

```env
SECRET_KEY=your_django_secret_key
DB_NAME=trucksigns_db
DB_USER=trucksigns_user
DB_PASSWORD=supertrucksignsuser!
DB_HOST=localhost
DB_PORT=5432
```

Update `.env` with your local database settings and keys.

- The SECRET_KEY is the django secret key. To generate a new one see: [Stackoverflow Link](https://stackoverflow.com/questions/41298963/is-there-a-function-for-generating-settings-secret-key-in-django) or

```bash
# Django secret key
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

- **NOTE: not required for exercise**<br/>The STRIPE_PUBLISHABLE_KEY and the STRIPE_SECRET_KEY can be obtained from a developer account in [Stripe](https://stripe.com/). 
        - To retrieve the keys from a Stripe developer account follow the next instructions:
            - Log in into your Stripe developer account (stripe.com) or create a new one (stripe.com > Sign Up). This should redirect to the account's Dashboard.
            - Go to Developer > API Keys, and copy both the Publishable Key and the Secret Key.

- The EMAIL_HOST_USER and the EMAIL_HOST_PASSWORD are the credentials to send emails from the website when a client makes a purchase. This is currently disable, but the code to activate this can be found in views.py in the create order view as comments. Therefore, any valid email and password will work.


#### 5. Set up PostgreSQL

```bash
sudo apt install postgresql postgresql-contrib
```

```bash
sudo systemctl start postgresql
```

```bash
sudo -u postgres psql
```

```bash
# Inside psql
CREATE USER trucksigns_user WITH PASSWORD 'supertrucksignsuser!';
CREATE DATABASE trucksigns_db OWNER trucksigns_user;
GRANT ALL PRIVILEGES ON DATABASE trucksigns_db TO trucksigns_user;
```

#####  ⚠️ Troubleshooting: PostgreSQL Connection Refused

If you encounter this error:

```text
django.db.utils.OperationalError: could not connect to server: Connection refused
```

It likely means PostgreSQL is not running.

#####  ✅ Solution (Local PostgreSQL):

```bash
sudo systemctl start postgresql
```

Check status:

```bash
sudo systemctl status postgresql
```

Toy have to see : 

```bash
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; preset: disabled)
     Active: active (exited) since Fri 2025-05-16 03:22:11 EDT; 2s ago
 Invocation: 3bc870b412f84d3e844a26a9d6f7a117
    Process: 5200 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 5200 (code=exited, status=0/SUCCESS)
   Mem peak: 1.8M
        CPU: 9ms

May 16 03:22:11 kali systemd[1]: Starting postgresql.service - PostgreSQL RDBMS...
May 16 03:22:11 kali systemd[1]: Finished postgresql.service - PostgreSQL RDBMS.
```

#### 6. Run Django

```bash
DJANGO_SETTINGS_MODULE=truck_signs_designs.settings.dev python manage.py migrate
```

```bash
DJANGO_SETTINGS_MODULE=truck_signs_designs.settings.dev python manage.py runserver
```

Visit `http://127.0.0.1:8000/admin`

---

### Docker Setup

#### 1. Clone the repository

```bash
git clone git@github.com:vkebkal/truck_signs_api.git
cd truck_signs_api
```


#### 2. Set up environment variables

```bash
cd truck_signs_designs/settings
cp simple_env_config.env .env
```

Copy the content of the example env file that is inside the truck_signs_designs folder into a .env file:

The new .env file should contain all the environment variables necessary to run all the django app in all the environments.

```env
SECRET_KEY=your_django_secret_key
DOCKER_SECRET_KEY=your_generated_docker_key
DOCKER_DB_NAME=trucksigns_db
DOCKER_DB_USER=trucksigns_user
DOCKER_DB_PASSWORD=supertrucksignsuser!
DOCKER_DB_HOST=db
DOCKER_DB_PORT=5432
```

Update `.env` with your local database settings and keys.

- The SECRET_KEY is the django secret key. To generate a new one see: [Stackoverflow Link](https://stackoverflow.com/questions/41298963/is-there-a-function-for-generating-settings-secret-key-in-django) or

```bash
# Django secret key
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

- The DOCKER_SECRET_KEY is the docker secret key. To generate a new one see:

```bash
# Docker secret key
python -c "import secrets; print(secrets.token_urlsafe(50))"
```

- **NOTE: not required for exercise**<br/>The STRIPE_PUBLISHABLE_KEY and the STRIPE_SECRET_KEY can be obtained from a developer account in [Stripe](https://stripe.com/). 
        - To retrieve the keys from a Stripe developer account follow the next instructions:
            - Log in into your Stripe developer account (stripe.com) or create a new one (stripe.com > Sign Up). This should redirect to the account's Dashboard.
            - Go to Developer > API Keys, and copy both the Publishable Key and the Secret Key.

- The EMAIL_HOST_USER and the EMAIL_HOST_PASSWORD are the credentials to send emails from the website when a client makes a purchase. This is currently disable, but the code to activate this can be found in views.py in the create order view as comments. Therefore, any valid email and password will work.


#### 3. Run Docker

This command reads your Dockerfile and docker-compose.yml, builds the necessary images, and prepares containers: 
```bash
docker-compose build
```

This starts up both the Django application (web) and PostgreSQL database (db) defined in your docker-compose.yml:
```bash
docker-compose up
```

#### 4. Create superuser (Optional step) 

To create a super user run:

```bash
docker-compose exec web python manage.py createsuperuser
```

#### 5. Check

Congratulations =) !!! The App should be running in [http://`<your-ip>`:8000](http://<your-ip>:8000)

---

## Useful Commands

```bash
# Run migrations
docker-compose exec web python manage.py migrate
```

```bash
# Create admin user
docker-compose exec web python manage.py createsuperuser
```

```bash
# Access shell
docker-compose exec web python manage.py shell
```

```bash
# Start all services
docker-compose up
```

```bash
# Stop all services
docker-compose down
```

---

## Screenshots

<div align="center">

### Mobile Admin Panel

![Mobile 1](./screenshots/Admin_Panel_View_Mobile.png)
![Mobile 2](./screenshots/Admin_Panel_View_Mobile_2.png)
![Mobile 3](./screenshots/Admin_Panel_View_Mobile_3.png)

### Desktop Admin Panel

![Desktop 1](./screenshots/Admin_Panel_View.png)
![Desktop 2](./screenshots/Admin_Panel_View_2.png)
![Desktop 3](./screenshots/Admin_Panel_View_3.png)

</div>

---

## Useful Links

- [Django Docs](https://docs.djangoproject.com/en/4.0/)
- [DRF Docs](https://www.django-rest-framework.org/)
- [Docker Docs](https://docs.docker.com/)
- [Stripe Setup](https://stripe.com/docs/keys)
- [DigitalOcean: PostgreSQL Setup](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-16-04)
