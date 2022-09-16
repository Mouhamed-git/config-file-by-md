## INTRODUCTION 

As I certainly did, you must have encountered problems with some configuration files on Kubernetes in project 3 (Refactor Monolith to Microservices and Deploy)😂 .

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

>  🔵 [!SUCCESS] # **I HOPE WELL THESE FILES CAN HELP YOU🚀** 
___
# Thank you ! 🤗

- **LinkedIn** : https://www.linkedin.com/in/mouhamad-diack-b0b1541a3/
___
