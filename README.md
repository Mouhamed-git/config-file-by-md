## INTRODUCTION 

As I certainly did, you must have encountered problems with some configuration files on Kubernetes in project 3 (Refactor Monolith to Microservices and Deploy)😂 .

I decided to share all configuration files that will allow to validate a project.🤔
Cancel changes
## LET'S GO 🚀

> ### ** 🔵 [!FILE1] # **ENV-CONFIGMAP** 
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

> ### ** 🔵 [!FILE2] # **ENV-SECRET** 
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

> ### ** 🔵 [!FILE3*] # **AWS-SECRET** 
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

> ### ** 🔵 [!FILE4*] # **DEPLOYMENT (USERS - FEED - FRONTEND)** 
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
>                  cpu: "your-value" #2000m for example
>                  memory: "your-value" #2048Mi for example
>                requests:
>                  cpu: "your-value" #500m for example
>                  memory: "your-value" #1024Mi for example


> ### ** 🔵 [!FILE5*] # **SERVICE (USERS - FEED - FRONTEND)** 
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

> ### ** 🔵 [!FILE6*] # **DEPLOYMENT REVERSEPROXY**
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
>                  memory: "your-value" #64Mi for example
>                  cpu: "your-value" #250m for example
>                limits:
>                  memory: "your-value" #1024Mi for example
>                  cpu: "your-value" #500m for example
>              ports:
>                - containerPort: 8080
>          restartPolicy: Always


> ### ** 🔵 [!FILE7*] # **SERVICE REVERSEPROXY** 
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

> ### ** 🔵 [!FILE8] # **NGINX-CONFIGURATION** 
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

>  🔵 [!SUCCESS] # **I HOPE WELL THESE FILES CAN HELP YOU🚀** 
___
# Thank you ! 🤗

- **LinkedIn** : https://www.linkedin.com/in/mouhamad-diack-b0b1541a3/
___
