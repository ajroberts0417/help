# help
For helpful pieces of code and installation instructions that I don't want to lose

# New Computer Setup
## Some basics
1. Install homebrew (see [installing homebrew](https://brew.sh/))
`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

2. Install [hub](https://github.com/github/hub) extension
`brew install hub`

3. (Add SSH key)[https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account] for github

4. Update gitconfig
`code ~/.gitconfig`

Add this:
```md
# This is Git's per-user configuration file.
[user]
# Please adapt and uncomment the following lines:
	name = Andrew Roberts
	email = ajroberts0417@gmail.com
```

5. Install postgres
```sh
brew install postgresql
brew services start postgresql
```

## Installing Python
Managing python environments can be tricky, even for experienced developers. One easy tool to do so is [pyenv](https://github.com/pyenv/pyenv).
Here's how you can use pyenv to install a python 3.9 environment

1. Install pyenv (see [pyenv installer](https://github.com/pyenv/pyenv-installer))
`curl https://pyenv.run | bash` 

NOTE: make sure to follow the steps in your terminal after this installation!

1b.  Restart your shell so the changes take effect.

2. Install the latest version of python 3.9 using pyenv
`pyenv install 3.9.2`

3. Use this version locally in your qrono project
(Note: make sure you're in the `qrono` directory!)
`pyenv local 3.9.2`

3b. Alternatively, if you want 3.9.2 to be your global version of python:
`pyenv global 3.9.2`

4. Install the latest version of pipenv:
`pip install pipenv`


# Windows Installation of Python Environment
### Windows

#### Suggestion: Install a console emulator running on ConEmu

My personal recommendation is [Cmder](http://cmder.net/)

#### Step 1: Install Chocolatey, a package manager for windows

Install [chocolatey](https://chocolatey.org/install)
```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

#### Step 2: Install make

Run this in your console
```sh
choco install make
```

Verify your installation of make:
```sh
refreshenv
make --version
```

#### Step 2: Ensure you have Python 3.7 installed

Check your currently installed version of Python.
```sh
python --version
```

If you don't have Python version 3.7, install or upgrade to Python 3 using Chocolatey:
```sh
choco install python
```

#### Step 3: Install Pipenv using pip

Python3 should install pip automatically, but check for updates with the following command:
```sh
python -m pip install -U pip
```

Now install pipenv with a User installation:
```sh
pip install --user pipenv
```

> :warning: **NOTE:** If pipenv isn't available in your console after installing and running `refreshenv`
you will need to add the user base's binary directory to your PATH. On Windows you can find the user base binary directory by running `py -m site --user-site` and replacing site-packages with Scripts. For example, this could return `C:\Users\Username\AppData\Roaming\Python36\site-packages` so you would need to set your PATH to include `C:\Users\Username\AppData\Roaming\Python36\Scripts`. You can set your user PATH permanently in the Control Panel. You may need to run `refreshenv` for the changes to take effect.

**NOTE 2:** dependencies will only be available within the `pipenv` virtualenv. Enter the virtualenv with `pipenv shell`, or run a single command with `pipenv run my-cool-command`.

#### Step 4: Install Postgres using Chocolatey

Postgres requires a password parameter, so run the following command, with your own password to be assigned to the postgres user:

```sh
choco install postgresql10 --params '/Password:YOURPASSWORDHERE' --params-global
```

Postgres should start automatically. If you run into trouble, refer to the [Postgres website](https://www.postgresql.org/download/windows/).

## System Status Checks

### Checking if Postgres is running

Sometimes, a configuration error can cause Postgres to fail on startup. To check the status of the process, run this command:

```sh
pg_ctl status
```


# Problems with Psycopg2 for MacOS

Openssl is a dependency for `psycopg2`, and the installation might fail if the linked library isn't the right version. If this command fails with clang error 1—and you are on macOS—ensure Xcode is installed with `xcode-select --install` and ensure openssl is linked by running:

```sh
env LDFLAGS="-I/usr/local/opt/openssl/include -L/usr/local/opt/openssl/lib" pipenv install --dev
```

If this command fails with clang error 1 and `error: architecture not supported`, add the `ARCHFLAGS` option as well:

```sh
env LDFLAGS="-I/usr/local/opt/openssl/include -L/usr/local/opt/openssl/lib" ARCHFLAGS="-arch i386 -arch x86_64" make install-dev
```


# Deploying a Django app to heroku
https://devcenter.heroku.com/articles/django-app-configuration

Deploying a new heroku app to Django


### 0. Install dependencies
`pipenv install gunicorn django-heroku`

### 1. Configuration
A. At the bottom of your settings.py:

```
import django_heroku

# SECURITY
SECURE_SSL_REDIRECT = True
if SERVICE_ENV == "dev":
    SECURE_SSL_REDIRECT = False
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_SECURE = True
# SECURE_HSTS_SECONDS = 600
SECURE_REDIRECT_EXEMPT = [
    "127.0.0.1",
    "0.0.0.0",
    "localhost",
]

# Activate Django-Heroku.
django_heroku.settings(locals())
```

B. Configure ALLOWED_HOSTS in settings.py:
```
ALLOWED_HOSTS: List[str] = [
    "127.0.0.1",
    "0.0.0.0",
    "localhost",
    "appname.herokuapp.com",
    "weird-name-12345.herokuapp.com",
    "web.site",
]
```

C. Configure Postgres in settings.py:
```
# Database
# https://docs.djangoproject.com/en/3.1/ref/settings/#databases

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": env("PGDBNAME", default=None),
        "USER": env("POSTGRES_USER", default=None),
        "PASSWORD": env("POSTGRES_PASSWORD", default=""),
        "HOST": "localhost",
        "PORT": "5432",
    }
}
```

D. Then, create a Makefile with a release command:
```
# scripts to run in the "release" phase of the deploy process
# https://devcenter.heroku.com/articles/release-phase
.PHONY: release
release:
	set -e pipefail
	python manage.py migrate && \
	python manage.py check --deploy
```

E. Then create a Procfile in your project root like this:
```
release: make release
web: gunicorn <myproject>.wsgi
```

where `myproject.wsgi` is dot notation pointing to the `wsgi.py` file in your project directory.


### 2. Create a heroku project:
In your project root, using the heroku cli run:
`heroku create`

### 3. Set environment variables:
for instance:
`heroku config:set SECRET_KEY=<secret_key>`

### 4. Push
`heroku push origin main`

### 5. Add a custom domain
`heroku domains:add mysite.com`

Then run:
`heroku domains`

And add the record to DNS:
`ALIAS | @ | weird-name-kvpstcwb78masdadsh12387asdhr.herokudns.com`
