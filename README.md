# Fleetman


### Objectifs

Votre rôle est de déployer cette application distribuée sur un cluster Kubernetes. Pour ce faire, vous vous utiliserez les informations présentes dans le fichier Docker Compose et passerez par les étapes suivantes :

    1. Créer un déploiement pour chaque conteneur.

    2. Créer un service pour chaque déploiement. Vous veillerez à utiliser un service interne ou externe selon ce qui est le plus approprié.

    3. Utiliser un volume pour la base de données.


### Elements à notre disposition :

**fleetman-position-simulator** : une application Spring Boot émettant en continu des positions fictives de véhicules.
**fleetman-queue** : une queue Apache ActiveMQ qui reçoit puis transmet ces positions.
**fleetman-position-tracker** : une application Spring Boot qui consomme ces positions reçues pour les stocker dans une base de données MongoDB. Elles sont ensuite disponibles via une API RESTful.
**fleetman-mongo** : instance de la base de données MongoDB.
**fleetman-api-gateway** : une API Gateway servant de point d'entrée pour l'application web
**fleetman-web-app** : l'application web présentée précédemment.




### Installation and Launching Fleetman with Helm
### Prerequisites

** Ensure that you have the following tools installed on your machine:**
https://helm.sh/docs/intro/install/

Helm: ` Install Helm` 
Clone the Project
Clone the Fleetman project from your Git repository:
git clone https://github.com/MitryDim/fleetman.git

cd fleetman

### Configuration
` values.yaml` File
Open the values.yaml file in the fleetman/charts/fleetman/ directory.
Modify the values according to your needs, especially those under deployments for each application.
Ensure Docker images and probe configurations (liveness and readiness) are correct.
Installation with Helm
In the fleetman/charts/fleetman/ directory, run the Helm command to install the Fleetman project:


`helm install your-release-name .` 
Replace your-release-name with the desired name for your deployment.

### Verification of Installation
Check that the pods are running:
`kubectl get pods` 

### Verify that the services are deployed:
`kubectl get services` 

###  Accessing Services
You can access the deployed services using IP addresses or service names. For example, for access to webapp service, you can access the application via http://127.0.0.1:30080.

**Uninstallation with Helm**
If needed, you can uninstall Fleetman using the Helm command:
`helm uninstall your-release-name`













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