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


# AWS 3-Tier Architecture – Passport Application System modified with RDS

# 📌 Overview

This project implements a complete 3-tier architecture on AWS using:

- **Presentation Tier** → Apache (EC2)

- **Logic Tier** → Flask API (EC2)

- **Data Tier** → MariaDB (RDS)

The application allows users to submit a passport application form through a web interface. The data is stored securely in an **RDS database**.

## 🏗 Architecture Flow
```java
Browser
   ↓
Presentation Tier (Apache - Port 80)
   ↓ Reverse Proxy
Logic Tier (Flask - Port 5000 - Private IP)
   ↓
RDS MariaDB (Port 3306 - Private)
```


## PHASE 1 — CREATE SECURITY GROUPS (Do This First)

Go to **EC2** → **Security Groups** → **Create 3 groups**.

### 1️) Presentation Security Group

**Name**: ```presentation-sg```

**Inbound Rules**:

- HTTP (80) → 0.0.0.0/0

- SSH (22) → Your IP only

**Outbound**:

Allow all (default)

### 2️) Logic Security Group

**Name**: logic-sg

**Inbound**:

Port 5000 → Source = `presentation-sg`

SSH (22) → Your IP only

**Outbound**:

Allow all

### 3️) RDS Security Group

**Name**: `rds-sg`

**Inbound**:

MySQL/Aurora (3306) → Source = `logic-sg`

**Outbound**:

Default

## PHASE 2 — CREATE RDS (MariaDB)

Go to **RDS** → **Create database**

- Engine: MariaDB
- Template: Free tier
- DB Identifier: `flask-mariadb-db`
- Master Username: `admin`
- Password: `choose strong password`

**Connectivity:**

- VPC: default
- Public access: **NO**
- Security Group: select `rds-sg`

**Database name**:

- `flaskdb`

Do not connect to ec2 compute service. You will do it manually

Click Create.

Wait until status = **Available**.

Copy the **RDS endpoint**.

## PHASE 3 — CREATE EC2 INSTANCES

### 4️) Create Logic Tier Instance

- Amazon Linux 2023
- Security Group: `logic-sg`

Launch.

After running:
Copy its **Private IP**.

### 5️) Create Presentation Tier Instance

- Amazon Linux 2023
- Security Group: presentation-sg

Launch.

Copy its **Public IP**.

## PHASE 4 — CONFIGURE LOGIC TIER (Flask Server)

### 6️) SSH into Logic Tier

### 7️) Install Required Packages

```bash
sudo dnf update -y
sudo dnf install python3 -y
sudo dnf install python3-pip -y
sudo dnf install mariadb105 -y
pip3 install flask flask-cors mysql-connector-python
```

### 8️) Create Flask App

```bash
nano app.py
```
Paste:

```python
import os
from flask import Flask, request, jsonify
from flask_cors import CORS
import mysql.connector
from mysql.connector import Error

# -------------------------
# Flask app setup
# -------------------------
app = Flask(__name__)
CORS(app)  # Allow cross-origin requests from Presentation Tier

# -------------------------
# Database connection
# -------------------------
def create_connection():
    """
    Create and return a connection to the RDS MariaDB database.
    Environment variables must be set:
    DB_HOST, DB_USER, DB_PASSWORD, DB_NAME
    """
    try:
        connection = mysql.connector.connect(
            host=os.environ.get("DB_HOST"),
            user=os.environ.get("DB_USER"),
            password=os.environ.get("DB_PASSWORD"),
            database=os.environ.get("DB_NAME")
        )
        return connection
    except Error as e:
        print(f"Database connection failed: {e}")
        return None

# -------------------------
# Submit route
# -------------------------
@app.route('/submit', methods=['POST'])
def submit_form():
    """
    Handles form submission from Presentation Tier.
    Expects: firstName, lastName, dob, email in form data.
    Returns JSON success/failure.
    """
    try:
        first_name = request.form.get('firstName')
        last_name = request.form.get('lastName')
        dob = request.form.get('dob')
        email = request.form.get('email')

        # Validate input
        if not all([first_name, last_name, dob, email]):
            return jsonify({"success": False, "message": "All fields are required"}), 400

        # Connect to DB
        connection = create_connection()
        if not connection:
            return jsonify({"success": False, "message": "Database connection failed"}), 500

        cursor = connection.cursor()
        insert_query = """
            INSERT INTO applications (first_name, last_name, dob, email)
            VALUES (%s, %s, %s, %s)
        """
        cursor.execute(insert_query, (first_name, last_name, dob, email))
        connection.commit()
        cursor.close()
        connection.close()

        return jsonify({"success": True, "message": "Application submitted successfully"}), 200

    except Exception as e:
        print(f"Error processing request: {e}")
        return jsonify({"success": False, "message": str(e)}), 500

# -------------------------
# Health check route (optional)
# -------------------------
@app.route('/health', methods=['GET'])
def health_check():
    return jsonify({"status": "Logic tier running"}), 200

# -------------------------
# Main
# -------------------------
if __name__ == "__main__":
    # Flask will listen on port 5000 for requests from Presentation EC2
    app.run(host="0.0.0.0", port=5000, debug=False)
```

