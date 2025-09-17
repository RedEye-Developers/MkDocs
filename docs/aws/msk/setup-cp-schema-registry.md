### Step 1 :
---
#### Build Dockerfile

This Docker Build for setup ***aws-msk-iam-auth*** plugin and ***AWS-MSK-Authentication*** Env variables by default in New schema-registry image.

``` sql
FROM confluentinc/cp-schema-registry:latest

USER root
ADD --chown=appuser:appuser https://github.com/aws/aws-msk-iam-auth/releases/download/v2.3.3/aws-msk-iam-auth-2.3.3-all.jar /usr/share/java/cp-base-new/
ADD --chown=appuser:appuser https://github.com/aws/aws-msk-iam-auth/releases/download/v2.3.3/aws-msk-iam-auth-2.3.3-all.jar /usr/share/java/schema-registry/

ENV SCHEMA_REGISTRY_KAFKASTORE_SECURITY_PROTOCOL="SASL_SSL"
ENV SCHEMA_REGISTRY_KAFKASTORE_SASL_MECHANISM="AWS_MSK_IAM"
ENV SCHEMA_REGISTRY_KAFKASTORE_SASL_JAAS_CONFIG="software.amazon.msk.auth.iam.IAMLoginModule required;"
ENV SCHEMA_REGISTRY_KAFKASTORE_SASL_CLIENT_CALLBACK_HANDLER_CLASS="software.amazon.msk.auth.iam.IAMClientCallbackHandler"

USER root

EXPOSE 8081
```

Run this cmd to Build Dockerfile.

``` bash
$ docker build -f <DockerFile-Path> -t aws-msk-schema-registry:latest .
```

### Step 2 :
---
#### Setup Docker-Compose

``` yaml
services:
  schema-registry:
    image: aws-msk-schema-registry:latest
    hostname: localhost:schema-registry
    container_name: schema-registry
    ports:
      - "0.0.0.0:8081:8081"
    volumes:
      - "./data:/topic_data"
    environment:
      SCHEMA_REGISTRY_HOST_NAME: schema-registry
      SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS: "<BOOTSTRAP_SERVERS_HOST>:<PORT>"
      SCHEMA_REGISTRY_LISTENERS: http://0.0.0.0:8081
      SR_HOSTNAME: schema-registry
      SR_LISTENERS: http://schema-registry:8081
      SR_DEBUG: 'true'
      SR_KAFKASTORE_TOPIC_SERVERS: SASL_SSL://<BOOTSTRAP_SERVERS_HOST>:<PORT>
      SCHEMA_REGISTRY_LOG4J_LOGGERS: "org.apache.kafka=ERROR,io.confluent.rest.exceptions=FATAL"
      SCHEMA_REGISTRY_LOG4J_ROOT_LOGLEVEL: DEBUG
      AWS_ACCESS_KEY: "<Enter Key>"
      AWS_SECRET_KEY: "<Enter Key>"
```

**For More Details Checkout this Post :** [***Link***](https://medium.com/@anantadurgaprasadar/aws-msk-with-kafka-connect-and-schema-registry-43e86d6bfb3c)