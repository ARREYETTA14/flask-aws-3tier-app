# Flask-aws-3tier-app

In today's digital landscape, web applications are essential for delivering interactive and dynamic user experiences. To ensure scalability, security, and efficient management of resources, a robust architecture is necessary. One popular architecture is the 3-tier architecture, which divides the application into three distinct layers: the Presentation Tier, Logic Tier, and Data Tier. This separation allows for modular development, easier maintenance, and improved scalability.


![simple_3-tier_architecture_view](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/6c73a04a-b9d7-429c-8751-a04e03002e3d)

In this project, we will guide you through the process of setting up a 3-tier Flask application in Amazon Web Services (AWS). Flask, a lightweight and powerful web framework for Python, will serve as the backbone of our application. By leveraging AWS's extensive cloud services, we will deploy a scalable and secure web application that can handle a variety of workloads.

## Project Objectives
Presentation Tier: Set up the front-end of the application using HTML, CSS, and JavaScript to create a user-friendly interface for submitting data.
Logic Tier: Implement the business logic using Flask to handle user requests, process data, and interact with the database.
Data Tier: Configure and manage a MariaDB database to store application data securely and efficiently.

## Key Components
- **AWS EC2 Instances:** Deploy each tier on separate EC2 instances to ensure isolation and scalability.
- **Flask Framework:** Utilize Flask to develop the application logic and manage HTTP requests.
- **MariaDB:** Use MariaDB for the relational database to store user information and application data.
- **Apache HTTP Server:** Serve the front-end content using Apache on the Presentation Tier.

By the end of this project, you will have a fully functional 3-tier Flask application deployed on AWS, capable of handling user inputs, processing data, and storing it securely in a database. This setup is ideal for applications that require robust data management and high scalability, such as e-commerce platforms, content management systems, and more.

## Architecture Diagram
Below is the architecture diagram illustrating the setup. You will see the separation of the three tiers, each hosted on its own AWS EC2 instance, communicating with each other to deliver a seamless user experience.

![architecture_diagrame](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/956ef01c-00d2-4373-ba97-6ac3525b9643)

## Creating the Instances for the Different Tiers
In this scenario, we are going to use the default VPC. We will create a security group which we will use for all three instances with the following inbound rules.

![security_group_rules](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/163244d4-459b-4e9d-a53a-1b8138e59b3c)

**Note:** This setup is for illustration purposes. In a real-world scenario, you would need to tighten your security as per the requirements.

# Configuration Process

### Data Tier

SSH into the data tier instance and run the following commands.

1. **Install MariaDB**:
   ```sh
   sudo yum update -y
   sudo yum install -y mariadb-server

2. **Start and Enable Mariadb Service**:
   ```sh
   sudo systemctl start mariadb
   sudo systemctl enable mariadb
   
3. **Secure MariaDB Installation**:
   ```sh
   sudo mysql_secure_installation

4. **Create a Database and User**:
   - Log in to MariaDB:
     ```sh
     mysql -u root -p

   - Create flaskdb database, a user flaskuser and grant user privilage:
     ```sh
     CREATE DATABASE flaskdb;
     CREATE USER 'flaskuser'@'private_ip_logic_tier' IDENTIFIED BY 'your_password';
     GRANT ALL PRIVILEGES ON flaskdb.* TO 'flaskuser'@'private_ip_logic_tier';
     FLUSH PRIVILEGES;

5. **Configure MariaDB to Accept Remote Connections**:
   - Edit the MariaDB configuration file:
     ```sh
     sudo vi /etc/my.cnf

   - Find the [mysqld] section and add or modify the following line:
     ```sh
     bind-address=0.0.0.0

   - Restart the MariaDB server to effect the changes:
     ```sh
     sudo systemctl restart mariadb

6. **Create User Table**:
   - Log into MariaDB:
     ```sh
     mysql -u root -p
     
   - Select the database you created **flaskdb**
    ```sh
     USE flaskdb;
    ```
    
   - Create a table called **applications** in the database:
     ```sh
     CREATE TABLE applications (
        id INT AUTO_INCREMENT PRIMARY KEY,
        first_name VARCHAR(100),
        last_name VARCHAR(100),
        dob DATE,
        email VARCHAR(100)
     );

   - Verify table creation:
     ```sh
     SHOW TABLES;
     DESCRIBE applications;


### Logic Tier

SSH into the logic tier instance and run the following commands.

1. **Install Flask Dependencies**:
   ```sh
   sudo yum update -y
   sudo yum install python3-pip
   pip3 install Flask mysql-connector-python
   pip3 install flask-cors
   sudo chmod 755 app.py

