---
layout: post
title: Things to consider for a secure Django deployment
tags: Django
---

# A Highly-opinionated secure deployment for Django

## Basic Django deployment

A production-quality Django deployment addresses more than the process of getting the Django application up and running. There are ancillary processes that may be used as part of your system. In my case, this usually includes using Redis (or Valkey) for cache and other inter-process communications (IPC) requirements. I also use Postfix as an email relay agent, transferring the work for that from Django to an external process.

I like to use this diagram to show the connections between the key components:
![Diagram of Django deployment](/images/django_deployment.png)

This is a fairly typical deployment for me.

I'm generally running this either on an Ubuntu distribution or a Raspberry Pi, which means the commands and configuration options being selected are targeted to those platforms.

This implies using (where appropriate):
- systemd
- tmpfiles.d
- unix domain sockets
- FHS directories (Filesystem Hierarchy Standard)
- Standard user and group security (no ACLs (Access Control List))

I tend to prefer using unix domain sockets over internal ip addresses, they generally have a much lower latency for connections and data transfers. (You can't do this if you have components running on separate machines.)

There are a lot of "fiddley bits" involved with setting this up, and through the years I have developed some scripts to reduce the amount of manual intervention. These scripts are typically run either directly from the shell or through ansible.

I don't fully automate this because I work in a variety of development & deployment environments. I have some servers where I do development on the server itself, some where I can deploy via git, and others where I have had to deploy by copying directories directly.

I'm also working with a number of git repos using atypical directory structures. These repos frequently have the Django project code inside another directory, and it's that "inner" directory that needs to be deployed.

If I have an active project, I usually end up with a set of scripts specific to that project. (Yes, there are better ways of handling this. No, I don't do this often enough to worry about it - yet.)

> Note: What follows is what I consider my **baseline** deployment environment to be. An even more secure environment is possible through the use of facilities such ACLs, and cgroups. Docker containers can also provide a more secure deployment environment - but not by default. (It takes some work to create a secure Docker-based Django deployment.)

### Directory usage

My `/home` directory is reserved as a "working" environment. I never run production code from within `/home`. I might be using it for development, or it might be the location from which I do a `git pull` or `rsync`. As a result, how I use `/home` is very much unique by project, and so my deployments work from that as the starting point.

For the actual deployment, I use the following directories:

- `/opt/<project>`: project code
- `/var/www/html/<project>`: static files
- `/var/www/media/<project>`: media files (when needed)
- `/var/log/uwsgi/<project>`: log files
- `/run/<project>`: project-owned domain sockets

For the purpose of the remainder of this post, I'm going to give examples and snippets as if I were deploying the Django Tutorial `polls` project, using the name `mysite` as the project name. This means that the following directories will need to exist:

- `/opt/mysite`: project code
- `/var/www/html/mysite`: static files
- `/var/www/media/mysite`: media files (when needed)
- `/var/log/uwsgi/mysite`: log files
- `/run/mysite`: project-owned domain sockets


### Processes

#### I still use uWSGI to run my Django projects.

- It can handle projects in multiple languages. (I'm still maintaining a web site written in Perl, among others.)

- It has a comprehensive set of management and monitoring tools that I am comfortable with.

#### I've migrated my personal projects from redis to valkey.

Either one can be used for:

-  Django's cache

-  Celery message broker and result backend

-  Django Channels layer

#### I also run a local version of Postfix as my email target.

I do this for a number of reasons.

- Running postfix on my server allows me to send emails for my own use without needing to send them through a provider. (Mostly, I'm referring to error-related emails such as the HTTP 500 error emails.) Additionally, since those emails remain local, there's less risk involved with sensitive data that may be in them.

- Using a local connection improves the performance of the Django email functionality. It takes significantly less time to send an email from Django than if Django were connecting to an external email provider.

- If there's any problem with the provider's service or connecting to that service, Postfix will queue the messages and continue to try to send them. Meanwhile, from Django's perspective, the emails have been successfully sent.

- Reduces the number of connections that need to be made to my email provider. Every Django process or thread is only sending locally. The local postfix connection only creates one connection to the email provider.

- Adding Postfix to my email processing gives me yet one more option for tracking and monitoring email usage. The Postfix logs help me track how many emails are being sent and to whom. I also have the ability to prevent emails from going out after they have been generated.

#### I generally use PostgreSQL as the database

But there _are_ situation where I will use Sqlite, especially for the systems I run on a Raspberry Pi.

### Configurations

One of the objectives of this deployment environment is to minimize the damage that can occur if a component is compromised. One of the ways that this is accomplished is by greatly limiting the permissions granted to the accounts being used to run the projects.

For example, if I use an account named `mysite` to run that project, it will only have the ability to write to a small number of specific directories - primarily its unix socket directory, `MEDIA_ROOT`, and its logging directory. It will only be granted read access to everything else it needs.

For a truly secure environment, you may wish to further restrict access by using tools like SELinux or AppArmor. (Note: I've never needed to do this for a Django deployment.)

#### PostgreSQL

I generally don't bother changing the PostgreSQL configuration on a per-project basis. However, I do frequently run multiple projects on a single PostgreSQL instance, and so I might alter the configuration to best support them.

Periodic backups must be considered an absolute requirement - don't neglect them. How you do them and how often you do them is something only you can determine. You need to identify how much data you can afford to lose, and plan accordingly.

I have one system that is updated infrequently (less than once per day on average). I do a daily backup on the database itself. I have another system that sees much more frequent use. For it, I have set up a read-only replica to go along with the daily backups. In both cases, the daily backups are copied to a different system.

### Create directories and grant permissions for static and media files

The sample file `tmpfiles-mysite` ensures that the correct directories are created with the appropriate owners and permissions. This file can be copied to the `/etc/tmpfiles.d` directory. It can immediately be applied with the command `systemd-tmpfiles --create --remove`, otherwise, those directories will be validated every time the system is booted.
(See the tmpfiles docs for a detailed description of the file contents.)

### Configure the web services

Nginx is configured to forward traffic to Django, or to retrieve static files.
Anything else is ignored.

A sample file is in `nginx/sites-available/nginx-mysite`. It's not usable as-is. You need to have your ssl certificate for your domain, and change the path in the SSL directives to match your directory structure. If you use letsencrypt, the paths will be similar to what I'm showing in the sample file.

## Commands issued as `mysite`

### Prepare the project and the virtual environment


### Settings to be made to your settings.py file

* Note: There are many different ways to manage the settings.
  * Environment variables
  * Separate settings files with different names
  * Separate configuration files
  * etc

  This document assumes the simplest case - required changes are made
  directly to the settings.py file.

* Set `STATIC_ROOT` to be `/var/www/<project name>/static/`

  * `STATIC_ROOT=/var/www/mysite/static/`

* Set `MEDIA_ROOT` to be  `/var/www/<project name>/media/`

  * `MEDIA_ROOT=/var/www/mysite/media/`

* Make the other changes to your settings required for deployment as
  shown in the Deployment checklist. This includes setting:
  * `DEBUG=False`
  * `ALLOWED_HOSTS=['<your ip address or DNS name>']`
  * `SECRET_KEY='<new generated key>'`
  * `DATABASES`
  * `CACHES`
  * `LOGGING`
  * `ADMINS`

* If you're using redis as the cache, your `CACHES` setting may look
  like this:
  ```
  CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.redis.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379"
    }
  }
  ```

* Run collectstatic, verifying that your project has
  deployed the static files to the proper directories in
  `/var/www/mysite/static/`.

  * `. ~/ve/bin/activate`
  * `cd /opt/mysite/mysite`
  * `python manage.py collectstatic --noinput -c`
  * `python manage.py migrate --noinput`