### 9️) Configure Environment Variables (Temporary)

```bash
export DB_HOST=your-rds-endpoint
export DB_USER=admin
export DB_PASSWORD=yourpassword
export DB_NAME=flaskdb
```

### 10) Create Table in RDS

Connect:

```bash
mysql -h your-db-endpoint -P 3306 -u admin -p --ssl
```
Then create Database and a Table:

```sql
USE flaskdb;
```

```sql
CREATE TABLE applications (
   id INT AUTO_INCREMENT PRIMARY KEY,
   first_name VARCHAR(100),
   last_name VARCHAR(100),
   dob DATE,
   email VARCHAR(100)
);
```
Exit.

### 11) Run Flask

```bash
python3 app.py
```

### 12️) Create systemd Service

```bash
sudo nano /etc/systemd/system/flaskapp.service
```
paste:

```ini
[Unit]
Description=Flask App
After=network.target

[Service]
User=ec2-user
WorkingDirectory=/home/ec2-user
ExecStart=/usr/bin/python3 /home/ec2-user/app.py
Environment="DB_HOST=your-rds-endpoint"
Environment="DB_USER=admin"
Environment="DB_PASSWORD=yourpassword"
Environment="DB_NAME=flaskdb"
Restart=always

[Install]
WantedBy=multi-user.target
```
- Replace the `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_NAME` with the actual credentials

Then:

```bash
sudo systemctl daemon-reload
sudo systemctl start flaskapp
sudo systemctl enable flaskapp
```
Check:

```bash
sudo systemctl status flaskapp
```

## PHASE 5 — CONFIGURE PRESENTATION TIER (Apache + Reverse Proxy)

### 13️) SSH into Presentation Tier

### 14️) Install Apache 

```bash
sudo yum update -y
sudo yum install httpd -y
```

### 15️) Enable Apache

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

### 16️) Configure Reverse Proxy

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

- Scroll to the bottom.
- Add:

```apache
<VirtualHost *:80>
    DocumentRoot /var/www/html

    # Enable proxy modules (make sure they exist)
    LoadModule proxy_module modules/mod_proxy.so
    LoadModule proxy_http_module modules/mod_proxy_http.so

    # Proxy /submit to Logic EC2 Flask app
    ProxyPass /submit http://<LOGIC_PRIVATE_IP>:5000/submit
    ProxyPassReverse /submit http://<LOGIC_PRIVATE_IP>:5000/submit

    # Optional: allow CORS
    <Location /submit>
        Require all granted
    </Location>
</VirtualHost>
```
- Replace LOGIC_PRIVATE_IP.

Save.

Restart Apache:

```bash
sudo systemctl restart httpd
```
## PHASE 6 — ADD HTML FILE

### 17️) Add HTML File

```bash
sudo nano /var/www/html/index.html
```

