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

` values.yaml` File
Open the values.yaml file in the fleetman/charts/fleetman/ directory.
Modify the values according to your needs, especially those under deployments for each application.
Ensure Docker images and probe configurations (liveness and readiness) are correct.

### Installation with Helm

In the fleetman/charts/fleetman/ directory, run the Helm command to install the Fleetman project:

`helm install your-release-name .`
Replace your-release-name with the desired name for your deployment.

### Verification of Installation

Check that the pods are running:
`kubectl get pods`

### Verify that the services are deployed:

`kubectl get services`

### Accessing Services

You can access the deployed services using IP addresses or service names. For example, for access to webapp service, you can access the application via http://127.0.0.1:30080.

**Uninstallation with Helm**
If needed, you can uninstall Fleetman using the Helm command:
`helm uninstall your-release-name`

# Helm Configuration for Fleetman

This project uses Helm to manage the deployment of various applications within Kubernetes. The `values.yaml` file contains default configuration values for these deployments. You can customize these values according to your needs.

## Project Structure

    fleetman/
    |-- charts/
    | |-- fleetman/
    | |-- templates/
    | |-- deployments.yaml
    | |-- service.yaml
    | |-- persistentvolumes.yaml
    |-- values.yaml
    |-- README.md



Helm Configuration
values.yaml

The values.yaml file contains default values for Fleetman deployments.

Global Configuration
replicaCount: Default number of replicas for deployments.
useSpring: Use of Spring for Kubernetes.
image: Default Docker image configuration.
tag: Image tag.
pullPolicy: Image pull policy.
ports: Default exposed ports.
service: Default Kubernetes service configuration.
type: Service type.
protocol: Service protocol.
livenessProbe and readinessProbe: Default configurations for liveness and readiness probes.
Spring Profiles
Spring profiles can be defined with specific values.

Deployment Configurations
Specific deployments are configured with their own parameters.

mongodb
queue
position-simulator
position-tracker
api-gateway
webapp
deployments.yaml
The deployments.yaml file defines Kubernetes deployments generated from the values defined in values.yaml.

service.yaml
The service.yaml file defines Kubernetes services generated from the values defined in values.yaml.

persistentvolumes.yaml
The persistentvolumes.yaml file defines Kubernetes persistent volume claims generated from the values defined in values.yaml.

Customization
You can customize the configuration by modifying values in the values.yaml file. Make sure to follow the YAML structure and syntax.
