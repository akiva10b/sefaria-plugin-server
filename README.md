# Sefaria Plugin Server

This Django server facilitates the integration of plugins with [Sefaria.org](https://sefaria.org). It provides a secure mechanism for handling encrypted user identifiers sent by Sefaria's main server, decrypting them, and managing user data associated with plugins.

## Features
- Secure handling of encrypted user identifiers.
- Endpoints for creating and updating user data.
- Compatibility with local development and Heroku deployment.

## Local Development

The server can be developed locally using MySQL or SQLite as the database backend. 

### Local Setup

1. Clone the repository:
   ```bash
   git clone <repository_url>
   cd <repository_name>
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Configure your database in `settings.py`. For MySQL, set:
   ```python
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.mysql',
           'NAME': '<your_db_name>',
           'USER': '<your_db_user>',
           'PASSWORD': '<your_db_password>',
           'HOST': 'localhost',
           'PORT': '3306',
       }
   }
   ```

4. Run migrations:
   ```bash
   python manage.py migrate
   ```

5. Start the development server:
   ```bash
   python manage.py runserver
   ```

## Deployment to Heroku

### Prerequisites

1. Add the JawsMySQL addon to your Heroku app:
   ```bash
   heroku addons:create jawsdb
   ```

2. Configure the following environment variables in Heroku:
   - `MYSQL_DATABASE`
   - `MYSQL_HOST`
   - `MYSQL_PASSWORD`
   - `MYSQL_PORT`
   - `MYSQL_URI`
   - `MYSQL_USER`
   - `DISABLE_COLLECTSTATIC=1`
   - `DJANGO_SETTINGS_MODULE=core.settings`

### Deployment Steps

1. Deploy your code to Heroku:
   ```bash
   git push heroku main
   ```

2. Run migrations on Heroku:
   ```bash
   heroku run python manage.py migrate
   ```

## API Endpoints

### 1. Create a New Plugin User

**Endpoint:**
```
POST /plugin_user/
```

**Payload:**
```json
{
  "sefaria_id": "gAAAAABnWLIJwjoKJ3tLqY9JOFJkv3L0kwELmU7-dFlWBfETzTYqJTHOJhxlFJhMfU_5PdIYcLaXVo4NUI4PaGyQaHXaFgWCyw=="
}
```

- The `sefaria_id` can be retrieved by making a request to:
  ```
  sefaria.org/plugin/<your_plugin_id>/user
  ```
  
- The server decrypts the ID using a secret provided by Sefaria.

**Response:**
- Returns the numeric ID of the newly created user.

### 2. Update a Plugin User

**Endpoint:**
```
PUT /plugin_user/
```

**Headers:**
```
Plugin-User-Id: <encrypted_id>
```

- Use the encrypted ID of the user as the value for the `Plugin-User-Id` header.

**Payload:**
- Provide the fields you wish to update.

**Response:**
- Confirms the update of the user's information.

## Security

- Ensure that the secret is securely managed. It isnecessary for decrypting the encrypted IDs and enabling plugin functionality.
- Use HTTPS for all requests to ensure secure communication.
