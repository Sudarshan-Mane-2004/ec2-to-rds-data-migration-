# 📦 Data Migration Between EC2 and RDS

## 🧭 Overview

This documentation explains how to migrate a database from an EC2 instance (MariaDB) to an Amazon RDS (MySQL) database.  
It includes all steps — from installing MariaDB to creating the database, backup, and restoring it into RDS — written in easy and simple English.

---

### ☁️ 1️⃣ What is EC2 and RDS?

**🖥️ EC2 (Elastic Compute Cloud):**  
A cloud-based virtual server where you can host websites, databases, or applications.

**🗄️ RDS (Relational Database Service):**  
A managed AWS database that automatically handles storage, backups, and scaling.

*You can think of EC2 as your computer in the cloud and RDS as your ready-to-use cloud database.*

---

### 💡 2️⃣ Why We Do Data Migration

Data migration is done to:

- ✅ Move from self-managed MariaDB to managed RDS
- ✅ Improve database performance and reliability
- ✅ Enable automated backups and easier maintenance

---

### ⚙️ 3️⃣ Prerequisites

Before starting:

- AWS account with EC2 and RDS access
- A running EC2 instance with MariaDB installed
- A created RDS instance (MySQL)
- Security groups that allow:
  - **EC2 → Outbound port 3306**
  - **RDS → Inbound port 3306** (from EC2 security group)

---

### 🪜 4️⃣ Steps to Migrate Data

#### 🔹 Step 1: Launch EC2 Instance

1. Open the AWS Management Console
2. Go to EC2 → Instances → Launch Instance
3. Choose Amazon Linux 2
4. Select instance type (t2.micro for free tier)
5. Add key pair for SSH
6. Launch your instance ✅

#### 🔹 Step 2: Connect to EC2

Use SSH from your terminal:

```sh
ssh -i your-key.pem ec2-user@<ec2-public-ip>
```

#### 🔹 Step 3: Install MariaDB on EC2

Install and start MariaDB:

```sh
sudo yum install mariadb105-server -y
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

✅ MariaDB service is now active.

#### 🔹 Step 4: Create a Database and Table

Login to MariaDB:

```sh
sudo mysql -u root -p
```

Create a new database and table:

```sql
CREATE DATABASE studentdb;
USE studentdb;

CREATE TABLE students (
  rollnumber int primary key,
  name varchar(50),
  contact varchar(15),
  address varchar(100)
);
```

Insert some sample records:

```sql
INSERT INTO students VALUES 
(1, 'Sudarshan Mane', '9579979563', 'Latur'),
(2, 'Avinash Zalake', '7028100702', 'Pune'),
(3, 'Alankar Jadhav', '8767344211', 'Mumbai'),
(4, 'Varad Thorat', '9012345678', 'S.Nagar'),
(5, 'Samarath Shinde', '9000000000', 'Beed');
```

🎉 Your `studentdb` database and `students` table are ready.

#### 🔹 Step 5: Create Database Backup in EC2

Create a backup file named `studentdbbackup.sql`:

```sh
mysqldump -u root -p studentdb > studentdbbackup.sql
```

💾 This file (`studentdbbackup.sql`) contains all your table structure and data.

#### 🔹 Step 6: Create RDS Instance

1. Go to AWS Console → RDS → Create Database
2. Select MySQL as the engine
3. Choose Free Tier
4. Set:
   - DB Name: studentdb
   - Master Username: admin
   - Master Password: your password
5. Under Connectivity, allow your EC2 instance security group
6. Click **Create Database** ✅

#### 🔹 Step 7: Restore Backup into RDS

Now connect to your RDS instance from EC2:

```sh
mysql -h <rds-endpoint> -u admin -p
```
create a database for restoration inside admin 

Then restore your backup:

```sh
mysql -h <rds-endpoint> -u admin -p studentdb < studentdbbackup.sql
```

✅ Your database from EC2 is now migrated into RDS.

#### 🔍 8️⃣ Check Data in RDS

Verify the data inside RDS:

```sh
mysql -h <rds-endpoint> -u admin -p
USE studentdb;
select * from students;
```

You should see all five records from EC2 inside RDS 🎯

---

### 🔐 9️⃣ Security Tips

- Use strong passwords for your RDS users
- Disable public access for RDS
- Regularly take RDS snapshots
- Allow access only from your EC2 instance security group

---

### 🧾 🔟 Summary

| Step | Description                        |
|------|------------------------------------|
| 1    | Launch EC2 instance                |
| 2    | Install MariaDB                    |
| 3    | Create studentdb and students table|
| 4    | Insert records                     |
| 5    | Backup database as studentdbbackup.sql |
| 6    | Create RDS instance                |
| 7    | Restore backup into RDS            |
| 8    | Verify migrated data               |

---

## 📘 Conclusion

You successfully migrated your MariaDB database from EC2 to Amazon RDS using a backup and restore method.  
Now your data is safe, scalable, and easy to manage in AWS. 🌟

---

### ✍️ Author Info

- 👤 Created By: Sudarshan Mane
- 🕒 Date: November 2025
- 💻 Project: EC2 to RDS Data Migration
- 🧩 Tools Used: AWS EC2, RDS, MariaDB, MySQL
