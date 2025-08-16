# Use the official Python image from Docker Hub
FROM python:3.12-slim-bullseye

# Set environment variables
ENV DB_USERNAME=myuser
ENV DB_PASSWORD=mypassword
ENV DB_NAME=mydatabase

# Set the working directory in the container
WORKDIR /usr/src/app

COPY . . 

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    python3-dev \
    && rm -rf /var/lib/apt/lists/*

# Install project dependencies
RUN python -m ensurepip --upgrade \
    && python -m pip install --upgrade pip setuptools wheel \
    && python -m pip install --no-cache-dir -r requirements.txt

# Run the application
CMD ["python", "app.py"]

EXPOSE 5153