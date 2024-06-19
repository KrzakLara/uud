# uud

redhat: https://rha.ole.redhat.com/ 
ili
https://vcsa7.vua.cloud


## Exam details

### Site
http://vcsa7.vua.cloud/
- **username**: infoeduka_name
- **password**: Pa$$w0rd

### VM
- **username**: student
- **password**: student
- **root user**: root
- **password**: centos

### Mysql root user
- **username**: root
- **password**: secret

### Which ports are exposed to host machine?
- Nginx is running on port 80 & 81
- Mysql is running on port 3306

### Which versions of services does docker compose use?
- Mysql version is 5.7.42
- Nginx version is 1.25
- PHP version is 7.4

### Can I add my own initialization script for mysql?
Yes, you can change existing one - `./algebra.sql:/docker-entrypoint-initdb.d/1.sql` or add another one - `./new.sql:/docker-entrypoint-initdb.d/2.sql`

- Notice the number 1, this means this script or dump will execute first
- All dumps and scripts in `docker-entrypoint-initdb.d` will run only once, on container creation





*Addition:*

*lo4:*

7. 
Deploy WordPress and MySQL containers using podman. The WordPress container should successfully connect to the MySQL database container. Use images mysql:5.7 and wordpress:php8.2 from Docker Hub. The WordPress container should be named wordpress and the MySQL container should be named mysql. The WordPress container should not connect to the database by using the root user. Set required environment variables as you see fit. Note down the command used to complete this task into a file called LO4_M.

# Install Podman
sudo yum install -y podman

# Verify Podman installation
podman --version


# Enable user namespaces
echo "user.max_user_namespaces = 15000" | sudo tee -a /etc/sysctl.conf

# Apply the changes
sudo sysctl -p

# Verify Podman installation
podman --version

# Run MySQL container
podman run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wpuser -e MYSQL_PASSWORD=wppass mysql:5.7



prije 2. stepa: # Pull the required images
podman pull mysql:5.7
podman pull wordpress:php7.4-apache

# Run MySQL container
podman run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -e MYSQL_DATABASE=wordpress \
  -e MYSQL_USER=wpuser \
  -e MYSQL_PASSWORD=wppass \
  mysql:5.7

# Run WordPress container
podman run -d --name wordpress \
  --env WORDPRESS_DB_HOST=mysql \
  --env WORDPRESS_DB_USER=wpuser \
  --env WORDPRESS_DB_PASSWORD=wppass \
  --env WORDPRESS_DB_NAME=wordpress \
  -p 8080:80 \
  wordpress:php7.4-apache


8.
Choose and justify an appropriate methodology for running a simple Python application that requires a database for persistent data storage for the following scenarios:
a) Development and testing on a developer laptop
b) Production deployment with at least 10 instances and persistent storage
Note that the company has multiple applications which it needs to deploy and also wants to automatically build new images for new versions of base images.

L:
 | Scenario | Methodology | Command | Justification |
|----------|-------------|---------|---------------|
| Development and Testing on a Developer Laptop | Use Docker Compose with a Dockerfile for the Python app and `docker-compose.yml` for orchestrating the app and a PostgreSQL database. | `docker-compose up` | Docker Compose manages both the application and its database dependencies in isolated containers, simplifying development and testing. This setup can easily be mirrored on any developer's laptop, ensuring consistency across environments. PostgreSQL is chosen for its closer alignment with production-like settings compared to SQLite. |
| Production Deployment with At Least 10 Instances and Persistent Storage | Deploy on Kubernetes using deployments for the app and StatefulSets for PostgreSQL. Use Helm for package management and Jenkins for CI/CD to handle deployments and updates. | `helm install my-python-app ./my-helm-chart` <br> `kubectl scale deployment/my-python-app --replicas=10` | Kubernetes provides robust tools for managing scalable applications and persistent storage. Helm simplifies the deployment of complex applications. Scaling up to 10 instances is managed via Kubernetes' declarative syntax, and Jenkins automates the build and deployment processes for new image versions, ensuring continuous integration and deployment. |

N:

| Scenario | Methodology | Command | Justification |
|----------|-------------|---------|---------------|
| Development and Testing on a Developer Laptop | Utilize Docker Compose with a Dockerfile for the Python application and a `docker-compose.yml` file to orchestrate the application and a PostgreSQL database. | `docker-compose up` | Docker Compose allows for seamless management of the application and its database dependencies within isolated containers, making development and testing straightforward. This setup ensures that all developers have a consistent environment on their laptops. PostgreSQL is preferred for its similarity to production environments over SQLite. |
| Production Deployment with at Least 10 Instances and Persistent Storage | Use Kubernetes for deploying the application, with Deployments for the app and StatefulSets for PostgreSQL. Helm should be used for package management, and Jenkins for CI/CD to automate deployments and updates. | `helm install my-python-app ./my-helm-chart`<br>`kubectl scale deployment/my-python-app --replicas=10` | Kubernetes provides advanced features for managing scalable applications and persistent storage. Helm simplifies the deployment of complex applications. Scaling to 10 instances is efficiently handled using Kubernetes' declarative approach. Jenkins automates the build and deployment process for new image versions, supporting continuous integration and deployment. |

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
   
*lo5:*

