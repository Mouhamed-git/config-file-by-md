## INTRODUCTION 

As I certainly did, you must have encountered problems with some configuration files in project 3 (Refactor Monolith to Microservices and Deploy)😂 .

I decided to share all configuration files that will allow the validate a project.🤔

## LET'S GO 🚀
> ### ** 🔵 [!FILE1]DOCKER-COMPOSE-BUILD** 
> ```yaml
>  version: "3"
>  services:
>   reverseproxy:
>    build:
>      context: ./directory-reverse-proxy/ # ./udagram-reverseproxy/ for exemple
>      dockerfile: DockerFile
>    image: your-reverseproxy-image-name
>  backend_user:
>    build:
>      context: ./directory-udagram-api-user/
>      dockerfile: DockerFile
>    image: your-udagram-api-user-image-name
>  backend_feed:
>    build:
>      context: ./directory-udagram-api-feed/
>      dockerfile: DockerFile
>    image: your-udagram-api-feed-image-name
>  frontend:
>    build:
>      context: ./udagram-frontend/
>      dockerfile: DockerFile
>    image: your-frontend-image-name
 
> ### ** 🔵 [!FILE2] # **DOCKER-COMPOSE** 
> ```yaml
>  version: "3"
>  services:
>    reverseproxy:
>      image: your-reverse-proxy-image-name #reverseproxy for exemple
>      ports:
>        - 8080:8080
>      restart: always
>      depends_on:
>        - backend-user
>        - backend-feed
>    backend-user:
>      image: your-udagram-api-user-name
>      environment:
>        POSTGRES_USERNAME: $POSTGRES_USERNAME
>        POSTGRES_PASSWORD: $POSTGRES_PASSWORD
>        POSTGRES_DB: $POSTGRES_DB
>        POSTGRES_HOST: $POSTGRES_HOST
>        AWS_REGION: $AWS_REGION
>        AWS_PROFILE: $AWS_PROFILE
>        AWS_BUCKET: $AWS_BUCKET
>        JWT_SECRET: $JWT_SECRET
>        URL: $URL
>    backend-feed:
>      image: your-udagram-api-feed-name
>      volumes:
>        - $HOME/.aws:/root/.aws
>      environment:
>        POSTGRES_USERNAME: $POSTGRES_USERNAME
>        POSTGRES_PASSWORD: $POSTGRES_PASSWORD
>        POSTGRES_DB: $POSTGRES_DB
>        POSTGRES_HOST: $POSTGRES_HOST
>        AWS_REGION: $AWS_REGION
>        AWS_PROFILE: $AWS_PROFILE
>        AWS_BUCKET: $AWS_BUCKET
>        JWT_SECRET: $JWT_SECRET
>        URL: $URL
>    frontend:
>      image: your-udagram-frontend-name:local
>      ports:
>        - "8100:80"

> ### ** 🔵 [!FILE3] # **DOCKERFILE-BACKEND (USERS - FEED)** 
> ```bash
>    ## Construire
>    FROM node:13
>
>    # Create workdir app
>    WORKDIR /usr/src/app
>
>    # Copy package.json package-lock.json on current container directory
>    COPY package*.json ./
>
>    # Install dependencies
>    RUN npm ci
>
>    # Copy all others files needed
>    COPY . .
>
>    # Exposed port 8080
>    EXPOSE 8080
>
>    # Run server when container is up
>    CMD [ "npm", "run", "prod" ]

> ### ** 🔵 [!FILE4] # **DOCKERFILE-FRONTEND** 
> ```bash
>    ## Build
>    FROM beevelop/ionic:latest AS ionic
>
>    ## Crreate workdir app
>    WORKDIR /usr/src/app
>
>    # Copy package.json package-lock.json on current container directory
>    COPY package*.json ./
>
>    #Install dependencies
>    RUN npm ci 
>
>    # Copy all others files needed
>    COPY . .
>
>    # Build app
>    RUN ionic build
>
>
>    # Build nginx image
>    FROM nginx:alpine
>
>    # COPY www /usr/share/nginx/html
>    COPY --from=ionic /usr/src/app/www /usr/share/nginx/html

> ### ** 🔵 [!FILE5] # **DOCKERFILE-REVERSEPROXY** 
> ```bash
>    FROM nginx:alpine
>    COPY nginx.conf /etc/nginx/nginx.conf

> ### ** 🔵 [!FILE6] # **NGINX-CONFIGURATION** 
>    ```bash
>    worker_processes 1 ;
>    events {
>        worker_connections 1024 ; 
>    }
>    error_log /dev/stdout debug ;
>    http {
>        sendfile on ;
>        upstream user {
>            server your-user-service-name:8080 ; #user-svc:8080 for exemple
>        }
>        upstream feed {
>            server your-feed-service-name:8080 ; #feed-svc:8080 for exemple
>        }
>        proxy_set_header Host $host ;
>        proxy_set_header X-Real-IP $remote_addr ;
>        proxy_set_header X-NginX-Proxy true ;
>        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for ;
>        proxy_set_header X-Forwarded-Host $server_name ;
>        server {
>            listen 8080 ;
>            location /api/v0/feed {
>                proxy_pass http://feed ;
>            }
>            location /api/v0/users {
>                proxy_pass http://user ;
>            }
>        }
>    }

