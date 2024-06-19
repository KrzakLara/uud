# uud






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


 | Scenario | Methodology | Command | Justification |
|----------|-------------|---------|---------------|
| Development and Testing on a Developer Laptop | Use Docker Compose with a Dockerfile for the Python app and `docker-compose.yml` for orchestrating the app and a PostgreSQL database. | `docker-compose up` | Docker Compose manages both the application and its database dependencies in isolated containers, simplifying development and testing. This setup can easily be mirrored on any developer's laptop, ensuring consistency across environments. PostgreSQL is chosen for its closer alignment with production-like settings compared to SQLite. |
| Production Deployment with At Least 10 Instances and Persistent Storage | Deploy on Kubernetes using deployments for the app and StatefulSets for PostgreSQL. Use Helm for package management and Jenkins for CI/CD to handle deployments and updates. | `helm install my-python-app ./my-helm-chart` <br> `kubectl scale deployment/my-python-app --replicas=10` | Kubernetes provides robust tools for managing scalable applications and persistent storage. Helm simplifies the deployment of complex applications. Scaling up to 10 instances is managed via Kubernetes' declarative syntax, and Jenkins automates the build and deployment processes for new image versions, ensuring continuous integration and deployment. |

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
   
*lo5:*

9. 
Troubleshoot why the following command is wrong and why the container does not start successfully/terminates immediately. Explain your findings and correct the command. Note down both your findings and the command.
    
| Task | Issue Identified | Corrected Command | Explanation |
|------|------------------|-------------------|-------------|
| 9    | Missing root password specification and incorrect syntax for detach mode. | `podman run -d --name mysql -e MYSQL_USER=mike -e MYSQL_PASSWORD=mike -e MYSQL_ROOT_PASSWORD=notSoSecret mysql:5.7` | Added `MYSQL_ROOT_PASSWORD=notSoSecret` to satisfy MySQL's requirement for a root password. `-d` for detached mode is correctly used before specifying the image name. |


10.
What feature of container images enables you to deploy containers across multiple environments without having to care about accidentally changing the application code?
    
| Task | Feature | Description |
|------|---------|-------------|
| 10   | Consistency and Isolation | Container images ensure that the software runs the same way in any environment. This isolation between the application and its surroundings means developers don't have to worry about changes in the environment affecting the application's performance or functionalities. |


11. 
Troubleshoot why the following Dockerfile is not building. Explain your findings and correct the command. Note down both your findings and the corrected Dockerfile.
    
| Task | Issue Identified | Corrected Dockerfile Line | Explanation |
|------|------------------|--------------------------|-------------|
| 11   | Syntax error and ineffective update command. | `RUN apt-get update && apt-get install -y some-package` | Corrected syntax by removing the unmatched quote and added an actual install command (`apt-get install -y some-package`) to make the update meaningful. The placeholder `some-package` should be replaced with actual package names as needed. |

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

| Task | Reason | Description | Command |
|------|--------|-------------|---------|
| Choose Docker Swarm | Simplicity and Lower Operational Overhead | Docker Swarm is inherently simpler to set up and manage compared to OpenShift. For a startup without a dedicated DevOps team, Docker Swarm provides a more straightforward approach to container orchestration. It integrates directly with Docker, which many developers are familiar with, reducing the learning curve and operational complexity. This makes it more suitable for small teams or startups looking to quickly deploy and manage containerized applications without the need for advanced configurations or the overhead that comes with more comprehensive solutions like OpenShift. | `docker swarm init` |