9. [LO5_M, 4.5 pts]
Troubleshoot why the following command is wrong and why the container does not start successfully/terminates immediately. Explain your findings and correct the command. Note down both your findings and the command.
podman run --name mysql -e MYSQL_USER=mike -e MYSQL_PASSWORD=mike -d mysql:5.7 (4.5 points)
    
| Task | Issue Identified | Corrected Command | Explanation |
|------|------------------|-------------------|-------------|
| 9    | Missing root password specification and incorrect syntax for detach mode. | `podman run -d --name mysql -e MYSQL_USER=mike -e MYSQL_PASSWORD=mike -e MYSQL_ROOT_PASSWORD=notSoSecret mysql:5.7` | Added `MYSQL_ROOT_PASSWORD=notSoSecret` to satisfy MySQL's requirement for a root password. `-d` for detached mode is correctly used before specifying the image name. |


10. [LO5_M, 4.5 pts]
What feature of container images enables you to deploy containers across multiple environments without having to care about accidentally changing the application code? (4.5 points)

L:
| Task | Feature | Description |
|------|---------|-------------|
| 10   | Consistency and Isolation | Container images ensure that the software runs the same way in any environment. This isolation between the application and its surroundings means developers don't have to worry about changes in the environment affecting the application's performance or functionalities. |

N:

| Task | Feature | Description |
|------|---------|-------------|
| 10   | Consistency and Isolation | Container images ensure the application behaves the same across different environments. This isolation from the host system means that developers don't need to worry about environmental changes affecting the application, thereby preserving the integrity of the application code. |



11. [LO5_D, 5 pts]
Troubleshoot why the following Dockerfile is not building. Explain your findings and correct the command. Note down both your findings and the corrected Dockerfile. (2.5 points)
FROM ubuntu
LABEL VERSION=0.1
LABEL BLOCK=true
LABEL EMAIL=student@racunarstvo.hr
RUN apt update && echo it works"
WORKDIR /tmp/
CMD ["sleep", "10"]


L:
| Task | Issue Identified | Corrected Dockerfile Line | Explanation |
|------|------------------|--------------------------|-------------|
| 11   | Syntax error and ineffective update command. | `RUN apt-get update && apt-get install -y some-package` | Corrected syntax by removing the unmatched quote and added an actual install command (`apt-get install -y some-package`) to make the update meaningful. The placeholder `some-package` should be replaced with actual package names as needed. |

N:
| Task | Problem Identified | Corrected Dockerfile Line | Explanation |
|------|--------------------|--------------------------|-------------|
| 11   | Syntax error and ineffective update command. | `RUN apt-get update && apt-get install -y some-package` | Fixed the syntax error by removing the unmatched quote and included an actual installation command (`apt-get install -y some-package`) to make the update command functional. Replace `some-package` with the actual package names needed. |


----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 *lo6:*
 
12. 
Deploy the httpd application on OpenShift. There should be 2 pods in the deployment and a service load balancing traffic coming to httpd pods. Create a route to expose the service. If you decide to use a template, you need to use template called custom-httpd or template which has its description set to Custom HTTPD. If you are not using a template do not set resource limits and requests.
Note that you will not be able to test your route without adding your FQDN to the list of URLs in /etc/hosts file. It is enough that it is created in OpenShift.
There should be no image stream configured for this task. If you end up with image pull error, but you deployed everything as stated it is fine.

 | Task | Command | Description |
|------|---------|-------------|
| Deploy httpd application | `oc new-app --name=httpd --docker-image=httpd:2.4 --labels=app=httpd` | This command creates a new application in OpenShift using the httpd Docker image version 2.4. The application is named 'httpd' and appropriately labeled. |
| Scale to 2 pods | `oc scale --replicas=2 deployment/httpd` | Scale the deployment to have 2 pods to ensure high availability and load balancing. |
| Expose the service | `oc expose svc/httpd` | Expose the httpd service to external traffic by creating a route in OpenShift. This command automatically uses the service 'httpd' and exposes it externally. |
| Use a custom template (optional) | `oc new-app -f custom-httpd-template.yaml` | If using a custom template named 'custom-httpd-template.yaml', deploy it with this command. Ensure the template includes specifications for no resource limits, as required. |


13. 
Why would you use Docker Swarm instead of OpenShift for a simple startup company not having a dedicated DevOps employee?

L:
| Task | Reason | Description | Command |
|------|--------|-------------|---------|
| Choose Docker Swarm | Simplicity and Lower Operational Overhead | Docker Swarm is simpler to set up and manage compared to OpenShift. For a startup without a dedicated DevOps team, Docker Swarm provides a more straightforward approach to container orchestration. It integrates directly with Docker, which many developers are familiar with, reducing the learning curve and operational complexity. This makes it more suitable for small teams or startups looking to quickly deploy and manage containerized applications without the need for advanced configurations or the overhead that comes with more comprehensive solutions like OpenShift. | `docker swarm init` |

N:
| Task | Reason | Description | Command |
|------|--------|-------------|---------|
| Select Docker Swarm | Ease of Use and Minimal Operational Burden | Docker Swarm is easier to configure and administer compared to OpenShift. For a startup without a dedicated DevOps engineer, Docker Swarm offers a more straightforward method for container orchestration. It integrates seamlessly with Docker, which is widely known among developers, thus lowering the learning curve and reducing operational complexities. This makes it ideal for small teams or startups that need to deploy and manage containerized applications swiftly without dealing with the intricate setups and maintenance associated with more extensive solutions like OpenShift. | `docker swarm init` |

