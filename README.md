# STEPS

[:white_check_mark:] - working locally with same code without any issues  
[:white_check_mark:] - working with docker-compose.yml without any issues  
[:white_check_mark:] - working with k8s without any issues  
[:white_check_mark:] - working with helm without any issues  
[ ] - working with skaffold without any issues  

## Prerequisites

1. Install Docker
2. Install kubectl
3. Install minikube
4. Install Node.js
5. Install pnpm
6. Install git
7. Install helm

## Flow

1. Create a folder and name it `kpress-k8s-app`
2. Run the following command in terminal:

    ```bash
    git init
    ```

3. Run the following command in terminal:

    ```bash
    npm init -y
    ```

4. Run the following command in terminal:

    ```bash
    pnpm add express
    ```

5. Run the following command in terminal:

    ```bash
    pnpm add -D nodemon
    ```

6. Create a file named `server.js` and add the following code:

    ```javascript
    const express = require("express")
    const dotenv = require("dotenv")
    const app = express()

    dotenv.config()

    app.get("/", (req, res) => {
      res.send("Hello World!")
    })

    app.listen(process.env.PORT, () => {
      console.log(`Example app listening at ${process.env.APP_URL}:${process.env.PORT}`)
    })
    ```

7. Create a file named `.env` and add the following code:

    ```bash
    PORT=3030
    APP_URL=http://localhost
    ```

8. Create a file named `.gitignore` and add the following code:

    ```bash
    node_modules
    ```

9. Create a file named `Dockerfile` and add the following code:

    ```bash
    FROM node:23-alpine3.20

    # Install pnpm
    RUN npm install -g pnpm

    # Set the working directory
    WORKDIR /app

    # Copy package.json and pnpm-lock.yaml
    COPY package*.json pnpm-lock.yaml ./

    # Install dependencies
    RUN pnpm install --frozen-lockfile

    # Copy the rest of the application code
    COPY . .

    # Remove .env file if it exists
    RUN rm -f .env

    # Replace .env with .env.example
    RUN cp .env.example .env

    # Expose the port on which the server will run
    EXPOSE 3030

    # Start the backend server
    CMD ["pnpm", "start"]
    ```

10. Create a file named `.dockerignore` and add the following code:

    ```bash
    .git
    node_modules
    .env
    ```

11. Create a file named `.env.example` and add the same content as the `.env` file.

## Running the App Locally

1. Run Docker with this command:

    ```bash
    docker run -d \
      --name kpress-db \
      -e POSTGRES_DB=mydb \
      -e POSTGRES_USER=myuser \
      -e POSTGRES_PASSWORD=mypassword \
      -p 5432:5432 \
      postgres
    ```

2. If you don't have Docker installed, install PostgreSQL locally and create a database with the following credentials.

3. Run the following command in terminal:

    ```bash
    pnpm i
    ```

4. Run the following command in terminal:

    ```bash
    pnpm dev
    ```

5. Replace `.env.example` with `.env`.

## Running the App with Docker

1. Run the following command in terminal:

    ```bash
    docker-compose -f docker-compose-dev.yml up -d
    ```

## Running the App with Kubernetes

1. Create a folder for Kubernetes files and name it `k8s`.
2. Create a file named `deployment.yaml` and add the following code:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: kpress-k8s-app
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: kpress-k8s-app
      template:
        metadata:
          labels:
            app: kpress-k8s-app
        spec:
          containers:
            - name: kpress-k8s-app
              image: <your-username-docker>/kpress-k8s-app:latest
              ports:
                - containerPort: 3030
    ```

3. Create a file named `service.yaml` and add the following code:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: kpress-k8s-app
    spec:
      type: LoadBalancer
      selector:
        app: kpress-k8s-app
      ports:
        - protocol: TCP
          port: 80
          targetPort: 3030
    ```

4. Run the following command in terminal:

    ```bash
    git add .
    git commit -m "Initial commit"
    ```

5. Push it to GitHub.

6. Run the following command in terminal:

    ```bash
    docker build -t kpress-k8s-app .
    ```

7. For multi-architecture build, run the following command in terminal:

    ```bash
    docker buildx build --platform linux/amd64,linux/arm/v6,linux/arm/v7 -t kpress-k8s-app .
    ```

8. Run the following command in terminal:

    ```bash
    docker run -p 3030:3030 kpress-k8s-app
    ```

9. Run the following command in terminal:

    ```bash
    docker login
    ```

10. Run the following command in terminal:

    ```bash
    docker tag kpress-k8s-app <your-username-docker>/kpress-k8s-app:latest
    ```

11. Run the following command in terminal:

    ```bash
    docker push <your-username-docker>/kpress-k8s-app:latest
    ```

12. Run the following command in terminal:

    ```bash
    minikube start
    ```

13. Run the following command in terminal:

    ```bash
    minikube status
    ```

    The output should be like this:

    ```bash
    > minikube status
    minikube
    type: Control Plane
    host: Running
    kubelet: Running
    apiserver: Running
    kubeconfig: Configured
    ```

