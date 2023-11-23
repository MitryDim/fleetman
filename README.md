**Author : Dimitry.C - Valentin.L**

# Fleetman 🚚
---


## Objectives

Your role is to deploy this distributed application on a Kubernetes cluster. To do this, you will use the information in the Docker Compose file and go through the following steps:

    1. Create a deployment for each container.

    2. Create a service for each deployment. Be sure to use an internal or external service, whichever is more appropriate.

    3. Use a volume for the database.
---

### Elements à notre disposition :

**fleetman-position-simulator** : a Spring Boot application that continuously transmits fictitious vehicle positions.
**fleetman-queue** : an Apache ActiveMQ queue that receives and transmits these positions.
**fleetman-position-tracker** : a Spring Boot application that consumes these received positions and stores them in a MongoDB database. They are then made available via a RESTful API.
**fleetman-mongo** : instance of the MongoDB database.
**fleetman-api-gateway** : a Gateway API serving as an entry point for the web application.
**fleetman-web-app** : the web application presented above.

---

## Installation and Launching Fleetman with Helm

### Prerequisites

Helm is the package manager for Kubernetes.
Ensure that you have the following tools installed on your machine :
https://helm.sh/docs/intro/install/


### Clone the Project

⚠️**NE PAS OUBLIER DE METTRE LE GIT EN PUBLIC**⚠️

Clone the Fleetman project from your Git repository:
git clone

https://github.com/MitryDim/fleetman.git

Go on the helm directory : `cd fleetman`


### Configuration

Open the `values.yaml` file in the **fleetman** directory.
The values.yaml file contains default values for Fleetman deployments.
Modify the values according to your needs, especially those under deployments for each application.
Ensure Docker images and probe configurations (liveness and readiness) are correct.

### Installation with Helm

In the **fleetman** directory, run the Helm command to install the Fleetman project:

`helm install your-release-name .`
Replace "your-release-name" with the desired name for your deployment.

### Verification of Installation

Check that the pods are running:
`kubectl get pods`

### Verify that the services are deployed:

`kubectl get services`

### Accessing Services

You can access the deployed services using IP addresses or service names. For example, for access to webapp service, you can access the application via http://127.0.0.1:30080.

### Uninstallation with Helm
If needed, you can uninstall Fleetman using the Helm command:
`helm uninstall your-release-name`

## Project Structure

    fleetman/
    | |-- templates/
    |   |-- deployments.yaml
    |   |-- namespace.yaml
    |   |-- service.yaml
    |   |-- persistentvolumes.yaml
    |-- .helmignore
    |-- Chart.yaml
    |-- values.yaml
    |-- README.md

## Charts Templates

**deployments.yaml**
The deployments.yaml file defines Kubernetes deployments generated from the values defined in values.yaml.

**service.yaml**
The service.yaml file defines Kubernetes services generated from the values defined in values.yaml.

**persistentvolumes.yaml**
The persistentvolumes.yaml file defines Kubernetes persistent volume claims generated from the values defined in values.yaml.


#Value ENV for spring profile
spring:
  local:
    name: SPRING_PROFILES_ACTIVE
    value: local-microservice
  prod:
    name: SPRING_PROFILES_ACTIVE
    value: production-microservice

Global Configuration
  namespace: default Default value for namespace /!\ don't touch for this moment because the application hav one bug if is not in default namespace.
  replicaCount: 1 Default number of replicas for deployments.
  useSpring: local  depend of  value ENV for spring profile can be defined with specific values
  image: Default configuration image.
    tag: "latest" Default Image tag
    pullPolicy: IfNotPresent Default Image pull policy
  ports: 
    - 80   If you wan't an port interne and is not the same you can make this : InternalPort:ExternalPort for exemple 80:36500 if type is NodePort the port aceessible in externe was 36500 and point to port 80 in interne 
  service:
    type: ClusterIP Default value is ClusterIp the possible type in this project was NodePort and ClusterIp or LoadBalancer  https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types
    protocol: TCP Default protocol is TCP : https://kubernetes.io/docs/reference/networking/service-protocols/
  livenessProbe: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
    type: "httpGet"
    path: /
    initialDelaySeconds: 30
    periodSeconds: 10
  readinessProbe:
    type: "httpGet"
    path: /
    initialDelaySeconds: 30
    periodSeconds: 10

Deployment Configurations
Specific deployments are configured with their own parameters.