Paste:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Passport Application Form</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f6f9;
            margin: 0;
            padding: 0;
        }

        .container {
            width: 90%;
            max-width: 500px;
            margin: 50px auto;
            background: #ffffff;
            padding: 25px;
            border-radius: 8px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }

        h2 {
            text-align: center;
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-top: 15px;
            font-weight: bold;
        }

        input {
            width: 100%;
            padding: 8px;
            margin-top: 5px;
            border-radius: 4px;
            border: 1px solid #ccc;
        }

        button {
            margin-top: 20px;
            width: 100%;
            padding: 10px;
            background-color: #0073e6;
            color: white;
            border: none;
            border-radius: 4px;
            font-size: 16px;
            cursor: pointer;
        }

        button:hover {
            background-color: #005bb5;
        }

        .error {
            color: red;
            font-size: 13px;
        }

        .success {
            color: green;
            text-align: center;
            margin-top: 15px;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>Passport Application Form</h2>

    <!-- Notice: No more public IP here -->
    <form id="passportForm" method="POST">

        <label for="firstName">First Name</label>
        <input type="text" id="firstName" name="firstName">
        <span id="firstNameError" class="error"></span>

        <label for="lastName">Last Name</label>
        <input type="text" id="lastName" name="lastName">
        <span id="lastNameError" class="error"></span>

        <label for="dob">Date of Birth</label>
        <input type="date" id="dob" name="dob">
        <span id="dobError" class="error"></span>

        <label for="email">Email</label>
        <input type="email" id="email" name="email">
        <span id="emailError" class="error"></span>

        <button type="submit">Submit Application</button>

        <div id="responseMessage"></div>

    </form>
</div>

<script>
document.getElementById("passportForm").addEventListener("submit", function(event) {
    event.preventDefault();

    if (validateForm()) {
        submitForm();
    }
});

function validateForm() {
    let isValid = true;

    document.querySelectorAll(".error").forEach(el => el.innerText = "");

    const firstName = document.getElementById("firstName").value.trim();
    const lastName = document.getElementById("lastName").value.trim();
    const dob = document.getElementById("dob").value.trim();
    const email = document.getElementById("email").value.trim();

    if (!firstName) {
        document.getElementById("firstNameError").innerText = "First name is required";
        isValid = false;
    }

    if (!lastName) {
        document.getElementById("lastNameError").innerText = "Last name is required";
        isValid = false;
    }

    if (!dob) {
        document.getElementById("dobError").innerText = "Date of birth is required";
        isValid = false;
    }

    if (!email) {
        document.getElementById("emailError").innerText = "Email is required";
        isValid = false;
    } else if (!isValidEmail(email)) {
        document.getElementById("emailError").innerText = "Invalid email format";
        isValid = false;
    }

    return isValid;
}

function isValidEmail(email) {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return regex.test(email);
}

function submitForm() {
    const formData = new FormData(document.getElementById("passportForm"));

    /* Notice this is now a RELATIVE PATH */
    fetch('/submit', {
        method: 'POST',
        body: formData
    })
    .then(response => response.json())
    .then(data => {
        const responseDiv = document.getElementById("responseMessage");

        if (data.success) {
            responseDiv.className = "success";
            responseDiv.innerText = "Application submitted successfully!";
            document.getElementById("passportForm").reset();
        } else {
            responseDiv.className = "error";
            responseDiv.innerText = "Error: " + data.message;
        }
    })
    .catch(error => {
        document.getElementById("responseMessage").className = "error";
        document.getElementById("responseMessage").innerText = "Network error occurred.";
        console.error(error);
    });
}
</script>

</body>
</html>
```

Save.

Restart Apache:

```bash
sudo systemctl restart httpd
```

## PHASE 7 — TEST FULL FLOW

Open browser:

```bash
http://PRESENTATION_PUBLIC_IP
```

Fill and `Submit` form.

Flow:

```scss
Presentation (80)
→ Apache proxy
→ Logic private IP (5000)
→ RDS (3306)
```

## PHASE 8 — VERIFY DATA

- SSH into Logic Tier and connect to RDS:

```bash
mysql -h your-rds-endpoint -P 3306 -u admin -p --ssl
```

After logging in, run:

```sql
USE flaskdb;
```

Then:

```sql
SELECT * FROM applications;
```

# Boom!!!. You should see your submitted record. 





**PLEASE DO WELL TO REACH ME IN CASE YOU HAVE ANY TROUBLES. I WILL BE VERY GLAD TO HELP**

     

     
   
