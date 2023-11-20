# Fleetman


### Objectives

Your role is to deploy this distributed application on a Kubernetes cluster. To do this, you will use the information in the Docker Compose file and go through the following steps:

    1. Create a deployment for each container.

    2. Create a service for each deployment. Be sure to use an internal or external service, whichever is more appropriate.

    3. Use a volume for the database.


### Elements à notre disposition :

**fleetman-position-simulator** : a Spring Boot application that continuously transmits fictitious vehicle positions.
**fleetman-queue** : an Apache ActiveMQ queue that receives and transmits these positions.
**fleetman-position-tracker** : a Spring Boot application that consumes these received positions and stores them in a MongoDB database. They are then made available via a RESTful API.
**fleetman-mongo** : instance of the MongoDB database.
**fleetman-api-gateway** : a Gateway API serving as an entry point for the web application.
**fleetman-web-app** : the web application presented above.


# Helm Configuration for Fleetman

This project uses Helm to manage the deployment of various applications within Kubernetes. The `values.yaml` file contains default configuration values for these deployments. You can customize these values according to your needs.

## Project Structure

fleetman/
|-- charts/
|   |-- fleetman/
|       |-- templates/
|           |-- deployments.yaml
|           |-- service.yaml
|           |-- persistentvolumes.yaml
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