> ### ** 🔵 [!FILE7] # **ENV-CONFIGMAP** 
>    ```yaml
>    apiVersion: v1
>    kind: ConfigMap
>    metadata:
>      name: your-configmap-name #udagram-configmap for exemple
>    data:
>      AWS_REGION: us-east-1
>      AWS_PROFILE: default
>      AWS_BUCKET: your-bucket-name
>      URL: http://localhost:8100

> ### ** 🔵 [!FILE8] # **ENV-SECRET** 
>    ```yaml
>    apiVersion: v1
>    kind: Secret
>    metadata:
>      name: your-secret-name #udagram-env-secret for exemple
>    type: Opaque
>    stringData:
>      POSTGRES_USERNAME: your-username
>      POSTGRES_PASSWORD: your-password
>      POSTGRES_HOST: your-host-name
>      POSTGRES_DB: your-database-name
>      JWT_SECRET: your-jwt-secret

> ### ** 🔵 [!FILE9*] # **AWS-SECRET** 
>    ```yaml
>   apiVersion: v1
>   kind: Secret
>   metadata:
>     name: your-secret-name #udagram-aws-secret for exemple
>   type: Opaque
>   data:
>     AWS_ACCSS_KEY_ID: YOUR-AWS_ACCSS_KEY_ID #base64
>     AWS_SECRET_ACCESS_KEY: YOUR-AWS_SECRET_ACCESS_KEY #base64
>     AWS_SESSION_TOKEN: YOUR-AWS_SESSION_TOKEN #base64

> ### ** 🔵 [!FILE10*] # **DEPLOYMENT (USERS - FEED - FRONTEND)** 
>    ```yaml
>    apiVersion: apps/v1
>    kind: Deployment
>    metadata:
>      name: your-name #backend-user for-exemple
>      labels:
>        app: your-name
>    spec:
>      replicas: 1
>      selector:
>        matchLabels:
>          app: your-name
>      template:
>        metadata:
>          labels:
>            app: your-name
>        spec:
>          containers:
>            - name: your-name
>              image: your-dockerhub-username/your-docker-image-name:v1
>              envFrom:
>                - secretRef:
>                    name: your-env-secret-name
>                - secretRef:
>                    name: your-aws-secret-name
>                - configMapRef:
>                    name: your-configmap-name
>              ports:
>                - containerPort: 8080
>              resources:
>                limits:
>                  cpu: your-value
>                  memory: your-value
>                requests:
>                  cpu: your-value
>                  memory: your-value


> ### ** 🔵 [!FILE11*] # **SERVICE (USERS - FEED - FRONTEND)** 
>   ```yaml
>   apiVersion: v1
>   kind: Service
>   metadata:
>     labels:
>       app: your-deployment-app-name
>     name: your-service-name
>     namespace: default
>     selfLink: /api/v1/namespaces/default/services/your-deployment-app-name
>   spec:
>     ports:
>       - port: 8080
>         protocol: TCP
>         targetPort: 8080
>     selector:
>       app: your-deployment-app-name
>     sessionAffinity: None
>     type: ClusterIP
>   status:
>     loadBalancer: {}

> ### ** 🔵 [!FILE12*] # **DEPLOYMENT REVERSEPROXY**
>    ```yaml 
>    apiVersion: apps/v1
>    kind: Deployment
>    metadata:
>      name: your-name #reverseproxy for exemple
>      labels:
>        app: your-name
>    spec:
>      replicas: 1
>      selector:
>        matchLabels:
>          app: your-name
>      template:
>        metadata:
>          labels:
>            app: your-name
>        spec:
>          containers:
>            - name: your-name
>              image: your-dockerhub-username/your-docker-image-name:v1
>              imagePullPolicy: Always
>              resources:
>                requests:
>                  memory: your-value
>                  cpu: your-value
>                limits:
>                  memory: your-value
>                  cpu: your-value
>              ports:
>                - containerPort: 8080
>          restartPolicy: Always


> ### ** 🔵 [!FILE13*] # **SERVICE REVERSEPROXY** 
>    ```yaml
>    apiVersion: v1
>    kind: Service
>    metadata:
>      name: your-service-name #reverseproxy-svc for exemple
>    spec:
>      ports:
>        - protocol: TCP
>          port: 80
>          targetPort: 80
>      selector:
>        app: your-deployment-app-name


> ### ** 🔵 [!FILE14*] # **TRAVIS CI** 
>    ```yaml
>    language: node_js
>    node_js:
>      - 13
>
>    services:
>      - docker
>
>    # Pre-testing installs
>    install:
>      - echo "nothing needs to be installed"
>
>    # Scripts to be run such as tests
>    before_script:
>      - echo "no tests"
>
>    script:
>      - docker --version # print the version for logging
>      - docker-compose -f docker-compose-build.yaml build --parallel
>      - docker images # print all images building
>      - docker tag your-docker-reverseproxy-image-name your-dockerhub-username/your-reverseproxy-image-nam:v1
>      - docker tag your-docker-udagram-user-image-name your-dockerhub-username/your-docker-udagram-user-image-name:v1
>      - docker tag your-docker-udagram-feed-image-name your-dockerhub-username/your-docker-udagram-feed-image-name :v1
>      - docker tag  your-docker-frontend-image-name your-dockerhub-username/your-docker-frontend-image-name:v1
>
>    after_success:
>      - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
>      - docker push your-dockerhub-username/your-reverseproxy-image-nam:v1
>      - docker push your-dockerhub-username/your-docker-udagram-user-image-name:v1
>      - docker push your-dockerhub-username/your-docker-udagram-feed-image-name :v1
>      - docker push your-dockerhub-username/your-docker-frontend-image-name:v1


>  🔵 [!SUCCESS] # **I HOPE WELL THESE FILES CAN HELP YOU🚀** 
___
# Thank you ! 🤗

- **LinkedIn** : https://www.linkedin.com/in/mouhamad-diack-b0b1541a3/
___
