# specify-docker-deployment

This project provides an Ansible playbook for deploying [Specify7](https://www.specifysoftware.org/products/specify-7/), a web-based biological collections data management platform. It leverages Docker for deployment based on the recommended setup by the Specify Collections Consortium. For application configuration, refer to the official repository: [Specify Docker Compositions](https://github.com/Specify/docker-compositions/).

The Specify7 application and its database are managed as Docker services using the Ansible playbook in this repository. The project contains Ansible playbooks and roles to configure the application, database, and reverse proxy. It supports the deployment and management of multiple Specify7 instances on the same or different hosts, with each instance having its own configuration settings (e.g., database names, domain names, asset folders, and cron job schedules).

## Project Structure

- **Inventory Files**: Configurations for each Specify7 instance are stored in inventory files located in the **inventories** folder. To install a new Specify7 instance, copy an existing inventory file, modify it as necessary, and save it with a new name.

- **Ansible Vault**: Sensitive data like database credentials are stored in an Ansible vault located at **group_vars/all/vault.yml**. You can edit this vault file using the following command:
```bash
export EDITOR=nano; ansible-vault edit ansible/group_vars/all/vault.yml
 ```
## Setting Up the Database

To set up a database for a specific Specify7 instance, run the **specify-db.yml** playbook, specifying the appropriate inventory file. This playbook will execute the **specify_db** role.
```
ansible-playbook --ask-become-pass --ask-vault-pass -i inventories/demo.ini specify-db.yml
```
### Tasks Performed
- Retrieve and configure secret variables specific to the Specify7 database instance.
- Create the required directory structure on the host.
- Copy environment files.
- Set up the Docker Compose file.
- Configure backup scripts.
- Schedule a crontab job for backups.

**Note:** If seeding the database from a previous export, place the SQL dump in the */data/code/specify_instance/seed-database/* directory.

To start the database service, navigate to the */data/code/specify_instance/* directory and run:
```
docker-compose up -d 
```

## Setting Up the Specify7 Application
To configure the Specify7 application for a given instance, run the **specify-app.yml** playbook, specifying the relevant inventory file. This playbook executes the **specify_db** role.
```
ansible-playbook --ask-become-pass --ask-vault-pass -i inventories/demo.ini specify-app.yml
```
### Tasks Performed
- Retrieve and configure secret variables specific to the Specify7 application instance.
- Dynamically create the application environment.
- Create the necessary directory structure on the host.
- Copy environment files.
- Install required libraries.
- Set up the Docker Compose file.

## Setting Up Reverse Proxy / Webserver
To configure a reverse proxy or webserver using Nginx, run the **nginx-webserver.yml** playbook with the relevant inventory file. This playbook will execute the **nginx-webserver** role.
```
ansible-playbook --ask-become-pass --ask-vault-pass -i inventories/demo.ini nginx-webserver.yml
```
### Tasks Performed
- Create the necessary directory structure on the host.
- Copy configuration files.
- Set up the Docker Compose file.
- Manage volume definitions in the compose file for shared usage.

**Note:** SSL certificates are not included in this project. They should be manually copied into the /data/code/app/certs directory. Afterward, change the file permissions appropriately
 ```
chmod 644 *.crt
chmod 600 *.key
chmod 700 certs
```

## Additional Notes
- To enable connections between the Specify7 application and its database, ensure the port specified in the db_port_on_host variable (found in the inventory files) is open on the relevant host machines.
- The domain names specified in the domain variable (found in the inventory files) must be registered with the appropriate DNS entries pointing to the application server.
- Ensure HTTP (port 80) and HTTPS (port 443) are open on the application server.
- Do not check in the SSL certificates, key files, and .env files into version control unencrypted.
  
