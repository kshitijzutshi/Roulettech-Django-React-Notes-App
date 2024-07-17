# Backend Setup

1. pip3 install -r requirements.txt
2. django-admin startproject backend
3. cd backend
4. python manage.py startapp api
5. backend->settings.py add following additional lines 
    ```
    from datetime import timedelta
    from dotenv import load_dotenv
    import os

    load_dotenv()
    ...

    ALLOWED_HOSTS = ["*"] # to allow any host

    ...

    # for JWT stuff

    REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": (
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ),
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticated",
    ],
    }

    SIMPLE_JWT = {
        "ACCESS_TOKEN_LIFETIME": timedelta(minutes=30),
        "REFRESH_TOKEN_LIFETIME": timedelta(days=1),
    }

    # add installed apps 

    INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "api",
    "rest_framework",
    "corsheaders",
    ]

    # add middleware for CORS

    "corsheaders.middleware.CorsMiddleware",

    ...

    # end of file

    CORS_ALLOW_ALL_ORIGINS = True
    CORS_ALLOWS_CREDENTIALS = True
    ```
6. api->create serializer.py for jwt tokens
7. view.py -> to create a view for new user
8. backend->urls.py
9. python manage.py makemigrations
10. python manage.py migrate
11. Run server - python manage.py runserver

# Frontend Setup

1. In base directory, npm create vite@latest frontend -- --template react
2. npm install axios for network requests
3. npm install react-router-dom
4. npm install jwt-decode
5. Clean up src - remove css files; app.jsx body; main.jsx css imports
6. In src -> constants.js; api.js; .env file
7. npm run dev

# Deployment

To deploy your website to AWS, following the basic and bonus requirements, you'll need to perform several steps. This guide assumes you have an AWS account and the AWS CLI installed and configured.

### Basic Deployment

#### Deploy Frontend to AWS S3

1. Create an S3 Bucket:

- Go to the S3 service in the AWS Management Console.
- Create a new bucket with a unique name and disable 'Block all public access' settings. Acknowledge that the bucket will be publicly accessible.

2. Configure the Bucket for Website Hosting:

- In your bucket properties, enable 'Static website hosting'. Set the index document as index.html.

3. Deploy Your Frontend Code:

- Build your frontend project if necessary.
- Use the AWS CLI to upload your build directory to the S3 bucket:
```
aws s3 sync ./frontend/dist s3://your-bucket-name --acl public-read
```

4. Update DNS Records:

If you have a domain, update your DNS records to point to the S3 website endpoint provided by AWS.

#### Deploy Backend to AWS EC2

1. Launch an EC2 Instance:

- Go to the EC2 dashboard and launch a new instance.
- Choose an AMI (Amazon Machine Image), like Amazon Linux 2, and an instance type.
- Configure instance details as needed. For public access, ensure it's in a public subnet and assign a public IP.

2. SSH into Your EC2 Instance:

- Once the instance is running, connect to it using SSH with the key pair you specified during setup.

3. Deploy Your Backend Code:

- Transfer your backend code to the EC2 instance using SCP or a Git repository.
- Install any necessary dependencies, such as Python, Django, etc.
- Run your backend application, ensuring it listens on 0.0.0.0 to be accessible outside the EC2 instance.

4. Configure Security Group:

- In the EC2 dashboard, configure the security group of your instance to allow inbound traffic on the port your backend is running on (e.g., 8000).

### Bonus Deployment

#### Use AWS CloudFront for CDN

1. Create a CloudFront Distribution:
- Go to the CloudFront service in the AWS Management Console.
- Create a new distribution, setting the origin to your S3 bucket's website endpoint for the frontend and your EC2 instance for the backend.
- Configure additional settings as needed, such as default cache behavior and distribution settings.

#### Create a Custom VPC

1. Create a VPC:

- Go to the VPC dashboard in the AWS Management Console.
- Create a new VPC with a CIDR block (e.g., 10.0.0.0/16).

2. Create a Private Subnet:

- Within your VPC, create a new subnet that will serve as the private subnet. Specify a CIDR block (e.g., 10.0.1.0/24).

3. Launch EC2 in Private Subnet:

- When launching a new EC2 instance for your backend, specify your custom VPC and the private subnet.
- Since the instance is in a private subnet, consider setting up a bastion host or a NAT Gateway for SSH access and internet connectivity, respectively.

4. Update Route Tables:

- Ensure your VPC's route tables are correctly configured to allow traffic to and from the internet as necessary, especially if using a NAT Gateway.


