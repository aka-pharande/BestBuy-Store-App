
# Best Buy Store Application Deployment Guide

We will be deploying this application in Azure Kubernetes Service. 

## Step 0: Pre-requisites

### Task 1: Install `kubectl`

1. **Installing `kubectl`:**
   - Follow the official installation guide to install `kubectl` on your system. The guide provides detailed instructions for various operating systems.
   - [kubectl Installation Guide](https://kubernetes.io/docs/tasks/tools/)

2. **Verify `kubectl` Installation:**
   - After installing, confirm that `kubectl` is properly set up by running:
     ```bash
     kubectl version --client
     ```
   - You should see the client version information displayed, confirming a successful installation.

---

### Task 2: Install `Azure CLI`

You will need Azure CLI. If you don't have it: [**Install Azure CLI**](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)

---
## Step 1: Clone the Best Buy Store Repository

To begin, clone the [**Best Buy Store Repository**](https://github.com/aka-pharande/CST8915-LA2-BestBuy-App) repository, which contains all necessary deployment files.

 **Review the Deployment Files**:
   - Navigate to the `Deployment Files` folder
   - This folder contains YAML files for deploying all necessary Kubernetes resources, including services, deployments, StatefulSets, ConfigMaps, and Secrets.
   - There's a dedicated deployment file for each microservice if you wish to deploy them individually. Or you could use Kustomize to deploy them all together. 

---
## Step 2: Set Up the AKS Cluster
Create an AKS cluster with one worker node and one master/system node. For this step, you can refer to: [Lab 6](https://github.com/ramymohamed10/Lab6_24F_CST8915)

1. **Log in to Azure Portal:**
   - Go to [https://portal.azure.com](https://portal.azure.com) and log in with your Azure account.

2. **Create a Resource Group:**
   - In the Azure Portal, search for **Resource Groups** in the search bar.
   - Click **Create** and fill in the following:
     - **Resource group name**: `rg-cst8915-la2`
     - **Region**: `Canada`.
   - Click **Review + Create** and then **Create**.

3. **Create an AKS Cluster:**
   - In the search bar, type **Kubernetes services** and click on it.
   - Click **Create** and select **Kubernetes cluster**
   - In the `Basics` tap fill in the following details:
     - **Subscription**: Select your subscription.
     - **Resource group**: Choose `rg-cst8915-la2`.
     - **Cluster preset configuration**: Choose `Dev/Test`.
     - **Kubernetes cluster name**: `BestBuyStoreCluster`.
     - **Region**: Same as your resource group (e.g., `Canada`).
     - **Availability zones**: `None`.
     - **AKS pricing tier**: `Free`.
     - **Kubernetes version**: `Default`.
     - **Automatic upgrade**: `Disabled`.
     - **Automatic upgrade scheduler**: `No schedule`.
     - **Node security channel type**: `None`.
     - **Security channel scheduler**: `No schedule`.
     - **Authentication and Authorization**: `Local accounts with Kubernetes RBAC`.
   - In the `Node pools` tap fill in the following details:
     - Select **agentpool**. Optionally change its name to `masterpool`. This nodes will have the controlplane.
        - Set **node size** to `D2as_v4`.
        - **Scale method**: `Manual`
        - **Node count**: `1`
        - Click `update`
     - Click on **Add node pool**:
        - **Node pool name**: `workerspool`.
        - **Mode**: `User` 
        - Set **node size** to `D2as_v4`.
        - **Scale method**: `Manual`
        - **Node count**: `1`
        - Click `add`
   - Click **Review + Create**, and then **Create**. The deployment will take a few minutes.

4. **Connect to the AKS Cluster:**
   - Once the AKS cluster is deployed, navigate to the cluster in the Azure Portal.
   - In the overview page, click on **Connect**. 
   - Select **Azure CLI** tap.
   - Login to your azure account using the following command:
      ```
      az login
      ```
   - Set the cluster subscription using the command shown in the portal (it will look something like this):
      ```
      az account set --subscription 'subscribtion-id'
      ```

   - Copy the command shown in the portal for configuring `kubectl` (it will look something like this):
     ```
     az aks get-credentials --resource-group rg-cst8915-la2 --name BestBuyStoreCluster
     ```
---

## Step 3: Set Up the AI Backing Services
To enable AI-generated product descriptions and image generation features, you will deploy the required **Azure OpenAI Services** for GPT-4 (text generation) and DALL-E 3 (image generation). This step is essential to configure the **AI Service** component in the Algonquin Pet Store application.

### Task 1: Create an Azure OpenAI Service Instance

1. **Navigate to Azure Portal**:
   - Go to the [Azure Portal](https://portal.azure.com/).

2. **Create a Resource**:
   - Select **Create a Resource** from the Azure portal dashboard.
   - Search for **Azure OpenAI** in the marketplace.

3. **Set Up the Azure OpenAI Resource**:
   - Choose the **East US** region for deployment to ensure capacity for GPT-4 and DALL-E 3 models.
   - Fill in the required details:
     - Resource group: Use an existing one or create a new group.
     - Pricing tier: Select `Standard`.

4. **Deploy the Resource**:
   - Click **Review + Create** and then **Create** to deploy the Azure OpenAI service.

---

### Task 2: Deploy the GPT-4 and DALL-E 3 Models

1. **Access the Azure OpenAI Resource**:
   - Navigate to the Azure OpenAI resource you just created.

2. **Deploy GPT-4**:
   - Go to the **Model Deployments** section and click **Add Deployment**.
   - Choose **GPT-4** as the model and provide a deployment name (e.g., `gpt-4-deployment`).
   - Set the deployment configuration as required and deploy the model.

3. **Deploy DALL-E 3**:
   - Repeat the same process to deploy **DALL-E 3**.
   - Use a descriptive deployment name (e.g., `dalle-3-deployment`).

4. **Note Configuration Details**:
   - Once deployed, note down the following details for each model:
     - Deployment Name
     - Endpoint URL

---

### Task 3: Retrieve and Configure API Keys

1. **Get API Keys**:
   - Go to the **Keys and Endpoints** section of your Azure OpenAI resource.
   - Copy the **API Key (API key 1)** and **Endpoint URL**.

2. **Base64 Encode the API Key**:
   - Use the following command to Base64 encode your API key:
     ```bash
     echo -n "<your-api-key>" | base64
     ```
   - Replace `<your-api-key>` with your actual API key.

---

### Task 4: Update AI Service Deployment Configuration in the `Deployment Files` folder.
1. **Modify Secretes YAML**:
   - Edit the `secrets.yaml` file.
   - Replace `Base64-encoded-API-Key` placeholder with the Base64-encoded value of the `API_KEY`. 
2. **Modify Deployment YAML**:
   - Edit the `ai-service.yaml` file.
   - Replace the placeholders with the configurations you retrieved:
     - `AZURE_OPENAI_DEPLOYMENT_NAME`: Enter the deployment name for GPT-4.
     - `AZURE_OPENAI_ENDPOINT`: Enter the endpoint URL for the GPT-4 deployment.
     - `AZURE_OPENAI_DALLE_ENDPOINT`: Enter the endpoint URL for the DALL-E 3 deployment.
     - `AZURE_OPENAI_DALLE_DEPLOYMENT_NAME`: Enter the deployment name for DALL-E 3.

   Example configuration in the YAML file:
   ```yaml
   - name: AZURE_OPENAI_API_VERSION
     value: "2024-07-01-preview"
   - name: AZURE_OPENAI_DEPLOYMENT_NAME
     value: "gpt-4"
   - name: AZURE_OPENAI_ENDPOINT
     value: "https://<your-openai-resource-name>.openai.azure.com/"
   - name: AZURE_OPENAI_DALLE_ENDPOINT
     value: "https://<your-openai-resource-name>.openai.azure.com/"
   - name: AZURE_OPENAI_DALLE_DEPLOYMENT_NAME
     value: "dall-e-3"
   ```

---
## Step 5: Set Up the Azure Service Bus

You will need to create a Service Bus namespace and a queue. You can do this using the Azure CLI. 

### Task 1: Create Service Bus

```bash
az group create --name <resource-group-name> --location <location>
az servicebus namespace create --name <namespace-name> --resource-group <resource-group-name>
az servicebus queue create --name orders --namespace-name <namespace-name> --resource-group <resource-group-name>
```
---
### Task 2: Create Shared Access Policies for Authentication

**Sender Policy for Order Service** 
```bash
az servicebus queue authorization-rule create --name sender --namespace-name <namespace-name> --resource-group <resource-group-name> --queue-name orders --rights Send
```


**Listener Policy for Makeline Service**
```bash
az servicebus queue authorization-rule create --name listener --namespace-name <namespace-name> --resource-group <resource-group-name> --queue-name orders --rights Listen
```

---
### Task 3: Configure Order Service to Communicate with Azure Service Bus

1. **Get Access Keys**:
   - Go to the **Shared Access Policies** section of your Azure Service Bus resource.
   - Click on **sender** policy and copy the **Primary Key**.

2. **Base64 Encode the API Key**:
   - Use the following command to Base64 encode your API key:
     ```bash
     echo -n "<sender-access-key>" | base64
     ```
   - Replace `<sender-access-key>` with your actual access key.

3. **Get Hostname**:
    - Go to the **Overview** section of your Azure Service Bus resource.
    - Copy the value of **Host name** key.

4. Update Order Service Deployment Configuration in the `Deployment Files` folder.

    **Modify Secrets YAML**:
   - Edit the `secrets.yaml` file.
   - Replace `Base64-encoded-Service-Bus-Sender-Key` placeholder with the Base64-encoded value of the `sender-access-key`. 

    **Modify Order Service Yaml**:
    - Edit the `order-service.yaml` file.
    - Set the value of the `ORDER_QUEUE_HOSTNAME` env variable to the hostname from the previous step.
---

### Task 5: Configure Makeline Service to Communicate with Azure Service Bus

1. **Get Access Keys**:
   - Go to the **Shared Access Policies** section of your Azure Service Bus resource.
   - Click on **listener** policy and copy the **Primary Key**.

2. **Base64 Encode the API Key**:
   - Use the following command to Base64 encode your API key:
     ```bash
     echo -n "<listener-access-key>" | base64
     ```
   - Replace `<listener-access-key>` with your actual access key.

3. **Get Hostname**:
    - Go to the **Overview** section of your Azure Service Bus resource.
    - Copy the value of **Host name** key.

4. Update Makeline Service Deployment Configuration in the `Deployment Files` folder.

    **Modify Secrets YAML**:
   - Edit the `secrets.yaml` file.
   - Replace `Base64-encoded-Service-Bus-Listener-Key` placeholder with the Base64-encoded value of the `listener-access-key`. 

    **Modify Makeline Service Yaml**:
    - Edit the `makeline-service.yaml` file.
    - Set the value of the  `ORDER_QUEUE_URI` env variable to "amqps://$HOSTNAME" where $HOSTNAME should be replaced with hostname from the previous step.

---
## Step 5: Deploy and Validate the Application
   
### Task 1: Deploy the Application

- **Option 1:** You can deploy all the microservices together using the Kustomization.yaml which combines all the microservices deployments into one. This makes for an easier deployment without having to replicate the deployment configuration.

   ```bash
   kubectl apply -k .
   ```

- **Option 2:** You can deploy individual microservices using their respective deployment yaml file. Here's an example of how to deploy order-service.

   ```bash
   kubectl apply -f order-service.yaml
   ```

---
### Task 2: Validate the Deployment
- Check Pods and Services:
   ```bash
   kubectl get pods
   kubectl get services
   ```
- Test Frontend Access:
   - Locate the external IPs for store-front and store-admin services:
   ```bash
   kubectl get services
   ```
   - Access the Store Front app at the external IP on port 80.
   - Access the Store Admin app at the external IP on port 80.

---
## Step 6: Testing

### Task 1: Automated Testing With Virtual Customer and Worker
   ```bash
   kubectl apply -f admin-tasks.yaml
   ```
- Monitor Virtual Customer:
   ```bash
   kubectl logs -f deployment/virtual-customer
   ```
- Monitor Virtual Worker:
   ```bash
   kubectl logs -f deployment/virtual-worker
   ```

### Task 2: Manual Testing of AI-Generated Descriptions and Images:
- Store-Front: Access the customer-facing app at <External-IP>:80. Create an order and verify it appears in the Store-Admin app.
- Store-Admin: Manage products and complete orders via <External-IP>:80.
- AI Service: Submit a product description and image request and ensure the response includes a generated description and image.