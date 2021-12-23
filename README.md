# db_ecogomi
#install mysql and set password

```
sudo apt install mariadb-server
sudo mysql_secure_installation
```
open the database
```
sudo mysql -u root -p
```
create a database and pass:
```
CREATE DATABASE attendancesystem;
CREATE USER 'attendanceadmin'@'localhost' IDENTIFIED BY 'pimylifeup';
GRANT ALL PRIVILEGES ON attendancesystem.* TO 'attendanceadmin'@'localhost';
use attendancesystem;

create table attendance(
   id INT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE,
   user_id INT UNSIGNED NOT NULL,
   clock_in TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
   PRIMARY KEY ( id )
);

create table users(
   id INT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE,
   rfid_uid VARCHAR(255) NOT NULL,
   name VARCHAR(255) NOT NULL,
   created TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
   PRIMARY KEY ( id )
);
```
sudo pip3 install mysql-connector-python

mkdir ~/attendancesystem

nano ~/attendancesystem/save_user.py

```

  
  
  nano ~/attendancesystem/check_attendance.py
  
```  
#!/usr/bin/env python
import time
import RPi.GPIO as GPIO
from mfrc522 import SimpleMFRC522
import mysql.connector


db = mysql.connector.connect(
  host="localhost",
  user="ecogomiadmin",
  passwd="Hasan1546",
  database="ecogomi"
)

cursor = db.cursor()
reader = SimpleMFRC522()


try:
  while True:
    print('Place Card to\nrecord attendance')
    id, text = reader.read()

    cursor.execute("Select id, name FROM users WHERE rfid_uid="+str(id))
    result = cursor.fetchone()

    if cursor.rowcount >= 1:
      print("Welcome " + result[1])
      cursor.execute("INSERT INTO attendance (user_id) VALUES (%s)", (result[0],) )
      db.commit()
    else:
      print("User does not exist.")
      print("Create new user?")
      overwrite = input("Create (Y/N)? ")
      if overwrite[0] == 'Y' or overwrite[0] == 'y':
        print("Creating user.")
        time.sleep(1)
        sql_insert = "INSERT INTO users (name, rfid_uid) VALUES (%s, %s)"
        print("Enter new name")
        new_name = input("Name: ")
        cursor.execute(sql_insert, (new_name, id))
        db.commit()
        print("User " + new_name + "\nSaved")
      else:
        continue;
    time.sleep(2)
finally:
  GPIO.cleanup()

```
  
 sudo mysql -u root -p
 use attendancesystem;
  
SELECT * FROM users;
SELECT * FROM attendance;
sudo apt-get install nginx
sudo systemctl start nginx
sudo apt-get install php7.4-fpm php7.4-mbstring php7.4-mysql php7.4-curl php7.4-gd php7.4-curl php7.4-zip php7.4-xml -y

sudo nano /etc/nginx/sites-enabled/default

index index.html index.htm;-------->index index.php index.html index.htm;

location ~ \.php$ {
               include snippets/fastcgi-php.conf;
               fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        }
sudo systemctl reload nginx

sudo mkdir /var/www/html/attendance
sudo git clone https://github.com/pimylifeup/attendance-system-frontend.git /var/www/html/attendance
sudo nano /var/www/html/attendance/common.php



        