2. **Create app.py**:
   - Create the app.py then paste the code in the app.py file found in this GitHub repo into the file.

**Note**: Remember to Replace the **hostname** = 'with the private_ip of data tier instance', **user** = 'the user you created when creating database in data tier',    **password** = 'the password of your user' and **database** = 'the name of the database you created in the data tier'.

![sample_connection_string_view_app py](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/b2cf5833-429d-49ba-99c6-ba001bb63cbc)

   
   - Run the Flask application:
     ```sh
     python3 app.py


### Presenter Tier

SSH into the presenter tier instance and run the following commands.

1. **Install and Configure Apache**:
   ```sh
   sudo yum update -y
   sudo yum install httpd -y
   sudo systemctl start httpd 
   sudo systemctl enable httpd

2. **Create index.html**:
   - Create the index.html file in the following directory and paste the code found in the index.html file in GitHub into this created index.html file in your ec2 machine.
   ```sh
   cd /var/www/html/

**Note**: Remember to replace the public_Ip at the connection string line at the level of the **Passport Application Form** section of the html code which is **"http://public_ip_logic_tier_instance:5000/submit"** and at the JavaScript section of the code that uses the Fetch API to make a POST request to **"http://public_ip_logic_tier_instance:5000/submit"** with the Public_Ip of the Logic tier Instance.
![sample_connection_string_view_html](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/a9bddf79-5d74-4efa-b336-b21063da8806)
![sample_connection_string_view_html_1](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/63cd076c-db3e-48d6-bad9-848e20f943bc)

3. **Restart the Apache Server**:
   ```sh
   sudo systemctl restart httpd


### Testing 

1. Copy the public IP of the presenter or frontend instance and paste it into the browser to check if the frontend is reachable.

![frontend_test_view](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/d9dd1f64-b146-481b-ac90-8a1df7c869a6)

2. Input data and try submitting it.

![submit_view](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/a9188b42-801a-4d28-8ebe-a2fec82f21d7)

3. Log into the Database tier, search the flaskdb, and check if the input data was stored in the database:
   ```sh
   mysql -u root -p
   USE flaskdb;
   SELECT * FROM applications;
 
![database_view](https://github.com/ARREYETTA14/flask-aws-3tier-app/assets/112652000/e8940bd1-6290-4fef-a1a5-f1fd5d7bd82b) 



### Things to Note:
When you run the **app.py** logic code, you might try submitting data but find that it doesn't go through. This could be because **app.py** has stopped running. That's after all other connections have been done properly yet, after submitting the datat, it fails. To address this, you'll need to restart it by using the **python3 app.py** command.

Alternatively, to ensure the logic runs continuously, follow these steps:

1. **Create a systemd service file**: Open a terminal and create a new service file for your Flask application:
   ```sh
   sudo nano /etc/systemd/system/flaskapp.service
   
2. **Add the following configuration**: Replace **'/path/to/your/app'** with the actual path to your Flask application. Based on this guide, it's located in the home directory.
   ```sh
   [Unit]
   Description=A simple Flask web application
   After=network.target
   
   [Service]
   User=ec2-user
   WorkingDirectory=/home/ec2-user
   ExecStart=/usr/bin/python3 /home/ec2-user/app.py
   Restart=always
   
   [Install]
   WantedBy=multi-user.target

Make sure to replace **'ec2-user'** with your actual username if it's different.

3. **Reload the systemd daemon**:
   ```sh
   sudo systemctl daemon-reload

4. **Start your Flask service**:
   ```sh
   sudo systemctl start flaskapp

5. **Enable the service to start on boot**:
   ```sh
   sudo systemctl enable flaskapp

6. **Check the status of the service**:
   ```sh
   sudo systemctl status flaskapp
   ```
   
After completing these steps, try inputting your data again, and you should receive a success message.


**PLEASE DO WELL TO REACH ME IN CASE YOU HAVE ANY TROUBLES. I WILL BE VERY GLAD TO HELP**

     

     
   
