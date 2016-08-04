docker-wagtail
==
Wagtail, the cms based on django.

Docker Image
==

    $ docker build -t hbrls/wagtail:{version} .

For most of the time, you will continue to install app-specific dependencies. So I left the `USER root` there. Before you start you final app, you should set it to `USER www-data`.

`RUN sed "s/#logformat/$(cat /path/to/uwsgi-logformat.yaml)/" /uwsgi.yaml` to use your customized logformat.

What is already in `appl`, the opinionated best practice?
==

1. Initialized database

       $ mysql> CREATE DATABASE wagtailexample CHARACTER SET utf8 COLLATE utf8_general_ci;
       $ mysql> CREATE USER 'wagtailexample'@'%' IDENTIFIED BY 'wagtailexample';
       $ mysql> GRANT ALL PRIVILEGES ON wagtailexample.* TO 'wagtailexample'@'%'
       $ mysql> FLUSH PRIVILEGES;

       $ wagtail start appl

       $ python manage.py makemigrations
       $ python manage.py migrate

       $ python manage.py createsuperuser  # wagtail:wagtail, wagtail@wagtailexample.com

       $ python manage.py collectstatic

2.  Change the default home page to our own `~/home/models.IndexPage`.

    1. 在 `Welcome to wagtail` 下新建 `IndexPage`，然后将它转移到 `Root` 下
    2. 到 "设置 > 站点 > Root Page" 将默认首页设置为 `IndexPage`

3. Add example of page/subpage hierarchy

       Root
           Welcome to wagtail
           首页                 <- the in facto root
               博客文章列表
                   博客文章列表
                   一类文章
                   二类文章
               关于我们

4. Add example of `WhateverPage` and `AgreementPage`. The `Hallo.js` Wagtail 1.5.3 is using doesn't support `<h1>`. Waiting for the next major release.

What you are supposed to move on?
==

1. "设置 > 站点"

Conventions
==

1. `~/templates` for templates.

2. `~/static` is the *destination* of assets.

3. `DJANGO_ENV`: 'dev', 'sit', 'prod', ... to distinguish deploy environments.

4. The code (e.g. `~/home/models.py`, `~/blog/models.py`) provide *Page Types* to be used. The structure (i.e. site map) is up to your settings from the admin panel.

4. You would be requested a bunch of *carefully-designed-hard-coded-pages*. Just list their empty classes in `~/home/models.py` (e.g. `About`) and do the hard code and admin panel settings.

5. For *do-whatever-you-like* pages, just choose `~/home/models.Whatever` in the admin panel. You can also customize your own class like `~/home/models.Agreement` for a set of similar whatever pages.

References
==

1. https://github.com/apihackers/docker-wagtail
2. [Official: Your first Wagtail site](http://docs.wagtail.io/en/latest/getting_started/tutorial.html)
3. [Official: Wagtail demo project](https://github.com/torchbox/wagtaildemo)
4. [Official: Formatting uWSGI requests logs](http://uwsgi-docs.readthedocs.io/en/latest/LogFormat.html)

CHANGELOG
==

1.7 -> 1.12.2
--

```sql
ALTER TABLE wagtailexample.wagtailcore_pageviewrestriction ADD restriction_type VARCHAR(20) DEFAULT 'password' NULL;
ALTER TABLE wagtailexample.wagtailusers_userprofile ADD preferred_language VARCHAR(10) NULL;
ALTER TABLE wagtailexample.wagtailcore_page ADD last_published_at DATETIME(6) NULL;
ALTER TABLE wagtailexample.wagtailcore_page ADD live_revision_id INT(11) NULL;
ALTER TABLE wagtailexample.wagtailcore_page ADD draft_title VARCHAR(255) NULL;
```
