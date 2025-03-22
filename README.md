# Práctica de CI/CD: Ciclo de Vida del Desarrollo - Ramón Meza

# Flask Redis Counter - CI/CD Práctica

## Descripción y Función

**Flask-Redis-Counter** es una aplicación web simple desarrollada con **Flask** que implementa un contador persistente utilizando **Redis** como base de datos en memoria. Su objetivo es permitir a los usuarios incrementar o decrementar un contador a través de una interfaz web, manteniendo su valor incluso después de reiniciar la aplicación.

Esta aplicación tiene como propósito ofrecer un ejemplo práctico y didáctico del uso conjunto de **Flask** y **Redis** para la gestión de datos en tiempo real.

---

## Características principales

- **Interfaz web** sencilla para visualizar e incrementar el contador.
- **Persistencia con Redis**: el contador conserva su valor entre reinicios.
- **Ideal para aprender Flask y Redis** en un contexto real.
- **Ejemplo funcional para CI/CD**: se integra con pipelines de despliegue.
- **Despliegue automatizado** con Kubernetes usando **ArgoCD** y **Kind**.

---

## Infraestructura y despliegue

Este repositorio representa el proyecto central de la práctica del **Ciclo de vida de un desarrollo - CI/CD**, donde se realiza el despliegue de una aplicación Flask conectada a Redis, usando manifiestos de Kubernetes y desplegada en un clúster local con **Kind**, gestionado mediante **ArgoCD**.

### Tecnologías usadas:
- **Flask** como framework web en Python.
- **Redis** como base de datos en memoria para persistencia.
- **Docker** para contenerizar la aplicación.
- **Docker Hub** como repositorio de la imagen.  
- **Kubernetes** como orquestador de contenedores (vía Kind).
- **ArgoCD** para el despliegue continuo de manifiestos Kubernetes.
- **CircleCI** como herramienta de integración y despliegue continuo.

---

## Funcionamiento de la aplicación

Cada vez que se accede a la página principal de la aplicación Flask, se muestra un mensaje con el número de veces que ha sido cargada. Este contador se incrementa y se almacena en **Redis**, lo que permite que el valor persista entre sesiones y reinicios.

Redis sirve como el backend de almacenamiento rápido y persistente para esta información, eliminando la necesidad de usar bases de datos relacionales.

---

## CI/CD

- Se utiliza **CircleCI** para construir y testear la aplicación, escanear vulnerabilidades y subir la imagen a Docker Hub.
- Se reemplaza dinámicamente el tag de la imagen Docker en los manifiestos de Kubernetes.
- Se utiliza **ArgoCD** para aplicar y sincronizar los manifiestos en el clúster Kubernetes creado con **Kind**.

---

## Estado del Proyecto

Proyecto funcional y desplegable. Ideal como base para aprender sobre:
- Desarrollo de aplicaciones con Flask
- Uso de Redis como base de datos en memoria
- Contenerización y despliegue con Kubernetes
- Automatización con CircleCI y ArgoCD


## Repositorios y Recursos

- Código fuente: [GitHub - flask-redis-counter-Practica-RamonMeza](https://github.com/RAMON1743/flask-redis-counter-Practica-RamonMeza)
- Imágenes Docker: [Docker Hub - ramon1743/flask-redis-counter-practica-ramonmeza](https://hub.docker.com/repository/docker/ramon1743/flask-redis-counter-practica-ramonmeza/general)
  
 ![dockerhub](images/dockerhub.png)
 
- Pipelines CircleCI: [CircleCI Pipelines](https://app.circleci.com/pipelines/github/RAMON1743)
  
 ![circleci](circleci.png)

 

---

## Pipelines del Proyecto

### Build
Este pipeline cubre desde la configuración inicial hasta la subida de artefactos:
- Configuración del entorno y contenedores.
- Instalación del CLI de ArgoCD y configuración de Git.
- Definición de variables (SECRET_KEY, DOCKERHUB_REPO).
- Instalación de dependencias del proyecto.
- Ejecución de pruebas unitarias.
- Análisis estático con `pylint` y `flake8`.
- Construcción de imagen Docker.
- Subida de artefactos y resultados de prueba.

### Vulnerability Scan
Pipeline para análisis de vulnerabilidades:
- Instalación de Node.js y Snyk CLI.
- Autenticación con Snyk y escaneo del proyecto.

### Push Docker
Pipeline para subir la imagen Docker a Docker Hub:
- Configura entorno remoto para Docker.
- Login en Docker Hub.
- Construcción y tag de imagen.
- Push de `latest` y `:CIRCLE_SHA1`.

### Deploy to K8s
Pipeline para desplegar en Kubernetes:
- Instalación de herramientas (kubectl, Git, ArgoCD CLI).
- Configuración de acceso a Kubernetes.
- Clonado del repo de manifiestos.
- Ejecución de `argocd app sync` y monitoreo de salud. 

![pipelines](pipelines.png)

---

## Integraciones

- **Análisis de Código**: Integración con **SonarQube** para análisis estático.
  
    ![SonarQube](SonarQube.png)
  
- **Vulnerabilidades**: Escaneo automático con **Snyk** desde CircleCI.
  
   ![Snyk](Snyk.png)
---

## Despliegue en Minikube con ArgoCD

### Requisitos previos
- Docker
- Minikube
- kubectl
- ArgoCD CLI

### Pasos para el despliegue

1. **Iniciar Minikube**
```bash
minikube start
```

2. **Verificar el estado del clúster**
```bash
kubectl get nodes
```

3. **Instalar ArgoCD**
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

4. **Verificar los pods de ArgoCD**
```bash
kubectl get pods -n argocd
```

![pods_argocd](pods_argocd.png.png)


5. **Exponer la interfaz web de ArgoCD**
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:80
```

6. **Obtener la contraseña del usuario admin**
```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

7. **Acceder a la interfaz**

Visita: [https://localhost:8080](https://localhost:8080)

---

## Panel de ArgoCD - flask-redis-app

- **Estado**: Saludable y sincronizado.
- **Historial**: Última sincronización exitosa.
- **Árbol de recursos**: Incluye `flask-redis-config`, `flask-redis-secret`, pods de la app.
- **Acciones**: Sincronización, revisión de estado, rollback, entre otras.

![Panel de ArgoCD](ArgoCD.png)

---

## Conexión desde Kind

La aplicación `flask-redis-app` se despliega en Kubernetes y está enlazada correctamente al servicio de Redis, mostrando el contador actualizado en cada visita. 

![App-argoCD](App-argoCD.png)

Puedes acceder a la app desde el navegador usando la URL generada por el port-forward y mediante de Kind de flask-redis-app, enlazaría a la aplicación de **Flask-Redis-Counter**. 

![Flask-Redis-Counter](Flask-Redis-Counter.png)

  



