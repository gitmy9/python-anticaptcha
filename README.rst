python-anticaptcha
==================

.. image:: https://travis-ci.org/ad-m/python-anticaptcha.svg?branch=master
  :target: https://travis-ci.org/ad-m/python-anticaptcha

.. image:: https://img.shields.io/pypi/v/python-anticaptcha.svg
  :target: https://pypi.org/project/python-anticaptcha/
  :alt: Python package

.. image:: https://badges.gitter.im/python-anticaptcha/Lobby.svg
  :target: https://gitter.im/python-anticaptcha/Lobby?utm_source=share-link&utm_medium=link&utm_campaign=share-link
  :alt: Join the chat at https://gitter.im/python-anticaptcha/Lobby

.. image:: https://img.shields.io/pypi/pyversions/python-anticaptcha.svg
  :target: https://github.com/ad-m/python-anticaptcha/blob/master/setup.py
  :alt: Python compatibility

.. image:: https://coveralls.io/repos/github/ad-m/python-anticaptcha/badge.svg?branch=master
  :target: https://coveralls.io/github/ad-m/python-anticaptcha?branch=master
  :alt: Tests coverage

.. introduction-start

Client library for solve captchas with `Anticaptcha.com support`_.
The library supports both Python 2.7 and Python 3.

The library is cyclically and automatically tested for proper operation. We are constantly making the best efforts for its effective operation.

In case of any problems with integration - `read the documentation`_, `create an issue`_, use `Gitter`_ or contact privately.

.. _read the documentation: http://python-anticaptcha.readthedocs.io/en/latest/
.. _Anticaptcha.com support: http://getcaptchasolution.com/i1hvnzdymd
.. _create an issue: https://github.com/ad-m/python-anticaptcha/issues/new
.. _Gitter: https://gitter.im/python-anticaptcha/Lobby

.. introduction-end


Getting Started
---------------

.. getting-started-start

Install as standard Python package using::

    pip install python-anticaptcha

.. getting-started-end


Usage
-----

.. usage-start

To use this library do you need `Anticaptcha.com`_ API key.

Solve recaptcha
###############

Example snippet for Recaptcha:

.. code:: python

    from python_anticaptcha import AnticaptchaClient, NoCaptchaTaskProxylessTask

    api_key = '174faff8fbc769e94a5862391ecfd010'
    site_key = '6Le-wvkSAAAAAPBMRTvw0Q4Muexq9bi0DJwx_mJ-'  # grab from site
    url = 'https://www.google.com/recaptcha/api2/demo'

    client = AnticaptchaClient(api_key)
    task = NoCaptchaTaskProxylessTask(url, site_key)
    job = client.createTask(task)
    job.join()
    print job.get_solution_response()

The full integration example is available in file ``examples/recaptcha.py``.

If you only process few page many times to increase reliability, you can specify
whether the captcha is visible or not. This parameter is not required, as is the
system detects invisible sitekeys automatically, and needs several recursive
measures for automated training and analysis. For provide that pass
``is_invisible`` parameter to ``NoCaptchaTaskProxylessTask`` or ``NoCaptchaTask`` eg.:

.. code:: python

    from python_anticaptcha import AnticaptchaClient, NoCaptchaTaskProxylessTask

    api_key = '174faff8fbc769e94a5862391ecfd010'
    site_key = '6Lc-0DYUAAAAAOPM3RGobCfKjIE5STmzvZfHbbNx'  # grab from site
    url = 'https://losangeles.craigslist.org/lac/kid/d/housekeeper-sitting-pet-care/6720136191.html'

    client = AnticaptchaClient(api_key)
    task = NoCaptchaTaskProxylessTask(url, site_key, is_invisible=True)
    job = client.createTask(task)
    job.join()
    print job.get_solution_response()


Solve text captcha
##################

Example snippet for text captcha:

.. code:: python

    from python_anticaptcha import AnticaptchaClient, ImageToTextTask

    api_key = '174faff8fbc769e94a5862391ecfd010'
    captcha_fp = open('examples/captcha_ms.jpeg', 'rb')
    client = AnticaptchaClient(api_key)
    task = ImageToTextTask(captcha_fp)
    job = client.createTask(task)
    job.join()
    print job.get_captcha_text()

Solve funcaptcha
################

Example snippet for funcaptcha:

.. code:: python

    from python_anticaptcha import AnticaptchaClient, FunCaptchaTask, Proxy
    UA = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 ' \
         '(KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36'

    api_key = '174faff8fbc769e94a5862391ecfd010'
    site_key = 'DE0B0BB7-1EE4-4D70-1853-31B835D4506B'  # grab from site
    url = 'https://www.google.com/recaptcha/api2/demo'
    proxy = Proxy.parse_url("socks5://login:password@123.123.123.123")

    client = AnticaptchaClient(api_key)
    task = FunCaptchaTask(url, site_key, proxy=proxy, user_agent=user_agent)
    job = client.createTask(task)
    job.join()
    print job.get_token_response()

Report incorrect image
######################

Example snippet for reporting an incorrect image task:

.. code:: python

    from python_anticaptcha import AnticaptchaClient, ImageToTextTask

    api_key = '174faff8fbc769e94a5862391ecfd010'
    captcha_fp = open('examples/captcha_ms.jpeg', 'rb')
    client = AnticaptchaClient(api_key)
    task = ImageToTextTask(captcha_fp)
    job = client.createTask(task)
    job.join()
    print job.get_captcha_text()
    job.report_incorrect()

Custom tasks
############

There is support for your own (captcha) forms. It allows you to analyze any data in various ways, eg. classify offensive
image, count elements on the image, etc. The scope of the data, the form to describe them, you specify yourself.

For details, go to 'Custom fields' section in the documentation.

Setup proxy
###########

The library is not responsible for managing the proxy server. However, we point to
the possibility of simply launching such a server by:

.. code::

    pip install mitmproxy
    mitmweb -p 9190 -b 0.0.0.0 --ignore '.' --socks

Next to in your application use something like:

.. code:: python

    proxy = Proxy.parse_url("socks5://123.123.123.123:9190")

We recommend entering IP-based access control for incoming addresses to proxy. IP address required by
`Anticaptcha.com`_ is:

.. code::

    69.65.41.21
    209.212.146.168

.. _Anticaptcha.com: http://getcaptchasolution.com/i1hvnzdymd

Error handling
##############

In the event of an application error, the AnticaptchaException exception is thrown. To handle the exception, do the following:

.. code:: python

    from python_anticaptcha import AnticatpchaException, ImageToTextTask

    try:
        # any actions
    except AnticatpchaException as e:
        if e.error_code == 'ERROR_ZERO_BALANCE':
            notify_about_no_funds(e.error_id, e.error_code, e.error_description)
        else:
            raise



.. usage-end

Versioning
----------

We use `SemVer`_ for versioning. For the versions available, see the
`tags on this repository`_.

Authors
-------

-  **Adam Dobrawy** - *Initial work* - `ad-m`_

See also the list of `contributors`_ who participated in this project.

License
-------

This project is licensed under the MIT License - see the `LICENSE.md`_
file for details

.. _SemVer: http://semver.org/
.. _tags on this repository: https://github.com/ad-m/python-anticaptcha/tags
.. _ad-m: https://github.com/ad-m
.. _contributors: https://github.com/ad-m/python-anticaptcha/contributors
.. _LICENSE.md: LICENSE.md
