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