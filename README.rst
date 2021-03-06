.. {% comment %}

Django project template
=======================

This is the template I use for my projects.

Stack
-----

* `Django`_

* `Postgresql`_

* `Redis`_

* `Daemontools`_

* `Compass`_

.. _Django: https://www.djangoproject.com/
.. _Postgresql: http://www.postgresql.org/
.. _Redis: http://redis.io/
.. _Daemontools: http://cr.yp.to/daemontools.html
.. _Compass: http://compass-style.org/

Features
--------

* ``setup.py`` for deployment using real packages. Do yourself a favor, don't
  use VCS for deployments.

* Local development with `Foreman`_.

* Configuration entirely based on environment variables (**no code changes**
  when your deploy).

* SCSS/compass with simplified Frameless by `@idan`_ for layout management,
  responsive-ready.

* Integrated apps:

  * `django-discover-runner`_

  * `django-floppyforms`_

  * `django-ratelimit-backend`_

  * `django-redis-cache`_

  * `django-sekizai`_

  * `raven`_

  * And for development (in ``requirements-dev.txt``):

    * `django-debug-toolbar`_

    * `flake8`_

.. _Foreman: https://github.com/ddollar/foreman
.. _@idan: https://github.com/idan
.. _django-discover-runner: https://github.com/jezdez/django-discover-runner
.. _django-floppyforms: https://github.com/brutasse/django-floppyforms
.. _django-ratelimit-backend: https://github.com/brutasse/django-ratelimit-backend
.. _django-redis-cache: https://github.com/sebleier/django-redis-cache
.. _django-sekizai: https://github.com/ojii/django-sekizai
.. _raven: https://github.com/getsentry/raven-python
.. _django-debug-toolbar: https://github.com/django-debug-toolbar/django-debug-toolbar
.. _flake8: https://bitbucket.org/tarek/flake8

Usage
-----

To create a project using this template::

    mkvirtualenv -p python2 project_name
    workon project_name
    pip install Django
    django-admin.py startproject --template=https://github.com/brutasse/django-project-template/zipball/master --extension=py,rst,template project_name
    cd project_name
    find . -iname "*.template" -exec rename -v ".template" "" {} \;
    pip install -r requirements.txt && pip freeze -l > requirements.txt

Then follow the instructions in the README file.


What follows is the README for projects created using this template.

-----

.. {% endcomment %}
{{ project_name|title }}
========================

Setting up
----------

::

    cd /path/to/project
    add2virtualenv .
    gem install bundle
    bundle install
    pip install -r requirements.txt
    pip install -r requirements-dev.txt
    createdb -U postgres {{ project_name }}
    make syncdb
    make test

Development
-----------

* Running the development server & compass::

      foreman start

  If you need more processes (queue workers, stub mail servers or other
  things), add them to the ``Procfile``.

* Running the tests::

      make test

  Tests are located in the ``tests/`` directory. Create files at will, the
  test runner will auto-detect tests if the file names begin with ``test``.

Configuration
-------------

This template uses environment variables for configuration and application
secrets. The required environment variables are:

* ``SECRET_KEY``: the secret key for Django

* ``DATABASE_URL``: a heroku-like database URL.

Optionally you can use:

* ``DEBUG``: set it to something non-empty to switch to a development
  environment. Obviously, don't set it in production.

* ``SENTRY_DSN``: your DSN for sending exceptions and 404's to Sentry.

* ``REDIS_URL``: a heroku-like URL to configure redis. If you set it, redis
  will be used as a cache backend and the message and session storage engines
  will use it whenever possible.

Environment varibles are managed with ``envdir`` (from ``daemontools``). To
set an environment variable, add a file to the ``env`` directory with the
appropriate content. When you run any command in your project, prefix it with
``envdir env`` to pass it your configuration variables. Look at the
``Procfile`` and ``Makefile`` to see how it's done.

Deployment
----------

Do **not** use ``foreman`` to deploy apps created this way. Run ``setup.py
sdist``, export the distribution to your own PyPI server and use ``pip`` to
install it on your production machines. Or convert the python distribution to
a system package if you prefer.

Use ``daemontools``'s ``envdir`` program to manage application secrets
(``SECRET_KEY``, ``DATABASE_URL``, ``SENTRY_DSN``, etc.).

Use a process watcher such as ``supervisor`` or ``circus`` to run the web
server. Example::

    envdir /path/to/config /path/to/env/bin/gunicorn {{ project_name }}.wsgi -k gevent -b 127.0.0.1:8000 -w 2

The combination of ``envdir`` and an installable package makes it extremely
simple to automate your deployments.
