<div style="display: flex; align-items: center;">
  <img src="../img/higher_resolution_img (2).jpg" alt="Alt text" style="width: 115px; margin-right: 20px; border-radius: 10px; box-shadow: 5px 5px 15px rgba(0, 0, 0, 0.5);">
  <div style="font-family: Arial, sans-serif; font-size: 40px;">
    <span>InsightMonitor</span>
    <span style="color: gray;"> Documentation</span>
  </div>
</div>

&nbsp;

### Welcome
Welcome to the documentation for **InsightMonitor**, a model monitoring platform designed for easy deployment within AWS environments. This documentation will:  

- Guide you through the setup process, configuration, and secure operation of the platform using **Docker** and AWS infrastructure.
- Detail the navigation components of the Model Monitoring app including setting up internal app configurations, creating projects, and managing the created assets.
- Document the tests being performed in the application.

<br>

### Django Configuration (Within Docker)
The Django application is pre-configured and bundled within the Docker container. However, users can still customize key settings to match their specific environment using **environment variables**. This approach allows flexibility without modifying the container itself.

Key configurable settings include:

- **Security Keys**: Configure the secret key and other cryptographic elements securely.
- **Database Connections**: Set up the database engine, user credentials, and connection details.
- **Allowed Hosts**: Define the allowed hosts for your application to ensure secure operation.

Environment variables enable you to configure these settings across different environments, such as development and production. Refer to the **Django Setup** section for more detailed instructions on the required environment variables and how to customize your setup.

In addition to configuration, the platform includes:

- **Logging & Debugging**: Pre-configured logging to track errors and debugging information in both development and production environments.
- **Security Measures**: Built-in security configurations, including **end-to-end encryption**, to protect sensitive data and ensure compliance with regulations like **PHI**.


### Infrastructure Setup
For infrastructure deployment, **AWS CloudFormation** can be used to automate the setup of resources like VPC, EC2 instances, S3 buckets, and more. Templates and scripts are provided to assist with the setup.  

If multi-cloud support is considered in the future, **Terraform** offers greater flexibility for deploying across different cloud platforms. However, this documentation focuses on AWS CloudFormation to manage AWS infrastructure.  

### Key Configuration Areas:

- **Authentication & Authorization**: Configure user roles, permissions, and policies to ensure security, with support for integrating existing systems like **Azure AD** or **LDAP**.  
- **Secrets Management**: Secure access to secrets using **AWS Secrets Manager** or similar tools, with a focus on role-based access control and policy management.  
- **NGINX Configuration**: Set up **NGINX** as a reverse proxy for secure traffic routing. Templates are available to help you adapt the configuration to your security standards.  
- **Performance Optimization**: The platform is optimized for normal monitoring tasks using **Polars** to efficiently handle datasets within memory limits. For larger-than-memory monitoring, **Dask** or **DuckDB** can be utilized to scale the platform.  
- **Security Protocols**: The platform provides **end-to-end encryption** for sensitive data, including configurations for **PHI** (Protected Health Information) compliance. Encryption can be globally enforced or configured on a per-project basis.  

### Getting Started
For new users, we recommend starting with the **Quickstart Guide** for an overview of basic setup and configuration. This includes setting up the Docker container, configuring environment variables, and deploying the application. Advanced topics such as **custom authentication integration**, **NGINX configuration**, and **role-based access control** can be found in their respective sections.

Refer to the side navigation for detailed instructions on each topic.
