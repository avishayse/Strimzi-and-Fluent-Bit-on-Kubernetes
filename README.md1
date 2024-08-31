# Setting Up Kafka with Strimzi and Fluent Bit on Kubernetes

This guide provides step-by-step instructions for setting up Kafka using the Strimzi operator on Kubernetes and configuring Fluent Bit to collect logs from Kafka and forward them to Azure Blob Storage.

## Prerequisites

- A Kubernetes cluster with kubectl configured
- Helm installed on your local machine
- Azure Blob Storage account and container set up

## Step 1: Install the Strimzi Operator

First, install the Strimzi operator to manage Kafka on your Kubernetes cluster.

### Add the Strimzi Helm Repository

```bash
helm repo add strimzi https://strimzi.io/charts/
helm repo update 

Install the Strimzi Operator

kubectl create namespace kafka
helm install strimzi-kafka-operator strimzi/strimzi-kafka-operator --namespace kafka

Step 2: Deploy Kafka Cluster
Once the Strimzi operator is installed, you can deploy a Kafka cluster.

Create a Kafka Custom Resource (CR)
Create a file named kafka-cluster.yaml with the following content:
