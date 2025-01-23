# Use an official Python runtime as a base image
FROM python:3.9-slim-buster

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    DJANGO_SETTINGS_MODULE=terraform_resource_creation.settings

# Install system dependencies and Terraform
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    unzip \
    gcc \
    python3-dev \
    sqlite3 \
    && wget https://releases.hashicorp.com/terraform/1.5.7/terraform_1.5.7_linux_amd64.zip \
    && unzip terraform_1.5.7_linux_amd64.zip \
    && mv terraform /usr/local/bin/ \
    && rm terraform_1.5.7_linux_amd64.zip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Set work directory
WORKDIR /app/backend

# Copy Django project
COPY terraform_azure_backend/ .

# Install Python dependencies
RUN pip install --no-cache-dir \
    django \
    djangorestframework \
    psycopg2-binary \
    azure-identity \
    azure-mgmt-resource

# Create entrypoint script
RUN echo "#!/bin/sh\n\
python manage.py migrate --noinput\n\
exec \"\$@\"" > entrypoint.sh \
    && chmod +x entrypoint.sh

# Expose the port the app runs on
EXPOSE 8000

# Set entrypoint and command
ENTRYPOINT ["./entrypoint.sh"]
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]