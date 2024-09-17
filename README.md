

# Zabbix Monitoring System Deployment on AWS EC2

## Overview
This project outlines the steps to deploy **Zabbix**, an open-source monitoring system, on an **AWS EC2 instance** running **Ubuntu 24.04**. Zabbix is designed to monitor various services and infrastructure, providing a powerful tool for server and network management.

I previously deployed **Nagios**, another open-source monitoring solution, and shared a diagram of that deployment on my [GitHub Repository](your-github-repo-link). In this project, I demonstrate the installation and configuration process for Zabbix.

## Zabbix Installation Steps

### 1. **Update System**
Update your system to ensure all packages are up-to-date.
```bash
sudo apt update
sudo apt upgrade
```

### 2. **Install Apache, MySQL, and PHP**
Zabbix requires a web server, database, and PHP.
```bash
sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php
```

### 3. **Configure MySQL Database**
Set up the Zabbix database and user.
```bash
sudo mysql -u root
```
Then, run the following SQL commands:
```sql
CREATE DATABASE zabbix CHARACTER SET utf8 COLLATE utf8_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'Y0ur_Pa$$word';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
SET GLOBAL log_bin_trust_function_creators = 1;
FLUSH PRIVILEGES;
EXIT;
```
Replace `Y0ur_Pa$$word` with a strong password.

### 4. **Install Zabbix Server**
Download and install Zabbix server packages.
```bash
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-2+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_7.0-2+ubuntu24.04_all.deb
sudo apt update
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```

### 5. **Import Zabbix Database Schema**
Load the Zabbix schema into the database.
```bash
sudo zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

### 6. **Configure Zabbix Server**
Edit the Zabbix configuration file.
```bash
sudo nano /etc/zabbix/zabbix_server.conf
```
Update the database details:
```
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=your_password
```

### 7. **Start Zabbix Server and Agent**
Start Zabbix services and enable them at boot.
```bash
sudo systemctl start zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

### 8. **Configure PHP for Zabbix Frontend**
Set the correct timezone for PHP in the Zabbix configuration.
```bash
sudo nano /etc/zabbix/apache.conf
```
Update or add the line:
```
php_value date.timezone Europe/London
```
Replace `Europe/London` with your local timezone.

### 9. **Restart Apache Server**
Apply the changes by restarting Apache.
```bash
sudo systemctl restart apache2
```

### 10. **Open Firewall Ports**
Ensure Zabbix and Apache ports are open.
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 10051/tcp
```

### 11. **Setup Zabbix Frontend**
Go to `http://your_server_ip/zabbix` in your browser to complete the setup, using:
- **Username:** `Admin`
- **Password:** `zabbix`

## Nagios Deployment Diagram
The following diagram illustrates my **Nagios** open-source monitoring deployment on AWS EC2:
![image alt](https://github.com/Mk-Lawal/Zabbix-aws-ec32-Monitoring/blob/c7ca382bcbde68622ccd56e4ad70f0959036be4b/Zabbix.png)

## Next Steps
To expand your monitoring capabilities, you can:
- Install and configure Zabbix agents on remote hosts.
- Use Zabbix agents on **Windows** and **Linux** systems for detailed monitoring.

## Conclusion
Congratulations! You have successfully deployed **Zabbix** on AWS EC2. Feel free to explore Zabbix’s robust monitoring features and adjust the configuration to meet your specific needs.

