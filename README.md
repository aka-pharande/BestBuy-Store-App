# Best Buy Store Application

Welcome to the Best Buy Store application.

This sample demo app consists of a group of containerized microservices that can be easily deployed into a Kubernetes cluster. This is meant to show a realistic scenario using a polyglot architecture, event-driven design, and common open source back-end services (eg - RabbitMQ, MongoDB). The application also leverages OpenAI's models to generate product descriptions and images. This can be done using either [Azure OpenAI](https://learn.microsoft.com/azure/ai-services/openai/overview) or [OpenAI](https://openai.com/).

## Architecture

The application has the following services: 

| Service | Description | Github Repo |
| --- | --- | --- |
| `store-front` | Web app for customers to place orders (Vue.js) | [LA2-Store-Front](https://github.com/aka-pharande/CST8915-LA2-Store-Front) |
| `store-admin` | Web app used by store employees to view orders in queue and manage products (Vue.js) | [LA2-Store-Admin](https://github.com/aka-pharande/CST8915-LA2-Store-Admin) |
| `order-service` | This service is used for placing orders (Javascript) | [LA2-Order-Service](https://github.com/aka-pharande/CST8915-LA2-Order-Service) |
| `product-service` | This service is used to perform CRUD operations on products (Rust) | [LA2-Product-Service](https://github.com/aka-pharande/CST8915-LA2-Product-Service) |
| `makeline-service` | This service handles processing orders from the queue and completing them (Golang) | [LA2-Makeline-Service](https://github.com/aka-pharande/CST8915-LA2-Makeline-Service) |
| `ai-service` | Optional service for adding generative text and graphics creation (Python) | [LA2-AI-Service](https://github.com/aka-pharande/CST8915-LA2-AI-Service) |
| `rabbitmq` | RabbitMQ for an order queue | [rabbitmq](https://github.com/docker-library/rabbitmq) |
| `mongodb` | MongoDB instance for persisted data | [mongodb](https://github.com/docker-library/mongo) |
| `virtual-customer` | Simulates order creation on a scheduled basis (Rust) | [LA2-Virtual-Customer](https://github.com/aka-pharande/CST8915-LA2-Virtual-Customer) |
| `virtual-worker` | Simulates order completion on a scheduled basis (Rust) | [LA2-Virtual-Worker](https://github.com/aka-pharande/CST8915-LA2-Virtual-Worker) |


![Logical Application Architecture Diagram](assets/Algonquin%20Pet%20Store%20On%20Steroids.png)

## Run the app on Azure Kubernetes Service (AKS)

You can use the kubernetes YAML files provided in the [Deployment Files](./Deployment%20Files/) folder to deploy the app to an AKS cluster.



