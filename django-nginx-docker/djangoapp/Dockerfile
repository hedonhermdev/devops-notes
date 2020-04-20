# FROM python:3.8.2-alpine

# # set work directory
# WORKDIR /usr/src/app

# # set environment variables
# ENV PYTHONDONTWRITEBYTECODE 1
# ENV PYTHONUNBUFFERED 1

# RUN pip install --upgrade pip

# # Install psycopg2 binaries. 
# RUN apk update \
#     && apk add postgresql-dev gcc python3-dev musl-dev

# # Copy the requirements
# # Copying requirements before code is a good practice because this step can be cached.
# # So the requirements wont be reinstalled unless the requirements file has changed. 
# COPY ./requirements.txt .

# RUN pip install -r requirements.txt

# # Make the home directory for the app user
# RUN mkdir -p /home/app

# # Make an app user add it to the app group
# RUN addgroup -S app && adduser -S app -G app

# # create the appropriate directories
# ENV HOME=/home/app
# ENV APP_HOME=/home/app/web
# RUN mkdir $APP_HOME
# RUN mkdir $APP_HOME/staticfiles
# RUN mkdir $APP_HOME/mediafiles
# WORKDIR $APP_HOME

# # copy entrypoint.sh
# COPY ./entrypoint.sh $APP_HOME

# # copy project
# COPY . $APP_HOME

# # chown all the files to the app user
# RUN chown -R app:app $APP_HOME

# # change to the app user
# USER app

# # run entrypoint.prod.sh
# ENTRYPOINT ["/home/app/web/entrypoint.sh"]

# Pull the image
FROM python:3

# Prevents __pycache__ files
ENV PYTHONUNBUFFERED 1
ENV PYTHONDONTWRITEBYTECODE 1

# Create the home directory for the app user. 
RUN mkdir /home/app

# Create the app user
RUN useradd --user-group --system --no-create-home --no-log-init app

# Create appropriate directories
ENV APP_HOME=/home/app/web
RUN mkdir -p $APP_HOME/staticfiles
RUN mkdir -p $APP_HOME/mediafiles

WORKDIR $APP_HOME

# Copy requirements before code. This prevents downloading the requirements in subsequent builds.
# Unless the requirements.txt file has been modified. 
COPY requirements.txt $APP_HOME
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# Copy the code files
COPY . $APP_HOME

# Make the app user the owner of the $APP_HOME directory
RUN chown -R app $APP_HOME
RUN chmod 701 $APP_HOME

# Script which makes migrations, migrates and collects staticfiles to serve via Nginx.
ENTRYPOINT ["/home/app/web/entrypoint.sh"]