14. Check if kubectl is configured to connect to minikube:

    ```bash
    kubectl get nodes
    ```

    The output should be like this:

    ```bash
    > kubectl get nodes
    NAME       STATUS   ROLES           AGE   VERSION
    minikube   Ready    control-plane   19m   v1.31.0
    ```

15. Run the following command in terminal:

    ```bash
    kubectl apply -f k8s/deployment.yaml
    ```

16. Run the following command in terminal:

    ```bash
    kubectl apply -f k8s/service.yaml
    kubectl apply -f k8s/kpress-db-deployment.yaml
    ```

17. If you want to check the pods, run the following command in terminal:

    ```bash
    kubectl get pods
    ```

18. Run the following command in terminal:

    ```bash
    minikube service kpress-k8s-app
    ```

19. If you want to open the dashboard, run the following command in terminal:

    ```bash
    minikube dashboard
    ```

20. If you want to check the service, run the following command in terminal:

    ```bash
    kubectl get services
    ```

21. If you want to deploy more than one pod, run the following command in terminal:

    ```bash
    kubectl get deployments
    ```

22. If you want to delete the entire service, run the following command in terminal:

    ```bash
    kubectl delete service kpress-k8s-app
    ```

## Running the App with Helm

1. Run the following command in terminal:

    ```bash
    helm create helm
    ```

2. Create a file named `values.yaml` and add the following code:

    ```yaml
    # Default values for kpress-k8s-app.
    # This is a YAML-formatted file.
    # Declare variables to be passed into your templates.

    # This will set the replicaset count more information can be found here: https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/
    replicaCount: 3

    image:
      repository: ru44/kpress-k8s-app
      tag: "latest"
      pullPolicy: Always

    service:
      type: LoadBalancer
      port: 80
      targetPort: 3030
    ```

3. Remove all files inside the `helm/templates` folder and add the following files only: leave `_helpers.tpl` file, `deployment.yaml` file, and `service.yaml` file.

4. Create a file named `deployment.yaml` and add the following code:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: {{ .Chart.Name }}
    spec:
      replicas: {{ .Values.replicaCount }}
      selector:
        matchLabels:
          app: {{ .Chart.Name }}
      template:
        metadata:
          labels:
            app: {{ .Chart.Name }}
        spec:
          containers:
            - name: {{ .Chart.Name }}
              image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
              imagePullPolicy: "{{ .Values.image.pullPolicy }}"
              ports:
                - containerPort: {{ .Values.service.port }}
    ```

5. Create a file named `service.yaml` and add the following code:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: {{ .Chart.Name }}
    spec:
      type: {{ .Values.service.type }}
      ports:
        - protocol: TCP
          port: {{ .Values.service.port }}
          targetPort: {{ .Values.service.targetPort }}
      selector:
        app: {{ .Chart.Name }}
    ```

6. Run the following command in terminal:

    ```bash
    helm repo add bitnami https://charts.bitnami.com/bitnami
    ```

7. Run the following command in terminal:

    ```bash
    helm install kpress-db bitnami/postgresql \
      --set auth.username=myuser,auth.password=mypassword,auth.database=mydb \
      --set fullnameOverride=kpress-db
    ```

8. Run the following command in terminal:

    ```bash
    helm install kpress-release ./helm
    ```

9. If you want to check the pods, run the following command in terminal:

    ```bash
    kubectl get pods
    ```

10. Run the following command in terminal:

    ```bash
    minikube service kpress-k8s-app
    ```

## ArgoCD Steps

1. Run the following commands in terminal:

    ```bash
    helm repo add argo https://argoproj.github.io/argo-helm
    helm repo update

    # Install Argo CD in the `argocd` namespace
    kubectl create namespace argocd
    helm install argo-cd argo/argo-cd -n argocd
    ```

2. Run the following commands in terminal:

    ```bash
    kubectl port-forward svc/argo-cd-argocd-server -n argocd 8080:80

    kubectl get pods -n argocd
    kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 --decode # to get the password
    ```

3. Use the following `argoCD.yaml` file:

    ```yaml
    apiVersion: argoproj.io/v1alpha1
    kind: Application
    metadata:
      name: kpress-app
      namespace: argocd
    spec:
      destination:
        name: ''
        namespace: default
        server: 'https://kubernetes.default.svc'
      source:
        path: helm
        repoURL: 'https://github.com/ru44/kpress'
        targetRevision: HEAD
        helm:
          values: |
            replicaCount: 3
            image:
              repository: ru44/kpress-k8s-app
              tag: "latest"
            service:
              type: LoadBalancer
              port: 80
              targetPort: 3030
            database:
              host: kpress-db.default.svc.cluster.local
              user: myuser
              password: mypassword
              name: mydb
      project: default
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
    ```

---

## Chapter 2

now it will different story.
