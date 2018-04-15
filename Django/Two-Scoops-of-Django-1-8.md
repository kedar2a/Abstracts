# 1 | Coding Style

## 1.1 The Importance of Making Your Code Readable
- Code is read more than it is written. An individual block of code takes moments to write, minutes
or hours to debug, and can last forever without being touched again.
- What this means is that you should go the extra mile to make your code as readable as possible:
    - Avoid abbreviating variable names.
    - Write out your function argument names.
    - Document your classes and methods.
    - Comment your code.
    - Refactor repeated lines of code into reusable functions or methods.
    - Keep functions and methods short. A good rule of thumb is that scrolling should not be necessary to read an entire function or method.
- e.g:
    When you see a variable called balance sheet decrease , it’s much easier to interpret in your mind than an abbreviated variable like `bsd` or `bal` `s` `d`.


## 1.2 PEP 8
- PACKAGE TIP: Use *flake8* For Checking Code Quality
- 1.2.1
    - The 79-Character Limit Our preference is as follows:
        - On open source projects, there should be a hard 79-character limit. Our experience has shown
        that contributors or visitors to these projects will grumble about line length issues.
        - On private projects, we relax the limit to 99 characters, taking full advantage of modern monitors.
        - "Fitting the code in 79 columns is never a good reason to pick worse names for variables, functions, and classes. It’s much more important to have readable variable names than to fit in an arbitrary limit of hardware from three decades ago :)"


