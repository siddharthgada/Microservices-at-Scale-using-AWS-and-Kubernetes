FROM python:3.10-slim-buster

# Set your app working directory
WORKDIR /analytics

# Install system dependencies for PostgreSQL
RUN apt update -y && apt install build-essential libpq-dev -y
RUN pip install --upgrade pip setuptools wheel -y
RUN pip install -r requirements.txt

# Install Postgres and configure
USER root

ARG DB_USERNAME=$DB_USERNAME
ARG DB_PASSWORD=$DB_PASSWORD
ARG DB_HOST=$DB_HOST
ARG DB_PORT=$DB_PORT
ARG DB_NAME=$DB_NAME

USER postgres
WORKDIR /db
COPY ./db .

RUN service postgresql start && \
psql -c "CREATE USER $DB_USERNAME PASSWORD '$DB_PASSWORD'" && \
psql < 1_create_tables.sql && \
psql < 2_seed_users.sql && \
psql < 3_seed_tokens.sql && \
psql -c "GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO $DB_USERNAME;" && \
psql -c "GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO $DB_USERNAME"

# -- End database setup

# Expose Flask app port
EXPOSE 5153

# Start the database and Flask application
CMD service postgresql start && python app.py