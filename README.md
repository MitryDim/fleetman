
**Author : Dimitry.C - Valentin.L**

# Fleetman 🚚
---
![Result](https://github.com/MitryDim/fleetman/assets/80764455/10f1214c-532e-4448-9ecb-f5ffb1f14cc2)

## Summary

1. [Fleetman Project](#fleetman)
   - [Project Description](#project-description)
   - [Project Objective](#objective-of-the-project)
2. [Installation and Launching Fleetman with Helm](#installation-and-launching-fleetman-with-helm)
   - [Prerequisites](#prerequisites)
   - [Project Cloning](#clone-the-project)
   - [Configuration](#configuration)
   - [Installation with Helm](#installation-with-helm)
   - [Verification of Installation](#verification-of-installation)
   - [Verification of Deployed Services](#verify-that-the-services-are-deployed)
   - [Accessing Services](#accessing-services)
   - [Uninstallation with Helm](#uninstallation-with-helm)
3. [Project Structure](#project-structure)
   - [Chart Templates](#charts-templates)
4. [Values.yaml Configuration](#valuesyaml-configuration)
   - [Global Values](#configuration-of-the-global-values)
   - [Deployment Configuration](#deployment)
   - [Services](#services)
   - [Persistent Volumes](#persistent-volumes)
5. [Deployment template Configuration](#deployment-template-configuration)
   - [Containers Informations](#containers-informations)
   - [Resources Managements](#resources-managements)
   - [Secrets](#secrets)
   - [Spring Environment Variables](#spring-environment-variables)
   - [Ports](#ports)
   - [Probes Sections](#probes-section)
      - [Liveness Probe](#liveness-probe)
      - [Readiness Probe](#readiness-probe)
   - [Volumes Sections](#volumes-section)
      - [Volumes Mounts](#volumes-mounts)
      - [Volumes](#volumes)
6. [Service template Configuration](#service-template-configuration)
   - [Service Specification](#service-specification)
7. [Namespace Volumes Configuration](#namespace-template-configuration)
8. [Persistent Volumes Configuration](#persistent-volume-configuration)
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

>[!Note]
> Wait a few moments before deploying all pods. The probes are seting up.

### Verification of Installation

Check that the pods are running:
`kubectl get pods`

### Verify that the services are deployed

`kubectl get services`

### Accessing Services

You can access the deployed services using IP addresses or service names. For example, for access to webapp service, you can access the application via http://127.0.0.1 or http://localhost .

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

**namespace.yaml**
The namespace.yaml file defines Kubernetes namespaces generated from the values defined in values.yaml.

**service.yaml**
The service.yaml file defines Kubernetes services generated from the values defined in values.yaml.

**persistentvolumes.yaml**
The persistentvolumes.yaml file defines Kubernetes persistent volume claims generated from the values defined in values.yaml.


## [Values.yaml](https://github.com/MitryDim/fleetman/blob/0d9d06d3faf1937a099e7764026419c7f543ce28/values.yaml) Configuration

### Spring values
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
### Configuration of the global values
The `global` section in the configuration file holds essential parameters that influence the overall behavior of the Fleetman application.

the global configuration start with this section :
```YAML
global:
```
#### Deployment

The default configuration for deployment is directly in global section :
```YAML
  namespace: default
  replicaCount: 1
  useSpring: local
  image:
    tag: "latest"
    pullPolicy: IfNotPresent
```
>[!Warning]
>Default value for namespace /!\ don't touch for this moment because the application have one bug if is not in default namespace.
-   **Replica Count**: Sets the default number of replicas for deployments. Replicas represent the number of identical pods running the application.

-   **Spring Profile**: Controls the Spring profile used for the application. The value is dependent on the environment (ENV) variable for the Spring profile. Options include `local` for local development and `prod` for production development.

-   **Image Configuration:** Defines the default tag and pull policy are specified, providing a starting point for the deployment.

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
**requests**
To allocate/reserve resources for a container, simply configure the `requests` within the `resources` section of the container.

The above example would define that this container can use a maximum of:
-   0.25 Cores
-   256 Megabytes of Memory (RAM)

**limits**
To limit the resources of a container, simply configure the  `limits`  within the  `resources`  section of the container.
-   CPU in Core units, i.e. 3 = 3 Cores, 800m = 0.8 Core (=800  **M**illi-Core)
-   Memory (RAM) in Gi (Gigabyte), Mi (Megabyte) or Ki (Kilobyte)
> [!Warning]
> Resource limits should always be higher than the resource requests. Because resource limits are not allocated/reserved for a container (unlike resource requests), it is possible to oversubscribe resource limits, i.e. use a total of resource limits over all containers which is more than the cluster has.

> [!Note]
> This global configuration provides a foundation for Fleetman's deployment, with default values that can be adjusted based on specific deployment requirements.

#### Service
The default configuration of the service is in the global section and then in the service section:
``` YAML
global:
  ...
  service:
    type: ClusterIP
    protocol: TCP
    livenessProbe:
     ...
    readinessProbe:
    ...
```
   **Service Configuration:**
   -  Type: `ClusterIP` (Default)
   -   Protocol: `TCP` (Default)

  Defines the default configuration for Kubernetes services.
 The service type[^1] is set to `ClusterIP` by default, but alternatives include `LoadBalancer`.
 The protocol[^2] is set to `TCP`, and liveness and readiness [^3] probes are configured to monitor the health of the application.

>[!Caution]
>For a scalability problem do not use NodePort

```YAML
    ...
    livenessProbe:
      type: "tcpSocket"
      path: /
      initialDelaySeconds: 130
      periodSeconds: 10
    readinessProbe:
      type: "tcpSocket"
      path: /
      initialDelaySeconds: 130
      periodSeconds: 10
```
**Liveness Probe & Readiness Probe :**
-   **Type:** Specifies the type of probe, and in this case, it's set to "tcpSocket," meaning Kubernetes will perform an HTTP GET request. You can view different type in [documentation]
-   **Path:** Specifies the endpoint or path on the container where the probe should send the tcpSocket request. In this case, it's set to "/" indicating the root path. 
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


### Configuration of deployments values
 In the `values.yaml` file the deployment start with this section :

```YAML
deployments:
```
  The deployments value is the value use for the differents deployment these values are in section deployment the structure follow this :
```YAML
deployments:
  mongodb:
    containers:
      ...
  queue:
    containers:
      ...
  position-simulator:
    containers:
      ...
  position-tracker:
    containers:
      ...
  api-gateway:
    containers:
      ...
  webapp:
    containers:
      ...
 ```

We will see the values ​​that you can add or modify in relation to the values ​​of the global section
>[!Note]
> All the value has in containers section.

**image section**
```YAML
...
      image:
        repository: mongo
        tag: "3.6.23"
```
- the repository as a image of [docker hub](https://hub.docker.com/)
- tag as version of image (optional) by default use 'latest' of global value tag but is not recommended

**resources section**
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
 **volumeMounts section**
```YAML
...
     volumeMounts:
        - path: /data/db
          persistentVolumeClaim: true
```
If you want add a volume you can add this section.
the value `persistentVolumeClaim : true` is for your volume to be persistent, if you don't want you just have to delete this value.

If you want add multiple volume you just have to add a dash with `path : ` exemple :

```YAML
...
     volumeMounts:
        - path: /data/db
          persistentVolumeClaim: true
        - path: /data/db1
```

**Use Probe**
If you want add readinessProbe or livenessProbe you can add these values :
```YAML
...
      readinessProbe: true
      livenessProbe: true
```
You can change the probe values relative to the global value but the port is required you can do it like this for example:
```YAML
...
      probe:
        type: "tcpSocket"
        port: 27017
```

**Spring profile**
For modify the value of spring profile compared with global value you can proceed like this for exemple :
```YAML
...
      spring: prod
```

**ContainerPort**
If you want you can set the internal port number use in internal by your application in container section. exemple :
```YAML
...
      containerPort: 27017
```

### Configuration of services values
 In the `values.yaml` file the service configuration start with this section :
```YAML
services:
```
The services value is the value use for the differents services these values are in section services the structure follow this for exemple :

```YAML
services:
  webapp:
    type: "LoadBalancer"
    ports:
      - "30080:80"
  queue:
    ports:
      - "8161"
      - "61616"
...
```
- webapp: It's name of application ( it is necessary that it is the same as the value put in deployment )
- type[^1] : If you want override the global value you can add this here.
- ports: Is list of port(s) you want to expose. If the port you want to expose is not the same as that of the application you can do like this: `30080:80` the first port is port of service expose and 80 is the port internal of your application.
- protocol:  TCP[^2] This is the default value defined in the global values 
>[!Note]
> type and protocol was define in global section if it's the same value don't need to add in your configuration

### Persistent Volumes
If in the deployment value you use a persistentVolume you need to add this configuration in `values.yaml` and in `persistentVolumesClaim` section  for exemple :
```YAML
persistentVolumesClaim:
  mongodb-pv-claim:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 3Gi
```
- ***nameofdeployment***-pv-claim : Name of persistantVolume, you need set the same name of deployment
- accessModes:
	`ReadWriteOnce`
	the volume can be mounted as read-write by a single node. ReadWriteOnce access mode still can allow multiple pods to access the volume when the pods are running on the same node.

	`ReadOnlyMany`
	the volume can be mounted as read-only by many nodes.

	`ReadWriteMany`
	the volume can be mounted as read-write by many nodes.

	`ReadWriteOncePod`
**Feature state:**  `Kubernetes v1.27 [beta]`

	the volume can be mounted as read-write by a single Pod. Use ReadWriteOncePod access mode if you want to ensure that only one pod across the whole cluster can read that PVC or write to it. This is only supported for CSI volumes and Kubernetes version 1.22+.
-  ressources : specific quantities of a resource. In this case, the request is for storage
	- storage: (e.g. 8Gi determine 8 Go of storage)

> [!Note]
> You can modifie this values or add other values ​​that are in the global values ​​this will do an override.

---

## Deployment template configuration

This deployments file is a template. This file loops over the values ​​declared in the `values.yaml` file in the deployments section so that it allows you to create the necessary deployments without making several deployment files

```YAML
          {{- range $key, $value := .Values.deployments }}
```

This line initiates a loop that iterates through each element in the deployments array defined in the values (.Values). $key represents the key (name) of each element, and $value represents the associated value.

Kubernetes Deployment Declaration:

```YAML
          apiVersion: apps/v1
          kind: Deployment
```

This section indicates the Kubernetes API version and the resource type, which is a deployment.

Deployment Metadata:

```YAML
          metadata:
            namespace: {{ $value.namespace | default $.Values.global.namespace }}
            name: {{ $key }}
```

These metadata include the deployment's namespace, which is either specified in the values `$value.namespace` or defaults to the global value `$.Values.global.namespace`. The deployment name `$key` is based on the element's key in the array.



Deployment Labels:

```YAML
          labels:
            {{- with $value.labels }}
              {{- toYaml . | nindent 4}}
            {{ else }}
              app: {{ $key }}
            {{- end }}
```
Labels are defined here. If specific labels are configured in the values ($value.labels), they are used. Otherwise, a default label app: $key is used.

Deployment Specification (Spec):

```YAML
          spec:
            replicas: {{ $value.replicaCount | default $.Values.global.replicaCount }}
            selector:
              matchLabels:
                {{- with $value.labels }}
                  {{- toYaml . | nindent 6}}
                {{ else }}
                  app: {{ $key }}
                {{- end }}
            template:
              metadata:
                labels:
                  {{- with $value.labels }}
                    {{- toYaml . | nindent 8}}
                  {{ else }}
                    app: {{ $key }}
                  {{- end }}
              spec:
```

The **replicas** field specifies the desired number of replicas (instances) of the application that should be running in the Kubernetes cluster. The ability to easily scale the number of replicas up or down is one of the key benefits of using Kubernetes deployments. We can scale our application based on demand, ensuring that it can handle varying levels of traffic.


## Containers Informations

**containers** is where detailed information about the pods containers is specified. Each container within a pod is defined by the following block and sections

```YAML
          containers:
            - name: {{ $key }} <-- The name the name for the Container
              imagePullPolicy: {{ $value.containers.pullPolicy | default $.Values.global.image.pullPolicy }}
              image: "{{ $value.containers.image.repository | default $key }}:{{ $value.containers.image.tag  | default $.Values.global.image.tag}}"
```

  - **name** : The name assigned to the container, which can be dynamically generated using the value of `$key`. This name is used to uniquely identify the container within the pod.

  - **imagePullPolicy** : The policy for pulling the container image. It is derived from the configuration, with a default value set to `$.Values.global.image.pullPolicy` if not explicitly defined for the container.

  - **image** : The container image to be used.


#### Resources Managements

The next section deals with configuring resources (CPU and memory) for containers in the Kubernetes deployment.

```YAML
          {{- if $value.containers.resources | default $.Values.global.resources }}
          resources:
            request:
              memory: {{ .request.memory | default $.Values.global.resources.request.memory }}
              cpu: {{ .request.cpu | default $.Values.global.resources.request.cpu }}
            limits:
              memory: {{ .limits.memory | default $.Values.global.resources.limits.memory }}
              cpu: {{ .limits.cpu | default $.Values.global.resources.limits.cpu }}
          {{- end -}}
```
This section checks whether specific resources have been defined for the container. If so, they are added to the resources section. If not, the default values specified in the global parameters are used.

  - request:
      - memory : The amount of memory the container requests.
      - cpu : The amount of processing power (CPU) the container requests.

  - limits:
    - memory : The maximum amount of memory the container can use.
    - cpu : The maximum amount of processing power (CPU) the container can use.

#### Secrets

This part checks whether secrets are specified for the container. If so, they are injected into the container using the **envFrom** directive. This allows the container to access the secret values.

```YAML
...
          {{- with $value.containers.secret }}
          envFrom:
            - secretRef:
                name: {{ . }}
          {{- end -}}
```

#### Spring environment variables

If the configuration specifies Spring environment variables for the container, they are added to the env section. This can be useful for Spring application-specific configuration.

```YAML
...
          {{- if $value.containers.spring }}
          {{- $springValue := index $.Values.spring $value.containers.spring }}
          env:
            - name: {{ $springValue.name }}
              value: {{ $springValue.value }}
          {{- end }}
```

#### Ports

This section deals with port configuration for the container. It first checks whether containerPort are specified for this container. If so, they are added to the ports section. Port name is assigned a unique name.

```YAML
...
          {{- if $value.containers.containerPort }}
          ports:
            - containerPort: {{ $value.containers.containerPort }}
              name: port-{{ $value.containers.ports }}
          {{- end }}
```

 - ports: containerPort is internal port use by application.

 - name: As the port name it was defined with this concatenation of `port-{{ . }}`,
  {{ . }}: This refers to the port number

---
## Probes section

The probes section manages the availability probes (livenessProbe and readinessProbe) for each container in the deployment.


#### Liveness Probe

The livenessProbe sub-section determines whether the container is running correctly. It checks the type, httpGet or tcpSocket, with values depending on the type used.

```YAML
          {{- if $value.containers.livenessProbe }}
          {{- with $value.containers.probe }}
          livenessProbe:
            {{- $livenessProbeType := .type | default $.Values.global.livenessProbe.type -}}

            {{- if eq $livenessProbeType "httpGet" }}
            httpGet:
              path: {{ .path | default $.Values.global.readinessProbe.path }}
              port: {{ .port }}
            {{- else if eq $livenessProbeType "tcpSocket"}}
            tcpSocket:
              port: {{ .port }}
            {{- end }}
            initialDelaySeconds: {{ .initialDelaySeconds | default $.Values.global.livenessProbe.initialDelaySeconds }}
            periodSeconds: {{ .periodSeconds | default $.Values.global.livenessProbe.periodSeconds }}
          {{- end }}
          {{- end }}
```

#### Readiness Probe

The readinessProbe sub-section determines whether the container is ready to receive traffic. It checks the type, httpGet or tcpSocket, with values depending on the type used.

```YAML

          {{- if $value.containers.readinessProbe }}
          {{- with $value.containers.probe }}
          readinessProbe:
            {{- $readinessProbeType := .type | default $.Values.global.readinessProbe.type -}}

            {{- if eq $readinessProbeType "httpGet" }}
            httpGet:
              path: {{ .path | default $.Values.global.readinessProbe.path }}
              port: {{ .port }}
            {{- else if eq $readinessProbeType "tcpSocket"}}
            tcpSocket:
              port: {{ .port }}
            {{- end }}
            initialDelaySeconds: {{ .initialDelaySeconds | default $.Values.global.readinessProbe.initialDelaySeconds }}
            periodSeconds: {{ .periodSeconds | default $.Values.global.readinessProbe.periodSeconds }}
          {{- end }}
          {{- end }}
```
---

## Volumes section

The volumes section manages the configuration of volumes and mounts for each deployment.

#### Volumes Mounts

The volumeMounts specify the mounting points for the volumes in the container.

```YAML
          {{- if $value.containers.volumeMounts }}
          volumeMounts:
            {{- range $index, $volume := $value.containers.volumeMounts }}
            - mountPath: {{ $volume.path }}
              name: {{ $key }}-{{ $index }}
            {{- end }}
          {{- end }}
```

#### Volumes

The volumes sub-section configures the volumes used in pods.

```YAML
          {{- if $value.containers.volumeMounts }}
          volumes:
            {{- range $index, $volume := $value.containers.volumeMounts }}
            - name: {{ $key }}-{{ $index }}
              {{- if $volume.emptyDir }}
              emptyDir: {{ $volume.emptyDir }}
              {{- end }}
              {{- if $volume.persistentVolumeClaim | default false }}
              persistentVolumeClaim:
                claimName: {{ $key }}-pv-claim
              {{- end }}
            {{- end }}
          {{- end }}
          {{- end }}
```

>[!Warning]
> Ne copiez pas les bouts de code, il est possible qu'il  "le fait que certain element soit en trop ou manquant"   et que l'indentation ne soit pas correct

---

## Service template configuration
This file generates Kubernetes objects of type "Service" using values ​​defined in the Values.yaml file. Each Service object corresponds to a specific deployment defined in the services section of the  `Values.yaml` file.

The file begins with a loop that iterates over all the values ​​in the services section of the Values.yaml file.
```YAML
{{- range $key, $value := .Values.services }}
```
>[!Note]
>`{{  $key  }}` is the "key name" (application name) defined in section service in  `Values.yaml` file.

```YAML
...
apiVersion:  v1
kind:  Service
metadata:
  name:  fleetman-{{  $key  }}
 namespace: {{ $value.namespace | default $.Values.global.namespace }}
  labels:
  {{- with $value.labels }}
    {{- toYaml . | nindent 4}}
  {{ else }}
    app: {{ $key }}
  {{- end }}
```
-  Each deployment is defined as a Kubernetes object of type "Service" v1.
-   **name:**The service name is defined as "fleetman-`{{  $key  }}`"
-   **namespace:** namespace is taken from the service values ​​in Values.yaml. If not defined, it uses the global namespace defined in Values.yaml.
-   **labels:** Labels are set using the values ​​specified in Values.yaml. If no label is set, it defaults to  `app: {{  $key  }}`` .

### Service Specification

The Service spec section is defined with the selector, service type, and ports.

```YAML
...
spec:
  selector:
  {{- with $value.labels }}
    {{- toYaml . | nindent 4}}
  {{ else }}
    app: {{ $key }}
  {{- end }}
```

-   **selector:** The selector is set based on the service labels. If no label is set, it defaults to `app: {{ $key }}`

```YAML
...
  type: {{ $value.type | default $.Values.global.service.type }}
{{- end }}
```
-   **type:** The service type is defined using the values ​​specified in Values.yaml. If not defined, it uses the global service type defined in Values.yaml.

```YAML
...
  ports:
   {{- range $index,$port := $value.ports }}
   {{- $firstValuePort := splitList ":" $port| first | int }}
    - name: s-{{ $key }}-{{ $firstValuePort | default $port | int }}
      port: {{ $firstValuePort | default $port | int }}
      targetPort: {{  splitList ":" $port | last | default $port | int  }}
      protocol: {{$port.protocol | default $.Values.global.service.protocol }}
{{- end }}
```
- **name:** As the port name it was defined with this concatenation of "s-`{{ $key }}`-internalPort"
- **ports:** Ports are specified by iterating over the ports defined for the service in Values.yaml. The first port is used for call the service by this port.
- **targetPort:** Is the port internal use by your application which you want to point to.
- **protocol:** 
    supports the following protocols with Services:
    - SCTP
    - TCP (the default)
    - UDP

> [!Note]
> Each generated Service object is separated by `---` in the final YAML file.

```YAML
...
---
{{- end}}
```
The `{{- end}}` close the loop

---

## Namespace template configuration

Condition for Non-Default Namespace:

```YAML
          {{- if ne $.Values.global.namespace "default" }}
```

This line uses the `ne` (not equal) function to check if the value of `$.Values.global.namespace` is not equal to "default". If the condition is true, the following block is included in the final rendering.

Namespace Declaration:

```YAML
          apiVersion: v1
          kind: Namespace
          metadata:
            name: {{ $.Values.global.namespace }}
            labels:
              name: {{ $.Values.global.namespace }}
          {{- end -}}
```

If the condition is true, this part of the code generates a Namespace object with the name and label defined by the value of the global namespace. This creates a new namespace in the Kubernetes cluster.


## Persistent volume configuration


Loop Over Persistent Volume Claims:

```YAML
          {{- range $key, $value := .Values.persistentVolumesClaim }}
```

This line initiates a loop that iterates over each key-value pair in the persistentVolumesClaim section of the Helm chart values.

Persistent Volume Claim Definition:

```YAML
          apiVersion: v1
          kind: PersistentVolumeClaim
          metadata:
            name: {{ $key }}
            namespace: {{ $value.namespace | default $.Values.global.namespace }}
```

For each iteration, this block generates a PersistentVolumeClaim object with a name based on the current key. The namespace is determined by the value specified in the chart, or it defaults to the global namespace if not provided.

Spec Section:

```YAML
          spec:
            {{- with $value.accessModes }}
            accessModes:
              {{- toYaml . | nindent 4 }}
            {{- end }}
            {{- with $value.resources }}
            resources:
              {{- toYaml . | nindent 4 }}
            {{- end }}
          {{- end -}}
```

If the spec key is defined within the current persistent volume claim, it is included in the YAML output. The toYaml function is used to convert the spec section to YAML format, and the nindent function is used to apply indentation.


>[!Warning]
>pay attention to the indentation in the template files.

> [!CAUTION]
> If you modify a value and is not correct, this can create an error ! Please make sure if you modify.
