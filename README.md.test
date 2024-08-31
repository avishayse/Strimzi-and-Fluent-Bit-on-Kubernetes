
# Setting Up Kafka with Strimzi and Fluent Bit on Kubernetes

This guide provides step-by-step instructions for setting up Kafka using the Strimzi operator on Kubernetes and configuring Fluent Bit to collect logs from Kafka and forward them to Azure Blob Storage.

## Prerequisites

- A Kubernetes cluster with kubectl configured
- Helm installed on your local machine
- Azure Blob Storage account and container set up

## Step 1: Install the Strimzi Operator

First, install the Strimzi operator to manage Kafka on your Kubernetes cluster.

### Add the Strimzi Helm Repository

\`\`\`bash
helm repo add strimzi https://strimzi.io/charts/
helm repo update
\`\`\`

### Install the Strimzi Operator

\`\`\`bash
kubectl create namespace kafka
helm install strimzi-kafka-operator strimzi/strimzi-kafka-operator --namespace kafka
\`\`\`

## Step 2: Deploy Kafka Cluster

Once the Strimzi operator is installed, you can deploy a Kafka cluster.

### Create a Kafka Custom Resource (CR)

Create a file named \`kafka-cluster.yaml\` with the following content:

\`\`\`yaml
apiVersion: kafka.strimzi.io/v1beta2
kind: Kafka
metadata:
  name: my-cluster
  namespace: kafka
spec:
  kafka:
    version: 3.8.0
    replicas: 3
    listeners:
      - name: plain
        port: 9092
        type: internal
        tls: false
      - name: tls
        port: 9093
        type: internal
        tls: true
    config:
      offsets.topic.replication.factor: 3
      transaction.state.log.replication.factor: 3
      transaction.state.log.min.isr: 2
      default.replication.factor: 3
      min.insync.replicas: 2
      inter.broker.protocol.version: "3.8"
    storage:
      type: jbod
      volumes:
        - id: 0
          type: persistent-claim
          size: 100Gi
          deleteClaim: false
  zookeeper:
    replicas: 3
    storage:
      type: persistent-claim
      size: 100Gi
      deleteClaim: false
  entityOperator:
    topicOperator: {}
    userOperator: {}
\`\`\`

### Apply the Kafka CR

\`\`\`bash
kubectl apply -f kafka-cluster.yaml -n kafka
\`\`\`

### Verify the Kafka Deployment

\`\`\`bash
kubectl get pods -n kafka
\`\`\`

Ensure that the Kafka and Zookeeper pods are running, as well as the Strimzi cluster operator.

## Step 3: Deploy Fluent Bit for Log Forwarding

### Prepare Fluent Bit Configuration

Create a file named \`values.yaml\` with the following configuration for Fluent Bit:

\`\`\`yaml
serviceAccount:
  create: true

config:
  service: |
    [SERVICE]
        flush        1
        log_level    info

  inputs: |
    [INPUT]
        Name        kafka
        Brokers     my-cluster-kafka-bootstrap.kafka:9092
        Topics      test-topic
        Group_Id    fluent-bit-group
        client_id   fluent-bit-client
        format      json
        poll_ms     1000

  outputs: |
    [OUTPUT]
        Name                  azure_blob
        Match                 *
        account_name          your_storage_account_name
        shared_key            your_shared_key
        container_name        logs
        path                  kubernetes/logs
        auto_create_container on
        tls                   on
        blob_type             appendblob
\`\`\`

Replace \`your_storage_account_name\` and \`your_shared_key\` with your Azure Blob Storage credentials.

### Deploy Fluent Bit Using Helm

\`\`\`bash
kubectl create namespace logging
helm install fluent-bit fluent/fluent-bit --namespace logging -f values.yaml
\`\`\`

### Verify Fluent Bit Deployment

\`\`\`bash
kubectl logs -n logging daemonset/fluent-bit
\`\`\`

Ensure that Fluent Bit is running and connected to Kafka and Azure Blob Storage.

## Step 4: Produce Test Messages to Kafka

### Access the Kafka Client Pod

\`\`\`bash
kubectl exec -it broker-kafka-client -- /bin/bash -n kafka
\`\`\`

### Produce a Test Message

\`\`\`bash
kafka-console-producer --broker-list my-cluster-kafka-bootstrap:9092 --topic test-topic
\`\`\`

Type the following JSON message and press \`Enter\`:

\`\`\`json
{"key": "value", "message": "This is a test log message"}
\`\`\`

### Verify Logs in Azure Blob Storage

Use Azure Storage Explorer or the Azure CLI to check if the logs from \`test-topic\` are being stored in your Azure Blob Storage container.

## Step 5: Clean Up

To remove the resources created in this tutorial, you can delete the namespaces:

\`\`\`bash
kubectl delete namespace kafka
kubectl delete namespace logging
\`\`\`

This will remove the Kafka cluster, Strimzi operator, and Fluent Bit deployment from your Kubernetes cluster.