## 1.3 The Word on Imports
- The import order in a Django project is:
    1. Standard library imports 
    2. Imports from core Django 
    3. Imports from third-party apps including those unrelated to Django    
    4. Imports from the apps that you created as part of your Django project. (You’ll read more about


## 1.4 Use Explicit Relative Imports
- Get into the habit of using explicit relative imports. It’s very easy to do, and using explicit relative imports is a good habit for any Python programmer to develop.
- TIP: Use `from __future__ import absolute_import`
- e.g: from `.models import WaffleCone`


## 1.5 Avoid Using `Import *`
- The reason for this is to avoid implicitly loading all of another Python module’s locals into and over our current module’s namespace, this can produce unpredictable and sometimes catastrophic results.
- Don’t import everything if you’re only going to use one or two things.


## 1.6 Django Coding Style
- 1.6.1 Consider the Django Coding Style Guidelines
    - https://docs.djangoproject.com/en/dev/internals/contributing/writing-code/
    - https://docs.djangoproject.com/en/dev/internals/contributing/writing-code/coding-style/
    - sample AUTHORS file  (https://github.com/django/django/blob/master/AUTHORS)
    - editorconfig: http://editorconfig.org/ , https://github.com/sindresorhus/editorconfig-sublime
- 1.6.2 Use Underscores in URL Pattern Names Rather Than Dashes.
- 1.6.3 Use Underscores in Template Block Names Rather Than Dashes.

## 1.7 Choose JS, HTML, and CSS Style Guides

## 1.8 Never Code to the IDE (Or Text Editor)
- For example, template tags: follow commonly-used naming pattern of `<app name>_tags.py`


---


# 2 | The Optimal Django Environment Setup

## 2.1 Use the Same Database Engine Everywhere
- 2.1.1 You Can't Examine an Exact Copy of Production Data Locally
- 2.1.2 Different Databases Have Different Field Types/Constraints
    - TIP: Django+PostgreSQL Rocks
- 2.1.3 Fixtures for initial data:
    - https://code.djangoproject.com/wiki/Fixtures
    - https://docs.djangoproject.com/en/1.10/howto/initial-data/
    - https://docs.djangoproject.com/en/1.10/ref/migration-operations/#django.db.migrations.operations.RunPython

## 2.2 Use Pip and Virtualenv
- TIP: virtualenvwrapper
    - We also highly recommend virtualenvwrapper

## 2.3 Install Django and Other Dependencies via Pip
- TIP: Setting `PYTHONPATH`
- you can set your virtualenv `PYTHONPATH` so that the `django-admin.py` command can be used to serve your site and perform other tasks.
- Generally, when working on a single Django project, it’s easier to use manage.py than django-admin. If you need to switch between multiple Django settings files, use `django-admin` with `DJANGO_SETTINGS_MODULE` or the `--settings` command line option.

## 2.4 Use a Version Control System like git.

## 2.5 Optional: Identical Environments:
- There are the environment differences that you can eliminate:
    - Operating system differences
    - Python setup differences
    - Developer-to-developer differences
- 2.5.1 `Vagrant` and `VirtualBox`: 
    - The most common way to set up identical development environments is with `Vagrant` and `VirtualBox`.
    - TIP: Experimental: Using Isolated `Docker` Containers


---


# 3 | How to Lay Out Django Projects

## PACKAGE TIP: Django Project Templates
- https://github.com/pydanny/cookiecutter-django
- https://github.com/audreyr/cookiecutter

## 3.1 Django 1.8's Default Project Layout:
```
$ django-admin.py startproject mysite
$ cd mysite 
$ django-admin.py startapp my_app

# Here’s the resulting project layout:
mysite/
    manage.py
    my_app/
        __init__.py
        admin.py
        models.py
        tests.py
    views.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

- There are a number of problems with Django’s default project layout.

## 3.2 Our Preferred Project Layout
- Our layouts at the highest level are:
```
    LEVEL I:    <repository_root>/
    LEVEL II:       <django_project_root>/
    LEVEL III:          <configuration_root>/
```

- 3.2.1 Top Level: Repository Root
    - The top-level <repository root>/ directory is the absolute root directory of the project. In addition to the <django project root> we also place other critical components like the README.rst, docs/ directory, .gitignore, requirements.txt les, and other high-level les that are required for deployment.

- 3.2.2 Second Level: Project Root
    - If using django-admin.py startproject , you would run the command from within the repository root. e Django project that it generates would then be the project root.

- 3.2.3 Third Level: Configuration Root
    - The <con guration root> directory is where the settings module and base URLConf (urls.py) are placed. is must be a valid Python package (containing an __init__.py module).

- 3.3 Sample Project Layout:
    - Let’s take a common example: a simple rating site. Imagine that we are creating Ice Cream Ratings, a web application for rating different brands and flavors of ice cream.

```
    icecreamratings_project/
        .gitignore
        Makefile
        docs/
        README.rst
        requirements.txt
        icecreamratings/
            manage.py
            media/ # Development ONLY!
            products/
            profiles/
            ratings/
            static/
            templates/
            config/
                __init__.py
                settings/
                urls.py
                wsgi.py
```

- 3.4 What About the Virtualenv?
- Example:
    `~/projects/icecreamratings_project/`
    `~/.envs/icecreamratings/`

- 3.5 Going Beyond startproject
    - 3.5.1 Generating Project Boilerplate With Cookiecutter
        In order to use our Django project template, you’ll need a lightweight command-line tool called
        Cookiecutter. Cookiecutter is an advanced project templating tool that can be used for generating
        Django project boilerplate.
        Here’s how Cookiecutter works:
        . First, it asks you to enter a series of values, e.g the value for project name .
        . Then it generates all your boilerplate project les based on the values you entered.


---


# 4 | Fundamentals of Django App Design

## 4.1 The Golden Rule of Django App Design
- "Write programs that do one thing and do it well"
- In essence, each app should be tightly focused on its task. If an app can’t be explained in a single sentence of moderate length, or you need to say ‘and’ more than once, it probably means the app is too big and should be broken up.

## 4.2 What to Name Your Django Apps:
- keep to single word names
- As a general rule, the app’s name should be a plural version of the app’s main model, but there are many good exceptions to this rule, blog being one of the most common ones.
- You should also consider how you want your URLs to appear when choosing a name.
- Use valid, PEP 8-compliant, importable Python package names: short, all-lowercase names with- out numbers, dashes, periods, spaces, or special characters. If needed for readability, you can use underscores to separate words, although the use of underscores is discouraged.

## 4.3 When in Doubt, Keep Apps Small:
- Try and keep your apps small. Remember, it’s better to have many small apps than to have a few giant apps.

## 4.4 What Modules Belong in an App?
- 4.4.2 Uncommon App Modules
# uncommon modules
```
scoops/
behaviors.py
constants.py
context_processors.py
decorators.py
db/
exceptions
fields.py
factories.py
helpers.py
managers.py
middleware.py
signals.py
utils.py
viewmixins.py
```

- `behaviors.py` : An option for locating model mixins per subsection 6.5.1.
- `constants.py` : A good name for placement of app-level settings. If there are enough of them involved in an app, breaking them out into their own module can add clarity to a project.
decorators.py Where we like to locate our decorators. For more information on decorators, see section 9.3.
- `db/` Used in many projects for any custom model elds or components.
fields.py is commonly used for form elds, but is sometimes used for model elds when there isn’t enough eld code to justify creating a `db/` package.
- `factories.py` Where we like to place our test data factories. Described in brief in subsection 22.3.5
- `helpers.py` What we call helper functions. ese are where we put code extracted from views (sub-section 16.3.3) and models (section 6.5) to make them lighter. Synonymous with `utils.py`
- managers.py When `models.py` grows too large, a common remedy is to move any custom model managers to this module.
- `signals.py` While we argue against providing custom signals (see chapter 28), this can be a useful place to put them.
- `utils.py` Synonymous with `helpers.py`
- `viewmixins.py` View modules and packages can be thinned by moving any view mixins to this mod-ule. See section 10.2.


---


# 5 | Settings and Requirements Files

All settings les need to be version-controlled. is is especially true in production environments, where dates, times, and explanations for settings changes absolutely must be tracked. Don’t Repeat Yourself. You should inherit from a base settings le rather than cutting-and-pasting from one le to another. Keep secret keys safe. ey should be kept out of version control.

## 5.1 Avoid Non-Versioned Local Settings
Let’s break up development, staging, test, and production settings into separate components that inherit from a common base object in a settings le tracked by version control. Plus, we’ll make sure we do it in such a way that server secrets will remain secret.

## 5.2 Using Multiple Settings Files
Instead of having one settings.py le, with this setup you have a settings/ directory containing your
settings les. is directory will typically contain something like the following:

```
settings/
    __init__.py
    base.py
    local.py
    staging.py
    test.py
    production.py
```
Each settings module should have its own corresponding requirements le. We’ll cover this at the end of this chapter in section 5.5, ‘Using Multiple Requirements Files.’

- `base.py`:
    - Settings common to all instances of the project.
- `local.py`:
    - This is the settings file that you use when you're working on the project locally. Local development-specific settings include DEBUG mode, log level, and activation of developer tools like `django-debug-toolbar`. Developers sometimes name this file dev.py.
- `staging.py`:
    - Staging version for running a semi-private version of the site on a production server. This is `test.py` Settings for running tests including test runners, in-memory database definitions, and log settings.
- `production.py`:
    where managers and clients should be looking before your work is moved to production. This is the settings file used by your live production server(s). That is, the server(s) that host the real live website. This file contains production-level settings only. It is sometimes called `prod.py`.

## 5.3 Separate Configuration From Code
- use environment variables instead of keeping secret in `*_settings.py` file.
    - To access environment variables from one of your settings les, you can do something like this:
    ```
        import os
        SOME_SECRET_KEY = os.environ["SOME_SECRET_KEY"]
    ```
    - WARNING: Don't Import Django Components Into Settings Modules
- TIP: Using `django-admin.py` Instead of `manage.py`
    - https://docs.djangoproject.com/en/1.8/ref/django-admin/

## 5.4 When You Can't Use Environment Variables
- we advocate using non-executable les kept out of version control in a method we like to call the secrets le pattern.
- To implement the secrets le pattern, follow these three steps:
    1. Create a secrets le using the con guration format of choice, be it JSON, Con g, YAML, or even XML.
    2. Add a secrets loader ( JSON-powered example below) to manage the secrets in a cohesive, explicit manner.
    3. Add the secrets le name to the .gitignore or .hgignore.

## 5.5 Using Multiple Requirements Files
- It’s good practice for each settings le to have its own corresponding requirements le. what is required on each server. is means we’re only installing
- First create a requirements/ directory in the <repository root>. en create ‘.txt’ les that match the contents of your settings directory. results should look something like:
```
requirements/
    base.txt
    local.txt
    staging.txt
    production.txt
```

## 5.6 Handling File Paths in Settings

---

# 6 | Model Best Practices

## PACKAGE TIP: Our Picks for Working With Models
- `django-model-utils`
- `django-extensions`

## 6.1 Basics:

- 6.1.1 Break Up Apps With Too Many Models
    - In practice, we like to lower this number to no more than 5 models per app.

- 6.1.2 Be Careful With Model Inheritance
    + Django provides three ways to do model inheritance: abstract base classes, multi-table inheritance, and proxy models.
    + WARNING: Avoid Multi-Table Inheritance