# specify-docker-deployment

This Specify7 deployment is based on the recommended docker based deployment from the 
Specify Collections Consortium. It is available at https://github.com/Specify/docker-compositions/ and should be referred to for application configuration.

The Specify7 application and database are deployed as docker services on 2 diffrent virtual machines.

## Database
On the virutal machine where the database server is to be deployed create folder structure as below.
```
data
├── backups
├── code
└── volumes
    └── database
```
Copy the folder **db** into the application server's **/data/code/** directory.

In the server, create **env/.env_file** file by copying the **env/env_file.template** file.

Edit the **env/.env_file** and set appropriate values.

If the database is to be seeded from an existing database export, copy the previous database dexportump in SQL format into the **/data/code/db/seed-database** directory.

To start the service navigate to /data/code/db folder and execute 
```
docker-compose up -d 
```

## Application
On the virutal machine where the Specify application is to be deployed create folder structure as below.
```
data
├── backups
├── code
└── volumes
    ├── attachments
    ├── specify6
    └── static-files
```
The Specify7 application running in Docker is configured to run under a user/group  **specify** with UID/GID **999**.
So the ownership of the 3 folders under volumes should be changed to **999**.
```
chown -R 999:999 /data/volumes/attachments /data/volumes/specify6 /data/volumes/static-files
```
Copy the folder **app** into the application server's **/data/code/** directory.

Copy SSL certificate and key into **/data/code/app/certs** direcory. 

In the server change the file permissions of the SSL certificate, key and the folder containing them appropriately.
 ```
chmod 644 specify.nrm.se.crt 
chmod 600 specify.nrm.se.key 
chmod 700 certs
```

In the server, create **env/.env_file** file by copying the **env/env_file.template** file.

Edit the **env/.env_file**  and set the appropriate values.

To start the service navigate to /data/code/db folder and execute 
```
docker-compose up -d 
```

## Notes
The SSL certificate, key and .env_file should **NOT** be checked into git unencrypted.

MySQL default port **3306** should to be opened between the two virtual machines to allow the application to connect to the database.

HTTP and HTTPS Port **80** and **443** should be opened on the application server.

