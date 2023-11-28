**Author : Dimitry.C - Valentin.L**

# Fleetman 🚚

---

![Result](https://github.com/MitryDim/fleetman/assets/80764455/10f1214c-532e-4448-9ecb-f5ffb1f14cc2)

## Summary

- [Project description](https://github.com/MitryDim/fleetman/blob/main/README.md#project-description)
- [Objective of the projet](https://github.com/MitryDim/fleetman/blob/main/README.md#Objective-of-the-projet)
  - [Elements at our disposal](https://github.com/MitryDim/fleetman/blob/main/README.md#Elements-at-our-disposal)
- [Installation and Launching Fleetman with Helm](https://github.com/MitryDim/fleetman/tree/main#installation-and-launching-fleetman-with-helm)
  - [Prerequisites](https://github.com/MitryDim/fleetman/tree/main#prerequisites)
  - [Clone the Project](https://github.com/MitryDim/fleetman/tree/main#Clone-the-Project)

---

## Project description

The project is a web application that allows real-time tracking of a fleet of vehicles performing deliveries.

## Objective of the project

The goal of this project is to deploy this application with [Kubernetes](https://kubernetes.io/) as well as with the package manager [Helm](https://helm.sh/fr/), which will allow us to configure and deploy our application efficiently.

### Elements at our disposal :

Pour ce projet, nous utiliserons les images Dockers de supinfo4kube pour l'application et mongo pour la base de données.

- **[fleetman-position-simulator](https://hub.docker.com/r/supinfo4kube/position-simulator)** : a Spring Boot application that continuously transmits fictitious vehicle positions.
- **[fleetman-queue](https://hub.docker.com/r/supinfo4kube/queue)** : an Apache ActiveMQ queue that receives and transmits these positions.
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

**Value ENV for spring profile**

**_Spring values_**

> [!Note]
> The spring value is an environment value it was defined in the application you must choose local or prod.

```yaml
spring:
  local:
    name: SPRING_PROFILES_ACTIVE
    value: local-microservice
  prod:
    name: SPRING_PROFILES_ACTIVE
    value: production-microservice
```

**_Configuration of the global values_**
The `global` section in the configuration file holds essential parameters that influence the overall behavior of the Fleetman application.

the global configuration start with this section :

```YAML
global:
```

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



> [!Warning]
> Default value for namespace /!\ don't touch for this moment because the application have one bug if is not in default namespace.

Default Memory and CPU usage if their not given by the user, based on Computer Capacity

```YAML
  resources:
    requests:
      memory: "256Mi"
      cpu: "250m"
    limits:
      memory: "512Mi"
      cpu: "500m"
```

- **Replica Count**: Sets the default number of replicas for deployments. Replicas represent the number of identical pods running the application.

- **Spring Profile**: Controls the Spring profile used for the application. The value is dependent on the environment (ENV) variable for the Spring profile. Options include `local` for local development and `prod` for production development.

- **Image Configuration:** Defines the default tag and pull policy are specified, providing a starting point for the deployment.

- **Ports Configuration:** Specifies the internal ports used by the application. If an internal and external port need to differ, a mapping can be defined (e.g., `InternalPort:ExternalPort`). For example, if the type is NodePort, the externally accessible port would be `36500`, pointing to the internal port `80`.

> [!Note]
> This global configuration provides a foundation for Fleetman's deployment, with default values that can be adjusted based on specific deployment requirements.

```YAML
global:
  ...
  service:
    type: ClusterIP  #Default value is ClusterIp the possible type in this project was NodePort and ClusterIp or LoadBalancer https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types
    protocol: TCP #Default protocol is TCP Docs : https://kubernetes.io/docs/reference/networking/service-protocols/)
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

```YAML
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

- **Service Configuration:**
      -   Type: `ClusterIP` (Default)
      -   Protocol: `TCP` (Default)
      -   Liveness Probe: [^1]
          -   Type: `httpGet`
          -   Path: `/`
          -   Initial Delay: `30` seconds
          -   Period: `10` seconds
      -   Readiness Probe:
          -   Type: `httpGet`
          -   Path: `/`
          -   Initial Delay: `30` seconds
          -   Period: `10` seconds
      -   Description: Defines the default configuration for Kubernetes services. The service type is set to `ClusterIP` by default, but alternatives include `NodePort` and `LoadBalancer`. The protocol is set to `TCP`, and liveness and readiness probes are configured to monitor the health of the application.
  [^1]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/

**_Configuration of deployments values_**
The deployments value is the value use for the differents deployment.

- mongodb
- queue
- position-simulator
- position-tracker
- api-gateway
- webapp

You can modifie this values or add other values ​​that are in the global values ​​this will do an override.

```YAML
deployments:
  mongodb: <-- name of your deployement
    containers:
      image:
        repository: mongo
        tag: "3.6.23" <-- version of image docker
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
        probe:
          port: 8161
        livenessProbe: true
  position-simulator:
    containers:
      image:
        repository: supinfo4kube/position-simulator
        tag: "1.0.1"
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
  namespace: {{ $value.namespace | default $.Values.global.namespace }}
  name: {{ $key }}
  labels:
  {{- with $value.labels }}
    {{- toYaml . | nindent 4}}
  {{ else }}
    app: {{ $key }}
  {{- end }}
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
      containers:
        - name: {{ $key }}
          imagePullPolicy: {{ $value.containers.pullPolicy | default $.Values.global.image.pullPolicy }}
          image: "{{ $value.containers.image.repository | default $key }}:{{ $value.containers.image.tag  | default $.Values.global.image.tag}}"
          {{- with $value.containers.resources | default $.Values.global.resources }}
          resources:
            requests:
              memory: {{ .requests.memory }}
              cpu: {{ .requests.cpu }}
            limits:
              memory: {{ .limits.memory }}
              cpu: {{ .limits.cpu }}
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
