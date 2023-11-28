
**Author : Dimitry.C - Valentin.L**

# Fleetman 🚚
---
![Result](https://github.com/MitryDim/fleetman/assets/80764455/10f1214c-532e-4448-9ecb-f5ffb1f14cc2)

## Summary
* [Project description](https://github.com/MitryDim/fleetman/blob/main/README.md#project-description)
* [Objective of the projet](https://github.com/MitryDim/fleetman/blob/main/README.md#Objective-of-the-projet)
	* [Elements at our disposal](https://github.com/MitryDim/fleetman/blob/main/README.md#Elements-at-our-disposal)
* [Installation and Launching Fleetman with Helm](https://github.com/MitryDim/fleetman/tree/main#installation-and-launching-fleetman-with-helm)
	* [Prerequisites](https://github.com/MitryDim/fleetman/tree/main#prerequisites)
	* [Clone the Project](https://github.com/MitryDim/fleetman/tree/main#Clone-the-Project)
---
## Project description
The project is a web application that allows real-time tracking of a fleet of vehicles performing deliveries.

## Objective of the project
The goal of this project is to deploy this application with [Kubernetes](https://kubernetes.io/) as well as with the package manager [Helm](https://helm.sh/fr/), which will allow us to configure and deploy our application efficiently.

### Elements at our disposal :
Pour ce projet, nous utiliserons les images Dockers de supinfo4kube pour l'application et mongo pour la base de données.
-  **[fleetman-position-simulator](https://hub.docker.com/r/supinfo4kube/position-simulator)** : a Spring Boot application that continuously transmits fictitious vehicle positions.
-  **[fleetman-queue](https://hub.docker.com/r/supinfo4kube/queue)** : an Apache ActiveMQ queue that receives and transmits these positions.
- **[fleetman-position-tracker](https://hub.docker.com/r/supinfo4kube/position-tracker)** : a Spring Boot application that consumes these received positions and stores them in a MongoDB database. They are then made available via a RESTful API.
- **[fleetman-mongo](https://hub.docker.com/_/mongo)** : instance of the MongoDB database.
- **[fleetman-api-gateway](https://hub.docker.com/r/supinfo4kube/api-gateway)** : a Gateway API serving as an entry point for the web application.
- **[fleetman-web-app](https://hub.docker.com/r/supinfo4kube/web-app/tags)** : the web application presented above.

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


## [Values.yaml](https://github.com/MitryDim/fleetman/blob/0d9d06d3faf1937a099e7764026419c7f543ce28/values.yaml) Configuration 

***Spring values***
>[!Note]
>The spring value is an environment value it was defined in the application you must choose local or prod.
```yaml
spring:
  local:
    name: SPRING_PROFILES_ACTIVE
    value: local-microservice
  prod:
    name: SPRING_PROFILES_ACTIVE
    value: production-microservice
```
***Configuration of the global values***
The `global` section in the configuration file holds essential parameters that influence the overall behavior of the Fleetman application.

the global configuration start with this section : 
```YAML
global:
```
### Deployment

The default configuration for deployment is directly in global section : 
```YAML
  namespace: default
  replicaCount: 1
  useSpring: local
  image:
    tag: "latest"
    pullPolicy: IfNotPresent
  ports: 
    - 80
```
>[!Warning]
>Default value for namespace /!\ don't touch for this moment because the application have one bug if is not in default namespace.
-   **Replica Count**: Sets the default number of replicas for deployments. Replicas represent the number of identical pods running the application.

-   **Spring Profile**: Controls the Spring profile used for the application. The value is dependent on the environment (ENV) variable for the Spring profile. Options include `local` for local development and `prod` for production development.

-   **Image Configuration:** Defines the default tag and pull policy are specified, providing a starting point for the deployment.

-   **Ports Configuration:** Specifies the internal ports used by the application. If an internal and external port need to differ, a mapping can be defined (e.g., `InternalPort:ExternalPort`). For example, if the type is NodePort, the externally accessible port would be `36500`, pointing to the internal port `80`.
    
Default Memory and CPU usage if their not given by the user, based on Computer Capacity

```YAML
  ...
  resources:
    requests:
      memory: "256Mi"
      cpu: "250m"
    limits:
      memory: "512Mi"
      cpu: "500m"
```
###  requests
To allocate/reserve resources for a container, simply configure the `requests` within the `resources` section of the container.

The above example would define that this container can use a maximum of:
-   0.25 Cores
-   256 Megabytes of Memory (RAM)

###  limits
To limit the resources of a container, simply configure the  `limits`  within the  `resources`  section of the container.
-   CPU in Core units, i.e. 3 = 3 Cores, 800m = 0.8 Core (=800  **M**illi-Core)
-   Memory (RAM) in Gi (Gigabyte), Mi (Megabyte) or Ki (Kilobyte)
> [!Warning]
> Resource limits should always be higher than the resource requests. Because resource limits are not allocated/reserved for a container (unlike resource requests), it is possible to oversubscribe resource limits, i.e. use a total of resource limits over all containers which is more than the cluster has.

> [!Note]
> This global configuration provides a foundation for Fleetman's deployment, with default values that can be adjusted based on specific deployment requirements.

## Service
The default configuration of the service is in the global section and then in the service section: 
``` YAML
global:
  ...
  service:
    type: ClusterIP  #Default value is ClusterIp the possible type in this project was NodePort and ClusterIp or LoadBalancer 
    protocol: TCP #Default protocol is TCP Docs : ) 
    livenessProbe: 
     ...
    readinessProbe:
    ...
```
   **Service Configuration:**
   -  Type: `ClusterIP` (Default)
   -   Protocol: `TCP` (Default)
  
  Defines the default configuration for Kubernetes services. 
 The service type[^1] is set to `ClusterIP` by default, but alternatives include `NodePort` and `LoadBalancer`. 
 The protocol[^2] is set to `TCP`, and liveness and readiness [^3] probes are configured to monitor the health of the application.
 
```YAML
	...
    livenessProbe: 
      type: "httpGet"
      path: /
      initialDelaySeconds: 30
      periodSeconds: 10
    readinessProbe:
      type: "httpGet"
      path: /
      initialDelaySeconds: 30
      periodSeconds: 10 
```
**Liveness Probe & Readiness Probe :**
-   **Type:** Specifies the type of probe, and in this case, it's set to "httpGet," meaning Kubernetes will perform an HTTP GET request. You can view different type in [documentation]
-   **Path:** Specifies the endpoint or path on the container where the probe should send the HTTP GET request. In this case, it's set to "/" indicating the root path.
-   **Initial Delay Seconds:** Specifies the number of seconds after the container has started before the liveness probe is initiated. In this example, it's set to 30 seconds.
-   **Period Seconds:** Specifies the frequency, in seconds, at which the liveness probe should be performed. Here, it's set to 10 seconds.


These settings define how Kubernetes monitors the health of a container. The liveness probe checks if the container is live and responsive, while the readiness probe checks if the container is ready to receive traffic.

>[!Caution] 
>Liveness probes can be a powerful way to recover from application failures, but they should be used with caution. Liveness probes must be configured carefully to ensure that they truly indicate unrecoverable application failure, for example a deadlock.

> [!Note]
>  Incorrect implementation of liveness probes can lead to cascading failures. This results in restarting of container under high load; failed client requests as your application became less scalable; and increased workload on remaining pods due to some failed pods. Understand the difference between readiness and liveness probes and when to apply them for your app.

[^1]: https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types
[^2]: https://kubernetes.io/docs/reference/networking/service-protocols/
[^3]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/


***Configuration of deployments values***
The deployments value is the value use for the differents deployment.
 
 *  mongodb
 *  queue
*  position-simulator
* position-tracker
* api-gateway
* webapp
 
You can modifie this values or add other values ​​that are in the global values ​​this will do an override.
```YAML
deployments:
  mongodb: <-- name of your deployement
    containers:
      image:
        repository: mongo
        tag: "3.6.23" <-- version of image docker
      resources: <-- ressource is for control the ressources use by your application
        requests:
          memory: "256Mi"
          cpu: "250m"
        limits:
          memory: "512Mi"
          cpu: "500m"
      volumeMounts: <-- this value is for mount a volume
        - path: /data/db
          persistentVolumeClaim: true <-- make true if you wan't use persistent volume this value is required
      probe:
        type: "tcpSocket"
        port: 27017
      readinessProbe: true <-- this value is if you wan't add readinessProbe
      livenessProbe: true <-- this value is if you wan't add livenessProbe
  queue:
    containers:
      image:
        repository: supinfo4kube/queue
        tag: "1.0.1"
      resources:
        requests:
          memory: "256Mi"
          cpu: "250m"
        limits:
          memory: "512Mi"
          cpu: "500m"
        probe:
          port: 8161
        livenessProbe: true
  position-simulator:
    containers:
      image:
        repository: supinfo4kube/position-simulator
        tag: "1.0.1"
      resources:
        requests:
          memory: "256Mi"
          cpu: "250m"
        limits:
          memory: "512Mi"
          cpu: "500m"
      spring: prod <-- this value is the value of spring profile describe a little above
  position-tracker:
    containers:
      image:
        repository: supinfo4kube/position-tracker
        tag: "1.0.1"
      resources:
        requests:
          memory: 512Mi"
          cpu: "500m"
        limits:
          memory: "1Gi"
          cpu: "1000m"
      spring: prod
  api-gateway:
    containers:
      image:
        repository: supinfo4kube/api-gateway
        tag: "1.0.1"
      resources:
        requests:
          memory: "256Mi"
          cpu: "250m"
        limits:
          memory: "768Mi"
          cpu: "750m"
      spring: prod
  webapp:
    containers:
      image:
        repository: supinfo4kube/web-app
        tag: "1.0.0"
      resources:
        requests:
          memory: "256Mi"
          cpu: "250m"
        limits:
          memory: "512Mi"
          cpu: "500m"
      spring: prod
      probe:
        port: 80
      livenessProbe: true  
```
Specific deployments are configured with their own parameters.

# Deployment template Configuration


This deployments file is a template. This file loops over the values ​​declared in the `values.yaml` file in the deployments section so that it allows you to create the necessary deployments without making several deployment files
```YAML
{{- range $key, $value := .Values.deployments }}
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: {{ $value.namespace | default $.Values.global.namespace }} <-- value override or in global value 
  name: {{ $key }} <-- the key is name of the deployment is value file you have set
  labels: 
  {{- with $value.labels }} <-- if you configure value of label this get all values but is nothing the default app: name of deployment
    {{- toYaml . | nindent 4}}
  {{ else }}
    app: {{ $key }}
  {{- end }}
spec:
  replicas: {{ $value.replicaCount | default $.Values.global.replicaCount }} <-- The `replicas` option expects an integer and defines how many pods Kubernetes will create for this component the value is override if you set an value in value file by default is the value of global section
  selector:
    matchLabels:
      {{- with $value.labels }}  <-- if you configure value of label this get all values but is nothing the default app: name of deployment
        {{- toYaml . | nindent 6}}
      {{ else }}
        app: {{ $key }}
      {{- end }}
  template:
    metadata:
      labels:
      {{- with $value.labels }}  <-- if you configure value of label this get all values but is nothing the default app: name of deployment
        {{- toYaml . | nindent 8}}
      {{ else }}
        app: {{ $key }}
      {{- end }}
    spec:
      containers:
        - name: {{ $key }} <-- The name the name for the Container
          imagePullPolicy: {{ $value.containers.pullPolicy | default $.Values.global.image.pullPolicy }}
          image: "{{ $value.containers.image.repository | default $key }}:{{ $value.containers.image.tag  | default $.Values.global.image.tag}}"
          {{- if $value.containers.resources | default $.Values.global.resources }}
          resources:
            request:
              memory: {{ .request.memory}}
              cpu: {{ .request.cpu}}
            limits:
              memory: {{ .limits.memory}}
              cpu: {{ .limits.cpu}}
          {{- end -}}
          {{- with $value.containers.secret }}
          envFrom :
            - secretRef:
                name: {{ . }}
          {{- end -}}
          {{- if $value.containers.spring }}
          {{- $value := index $.Values.spring $value.containers.spring }}
          env:
            - name: {{ $value.name }}
              value: {{ $value.value }}
          {{- end }}
          {{- if $value.containers.ports}}
          ports:
            {{- range $value.containers.ports | default $.Values.global.ports }}
            - containerPort: {{ . }}
              name: {{ $key }}-{{ . }}
            {{- end }}
          {{- end }}
          {{- if $value.containers.livenessProbe }}
          {{- with $value.containers.probe }}
          livenessProbe:
          {{- $livenessProbeType := .type  | default $.Values.global.livenessProbe.type -}}
          {{- if eq $livenessProbeType "httpGet" }}
            httpGet:
          {{- else if eq $livenessProbeType "tcpSocket"}}
            tcpSocket:
          {{- end }}
          {{- if ne $livenessProbeType "tcpSocket" }}
              path: {{ .path | default $.Values.global.readinessProbe.path}}
          {{- end }}
              port: {{ .port }}
            initialDelaySeconds: {{ .initialDelaySeconds | default $.Values.global.livenessProbe.initialDelaySeconds }}
            periodSeconds: {{ .periodSeconds | default $.Values.global.livenessProbe.periodSeconds }}
          {{- end }}
          {{- end }}
          {{- if $value.containers.readinessProbe }}
          {{- with $value.containers.probe }}
          readinessProbe:
          {{- $readinessProbeType := .type  | default $.Values.global.readinessProbe.type -}}
          {{- if eq $readinessProbeType "httpGet" }}
            httpGet:
          {{- else if eq $readinessProbeType "tcpSocket"}}
            tcpSocket:
          {{- end }}
          {{- if ne $readinessProbeType "tcpSocket" }}
              path: {{ .path | default $.Values.global.readinessProbe.path}}
          {{- end }}
              port: {{ .port }}
            initialDelaySeconds: {{ .initialDelaySeconds | default $.Values.global.readinessProbe.initialDelaySeconds }}
            periodSeconds: {{ .periodSeconds | default $.Values.global.readinessProbe.periodSeconds }}
          {{- end }}
          {{- end }}
    {{- if $value.containers.volumeMounts }}
          volumeMounts:
            {{- range $index, $volume := $value.containers.volumeMounts }}
            - mountPath: {{ $volume.path}}
              name: {{ $key }}-{{ $index }}
            {{- end }}
      volumes:
      {{- range $index, $volume := $value.containers.volumeMounts }}
        - name: {{ $key }}-{{ $index }}
        {{- if $volume.emptyDir}}
          emptyDir: {{ $volume.emptyDir }}
        {{- end }}
        {{- if $volume.persistentVolumeClaim | default false }}
          persistentVolumeClaim:
            claimName: {{ $key }}-pv-claim
        {{ end }}
      {{ end }}
    {{ end }}
---
{{- end }}
```
> [!CAUTION]
> If you modify a value and is not correct, this can create an error ! Please make sure if you modify.
