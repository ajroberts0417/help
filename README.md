# help
For helpful pieces of code and installation instructions that I don't want to lose


# Windows Installation of Python Environment
### Windows

#### Suggestion: Install a console emulator running on ConEmu

My personal recommendation is [Cmder](http://cmder.net/)

#### Step 1: Install Chocolatey, a package manager for windows

Install [chocolatey](https://chocolatey.org/install)

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

**NOTE:** If pipenv isn't available in your console after installing and running `refreshenv`
you will need to add the user base's binary directory to your PATH. This is relatively simple, read the Yellow Box on [this tutorial page](https://python-docs.readthedocs.io/en/latest/dev/virtualenvs.html#virtualenvironments-ref)

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
