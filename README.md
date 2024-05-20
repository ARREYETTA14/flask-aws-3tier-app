## flask-aws-3tier-app

In today's digital landscape, web applications are essential for delivering interactive and dynamic user experiences. To ensure scalability, security, and efficient management of resources, a robust architecture is necessary. One popular architecture is the 3-tier architecture, which divides the application into three distinct layers: the Presentation Tier, Logic Tier, and Data Tier. This separation allows for modular development, easier maintenance, and improved scalability.

![simple_3-tier_architecture_view](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/6c73a04a-b9d7-429c-8751-a04e03002e3d)

In this project, we will guide you through the process of setting up a 3-tier Flask application in Amazon Web Services (AWS). Flask, a lightweight and powerful web framework for Python, will serve as the backbone of our application. By leveraging AWS's extensive cloud services, we will deploy a scalable and secure web application that can handle a variety of workloads.

# Project Objectives
Presentation Tier: Set up the front-end of the application using HTML, CSS, and JavaScript to create a user-friendly interface for submitting data.
Logic Tier: Implement the business logic using Flask to handle user requests, process data, and interact with the database.
Data Tier: Configure and manage a MariaDB database to store application data securely and efficiently.

# Key Components
AWS EC2 Instances: Deploy each tier on separate EC2 instances to ensure isolation and scalability.
Flask Framework: Utilize Flask to develop the application logic and manage HTTP requests.
MariaDB: Use MariaDB for the relational database to store user information and application data.
Apache HTTP Server: Serve the front-end content using Apache on the Presentation Tier.
By the end of this project, you will have a fully functional 3-tier Flask application deployed on AWS, capable of handling user inputs, processing data, and storing it securely in a database. This setup is ideal for applications that require robust data management and high scalability, such as e-commerce platforms, content management systems, and more.

# Architecture Diagram
Below is the architecture diagram illustrating the setup. You will see the separation of the three tiers, each hosted on its own AWS EC2 instance, communicating with each other to deliver a seamless user experience.

![architecture_diagrame](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/956ef01c-00d2-4373-ba97-6ac3525b9643)

https://private-user-images.githubusercontent.com/112652000/332092913-956ef01c-00d2-4373-ba97-6ac3525b9643.jpg?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTYyMTUxMTYsIm5iZiI6MTcxNjIxNDgxNiwicGF0aCI6Ii8xMTI2NTIwMDAvMzMyMDkyOTEzLTk1NmVmMDFjLTAwZDItNDM3My1iYTk3LTZhYzM1MjViOTY0My5qcGc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNTIwJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDUyMFQxNDIwMTZaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0xZjkyNjM1MDIyYjIyZDAyOTUyNWMyMDMwYTQ2YWVlOTBiOGNlODE1NDJhMGMxMmViOGIyZTgxZTYyOTY0MGEzJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.UgPHRs0gKk6hE5JwIOaEVANFhq4DTBhMbwMGbfyLkVY
