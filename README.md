# Best Buy Store Application

## Overview

Welcome to the Best Buy Store application!

The Best Buy Store Application is a cloud-native, microservices-based e-commerce demo app designed to showcase modern application architecture principles. The app uses a polyglot programming approach, an event-driven design, and leverages both managed and open-source backing services. By deploying this application in a Kubernetes cluster, you can explore the benefits of container orchestration and cloud-native technologies.

Key features include:

- Customer-facing Store-Front: Enabling users to browse and place orders.
- Employee-focused Store-Admin: Facilitating product management and order tracking.
- AI Integration: Harnessing GPT-4 and DALL-E for generating intelligent product descriptions and creative images.
- Event-Driven Order Processing: Using Azure Service Bus for scalable and reliable messaging.
- Persistent Data Storage: MongoDB for storing product and order information.
- Microservices Architecture: Independent services built with different technologies (Vue.js, Node.js, Rust, Python, Go).

The application demonstrates modern software development practices, including infrastructure as code, containerization, and managed services integration, making it a powerful example of a full-stack cloud-native solution.

## Architecture

### Table of Microservice Repositories  

| Service | Description | Github Repo |
| --- | --- | --- |
| `store-front` | Web app for customers to place orders (Vue.js) | [store-front](https://github.com/aka-pharande/CST8915-LA2-Store-Front) |
| `store-admin` | Web app used by store employees to view orders in queue and manage products (Vue.js) | [store-admin](https://github.com/aka-pharande/CST8915-LA2-Store-Admin) |
| `order-service` | This service is used for placing orders (Javascript) | [order-service](https://github.com/aka-pharande/CST8915-LA2-Order-Service) |
| `product-service` | This service is used to perform CRUD operations on products (Rust) | [product-service](https://github.com/aka-pharande/CST8915-LA2-Product-Service) |
| `makeline-service` | This service handles processing orders from the queue and completing them (Golang) | [makeline-service](https://github.com/aka-pharande/CST8915-LA2-Makeline-Service) |
| `ai-service` | Optional service for adding generative text and graphics creation (Python) | [ai-service](https://github.com/aka-pharande/CST8915-LA2-AI-Service) |
| `mongodb` | MongoDB instance for persisted data | [mongodb](https://github.com/docker-library/mongo) |
| `virtual-customer` | Simulates order creation on a scheduled basis (Rust) | [virtual-customer](https://github.com/aka-pharande/CST8915-LA2-Virtual-Customer) |
| `virtual-worker` | Simulates order completion on a scheduled basis (Rust) | [virtual-worker](https://github.com/aka-pharande/CST8915-LA2-Virtual-Worker) |

### Table of Docker Images**:    

| **Service**         | **Docker Image Link**                             |
|----------------------|--------------------------------------------------|
| `store-front`        | [bestbuy-store-front](https://hub.docker.com/r/akapharande/bestbuy-store-front) |
| `store-admin`        | [bestbuy-store-admin](https://hub.docker.com/r/akapharande/bestbuy-store-admin) |
| `order-service`      | [bestbuy-order-service](https://hub.docker.com/r/akapharande/bestbuy-order-service) |
| `product-service`    | [bestbuy-product-service](https://hub.docker.com/r/akapharande/bestbuy-product-service) |
| `makeline-service`   | [bestbuy-makeline-service](https://hub.docker.com/r/akapharande/bestbuy-makeline-service) |
| `ai-service`         | [bestbuy-ai-service](https://hub.docker.com/r/akapharande/bestbuy-ai-service) |
| `virtual-customer`   | [bestbuy-virtual-customer](https://hub.docker.com/r/akapharande/bestbuy-virtual-customer) |
| `virtual-worker`     | [bestbuy-virtual-worker](https://hub.docker.com/r/akapharande/bestbuy-virtual-worker) |


![Logical Application Architecture Diagram](assets/bestbuy-system-architecture.png)



## Getting Started

For detailed deployment instructions, see the [Deployment Guide](./Deployment-guide.md).

## Bonus (CI/CD)

To streamline the CI/CD processes for all microservices, I implemented a **reusable workflow** using GitHub Actions. Instead of duplicating the CI/CD pipeline (`ci_cd.yaml`) in each repository, a (**centralized reusable workflow**)[https://github.com/aka-pharande/CST8915-LA2-BestBuy-App/blob/main/.github/workflows/ci_cd.yaml] was created in this repository. This workflow is invoked by each microservice repository, passing the required variables and secrets.

### **Benefits**
- **Centralized Management**: CI/CD logic is maintained in one place, reducing duplication.
- **Consistency**: Ensures uniform build, test, and deployment practices across all services.
- **Efficiency**: Simplifies updates and maintenance.

### **Key Features**
1. **Build and Test**: Builds Docker images and runs unit and integration tests.
2. **Push to Docker Hub**: Publishes Docker images with version and `latest` tags.
3. **Deploy to Kubernetes**: Deploys microservices to AKS with seamless integration with Azure Service Bus.

### **Example Configuration**
Below is how a microservice (e.g., `store-front`) invokes the reusable workflow:

```yaml
name: Build, Test, and Deploy

on:
  push:
    branches:
      - main

jobs:
  use-reusable:
    uses: aka-pharande/CST8915-LA2-BestBuy-App/.github/workflows/ci_cd.yaml@main # Reference to the reusable workflow
    secrets:
      DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
      DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
      KUBE_CONFIG_DATA: ${{ secrets.KUBE_CONFIG_DATA }}

```

### **Demo Video**
The [Demo Video](https://youtu.be/your-demo-video-link) showcases:
- The application in action after deployment to the AKS cluster.
- AI-generated product descriptions and images using GPT-4 and DALL-E.
- Integration with the managed Azure Service Bus.
- CI/CD pipelines automating build, test, and deployment for all microservices.