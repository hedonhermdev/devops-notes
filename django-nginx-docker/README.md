# Django, PostgreSQL, Nginx on Docker
Deploying a Django app with a PostgreSQL database on Docker containers. Suitable for production(like) environments. This folder contains a minimal Django app along with the Dockerfiles and the docker-compose.yaml

# Resources

- Django on Docker: https://docs.docker.com/compose/django/
- PostgreSQL, Django, Nginx on Docker: https://testdriven.io/blog/dockerizing-django-with-postgres-gunicorn-and-nginx/

# Notes

- Make sure you make entrypoint.sh executable.

    ```bash
    $ sudo chmod +x entrypoint.sh
    ```
- The static and media directories need to be made separately because otherwise the `collectstatic` command fails because of permission issues. 


