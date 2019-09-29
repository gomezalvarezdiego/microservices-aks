## Services

The solution is splitted in the following services:

- Reporting Service Website (.NET Core 2.2)
- Reporting Service API (.NET Core 2.2)
- Reporting Service Queue (RabbitMQ)
- Reporting Service Processor (.NET Core 2.2)

## Technology Stack 

Azure Cloud Services:
- Container Registry
- CosmosDB (MongoDB)
- Key Vault
- Kubernetes Service
- SendGrid

Advanced Message Queuing Protocol:
- RabbitMQ

Development:
- Docker
- .NET Core

## Prerequisites

1. An active Azure subscription, there are several ways you can procure one:
    * <a href="https://azure.microsoft.com/en-us/free/" target="_blank">Azure Free Account</a>
    * <a href="https://visualstudio.microsoft.com/dev-essentials/" target="_blank">Visual Studio Dev Essentials</a>
    * <a href="https://azure.microsoft.com/en-us/pricing/member-offers/credit-for-visual-studio-subscribers/" target="_blank">Monthly Azure credit for Visual Studio subscribers</a>
2. <a href="https://code.visualstudio.com/" target="_blank">VS Code</a> o <a href="https://visualstudio.microsoft.com/vs/community/" target="_blank">Visual Studio 2019 Community</a>
3. Azure DevOps free account (<a href="https://dev.azure.com/" target="_blank">https://dev.azure.com/</a>)
4. .Net Core 2.2 Installed (<a href="https://www.microsoft.com/net/download" target="_blank">https://www.microsoft.com/net/download</a>)
5. Git for Windows, Linux or MacOS are optional (<a href="https://git-scm.com/downloads" target="_blank">https://git-scm.com/downloads</a>)
6. Docker Desktop (<a href="https://www.docker.com/get-started" target="_blank">https://www.docker.com/get-started</a>). For for older Mac and Windows systems that do not meet the requirements of <a href="https://docs.docker.com/docker-for-mac/" target="_blank">Docker Desktop for Mac</a> and <a href="https://docs.docker.com/docker-for-windows/" target="_blank">Docker Desktop for Windows</a> you could use <a href="https://docs.docker.com/toolbox/toolbox_install_windows/" target="_blank">Docker Toolbox</a>.

## Architecture

<div style="text-align:center">
    <img src="/resources/images/microservices-architecture.png" width="600" />
</div>

<div style="text-align:center">
    <img src="/resources/images/orchestration-architecture.png" width="600" />
</div>

## Leverage Azure DevOps

You could also leverage Azure DevOps to implement a CI/CD pipeline for each app. For that need to create a new Azure build pipeline per app by using the associated yaml definition located in the build-deploy folder.

<div style="text-align:center">
    <img src="/resources/images/devops-ci-cd-pipelines.png" width="600" />
</div>

## Hack Goals

1. The website must be running and needs to be exposed in a specific public ip address.

2. The api must be running and needs to be exposed in a specific public ip address.

3. The rabbitmq must be running and needs to be exposed in a specific public ip address.

4. The processor must be running it doesn't need any internet exposure with a public ip, but can be monitored using the Kubernetes dashboard.

5. Solution must be working as expected, website sends a request to the API, the API queue a message, the processor dequeue the message then creates a PDF file, save the information in CosmosDB and send the file back to the user who filled the website form.

6. Each application must be deployed in Kubernetes Service as a HELM chart.

7. Each application service must have a CI/CD automated pipeline for build, approve and deploy in Azure DevOps.

8. The Kubernetes Service must be up and running ready for monitoring through the dashboard.

## Let's Hack

Step 1:
- Sign in to Azure DevOps.

    <b>Having issues? </b> review the <a href="https://dev.azure.com" target="_blank">cheat link!</a>

Step 2:
- Create a new project.
    + Version control: Git.
    + Work item process: SCRUM.
    + Visibility: Public | Private.

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/devops/organizations/projects/create-project?view=azure-devops" target="_blank">cheat link!</a>

Step 3:
- Import the GitHub repo into the new Azure DevOps repo.

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/devops/repos/git/import-git-repository?view=azure-devops" target="_blank">cheat link!</a>
    

Step 4:
- Sign in to <a href="https://portal.azure.com" target="_blank">Azure Portal </a> and open the cloud shell bash or use the <a href="https://shell.azure.com/bash" target="_blank">Azure Cloud Shell</a>.

    <b>Having issues? </b> review the <a href="https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/cloud-shell/overview.md" target="_blank">cheat link!</a>


Step 5:
- Using the cloud shell bash clone the GitHub repo.
    ```bash
    git clone https://github.com/robece/microservices-aks.git
    ```

    <b>Having issues? </b> review the <a href="https://help.github.com/en/articles/cloning-a-repository" target="_blank">cheat link!</a>

Step 6:
- Create a new Container Registry resource, for this step you have two options: do it manually or executing the cloud shell bash script located in configuration\container-registry\config.sh.

    <br />
    <b>Important:</b> In this script you will need to provide:
    
    ```bash
    1. The name of the container registry.
    2. The name of the container registry resource group.
    3. The location of the container registry resource group.
    ```

    After the script execution remember to take note of the CALL TO ACTION annotation:

    ```bash
    1. RegistryName.
    2. RegistryUsername.
    3. RegistryPassword.
    ```

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal" target="_blank">cheat link!</a>

Step 7:
- Create a new CosmosDB resource with the API type: MongoDB, for this step you have two options: do it manually or executing the cloud shell bash script located in configuration\cosmos-db\config.sh.

    <br />
    <b>Important:</b> In this script you will need to provide:
    
    ```bash
    1. The name of the cosmos db account.
    2. The name of the cosmos db resource group.
    3. The name of the cosmos db database.
    4. The location of the cosmos db resource group.
    ```

    <b>Note:</b> If you want to create or use an existing CosmosDB account just validate that is using the MongoDB API.

    After the script execution remember to take note of the CALL TO ACTION annotation:

    ```bash
    1. ConnectionString.
    2. CosmosDBAccount.
    3. DatabaseId.
    ```

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/cosmos-db/create-cosmosdb-resources-portal" target="_blank">cheat link!</a>
    <br/>

Step 8:
- Create a new Key Vault resource, for this step you have two options: do it manually or executing the cloud shell bash script located in configuration\key-vault\config.sh.

    <br />
    <b>Important:</b> In this script you will need to provide:
    
    ```bash
    1. The name of the key vault.
    2. The name of the key vault resource group.
    3. The location of the key vault resource group.
    ```

    After the script execution remember to take note of the CALL TO ACTION annotation:

    ```bash
    1. KeyVaultCertificateName.
    2. KeyVaultClientId.
    3. KeyVaultClientSecret.
    4. KeyVaultIdentifier.
    5. KeyVaultEncryptionKey.
    ```

    <b>Having issues? </b> review the <a href="KeyVault-for-Microservices.md" target="_blank">cheat link!</a>

Step 9:
- Create a new Kubernetes Service resource, for this step you have two options: do it manually or executing the cloud shell bash script located in configuration\kubernetes-service\config.sh.

    <br />
    <b>Important:</b> In this script you will need to provide:
    
    ```bash
    1. The name of the kubernetes cluster.
    2. The name of the kubernetes cluster resource group.
    3. The number of nodes of the kubernetes cluster.
    4. The vm size of the nodes of the kubernetes cluster.
    5. The location of the kubernetes cluster resource group.
    6. The kind of cluster: with | without virtual nodes.
    ```

    <b>Note:</b> If you use the script to create the Kubernetes Service resource, you have the possibility to create a cluster supporting virtual nodes, virtual nodes feature is not required in this challenge, if you create the cluster without virtual nodes support you may need to create a new cluster to perform other challenges.

    After the script execution remember to take note of the CALL TO ACTION annotation:

    ```bash
    1. KubernetesCluster.
    2. KubernetesClusterResourceGroup.
    ```

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough" target="_blank">cheat link!</a>

Step 10:
- Execute the script configuration\public-ips\config.sh to create the three public IPs required by the apps.

    <br />
    <b>Important:</b> In this script you will need to provide:
    
    ```bash
    1. The name of the kubernetes cluster.
    2. The name of the kubernetes cluster resource group.
    ```

    After the script execution remember to take note of the CALL TO ACTION annotation:

    ```bash
    1. Public IP for Rabbit Service.
    2. Public IP for API Service.
    3. Public IP for Website.
    ```

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/aks/static-ip" target="_blank">cheat link!</a>

Step 11:
- Sign in in Azure Portal and create a new SendGrid resource.
- Manage the new SendGrid resource and Configure a new API Key.

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/sendgrid-dotnet-how-to-send-email" target="_blank">cheat link!</a>

Step 12:
- Create a Kubernetes Service namespace with the name: microservices-aks.

    ```bash
    kubectl create namespace microservices-aks
    ```

    <b>Having issues? </b> review the <a href="https://www.assistanz.com/steps-to-create-custom-namespace-in-the-kubernetes/" target="_blank">cheat link!</a>

Step 13:
- Configure the docker registry secret for Kubernetes Service.
    <br />
    ```bash
    kubectl create secret docker-registry [dockerRegistrySecretName] --docker-server [acrRegistryName].azurecr.io --docker-username [acrRegistryUsername] --docker-password [acrRegistryPassword] --docker-email [acrEmailAccount] --namespace microservices-aks
    ```

    ```bash
    [dockerRegistrySecretName] = the name of the docker-registry secret, e.g. robeceacr01-auth.
    [acrRegistryName] = the name of the Container Registry previously created.
    [acrRegistryUsername] = the username of the Container Registry previously created.
    [acrRegistryPassword] = the password of the Container Registry previously created.
    [dockerEmailAccount] = the email used in the subscription of the Container Registry previously created.
    ```

    <b>Note:</b> [dockerEmailAccount] the email parameter is optional, you can add an email adress or just remove the --docker-email parameter.

    <b>Important:</b> Take note of the [dockerRegistrySecretName] you may use it later.

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/aks/concepts-security" target="_blank">cheat link!</a>

Step 14:
- Go to secrets\reporting-service-api and configure the settings.
    <br />
    ```json
    {
        "ApplicationSettings": {
            "RabbitMQUsername": "RABBITMQ-USERNAME",
            "RabbitMQPassword": "RABBITMQ-PASSWORD",
            "RabbitMQHostname": "RABBITMQ-HOSTNAME-OR-IP-ADDRESS",
            "RabbitMQPort": "RABBITMQ-PORT",
            "DispatchQueueName": "DISPATCH-QUEUE-NAME",
            "KeyVaultCertificateName": "KEY-VAULT-CERTIFICATE-NAME",
            "KeyVaultClientId": "KEY-VAULT-CLIENT-ID",
            "KeyVaultClientSecret": "KEY-VAULT-CLIENT-SECRET",
            "KeyVaultIdentifier": "KEY-VAULT-IDENTIFIER",
            "KeyVaultEncryptionKey": "KEY-VAULT-ENCRIPTION-KEY"
        },
        "Logging": {
            "LogLevel": {
            "Default": "Debug",
            "System": "Information",
            "Microsoft": "Information"
            }
        }
    }
    ```

    Set the values:

    ```bash
    [RabbitMQUsername] = guest
    [RabbitMQPassword] = guest
    [RabbitMQHostname] = rabbitmq service public ip (previously defined)
    [RabbitMQPort] = 5672
    [DispatchQueueName] = dispatch
    [KeyVaultCertificateName] = key vault certificate name (previously defined)
    [KeyVaultClientId] = AAD application app Id (previously defined)
    [KeyVaultClientSecret] = AAD application client secret (previously defined)
    [KeyVaultIdentifier] = key vault identifier (previously defined) (e.g. https://keyvaultname.vault.azure.net)
    [KeyVaultEncryptionKey] = ENCRYPTION-KEY
    ```

- In the same folder path execute.

    ```bash
    kubectl create secret generic appsettings-secrets-reporting-service-api --from-file=appsettings.secrets.json --namespace microservices-aks
    ```

    <b>Having issues? </b> review the <a href="https://kubernetes.io/docs/concepts/configuration/secret/" target="_blank">cheat link!</a>

Step 15:
- Go to secrets\reporting-service-processor and configure the settings.
    <br />
    ```json
    {
        "ApplicationSettings": {
            "ConnectionString": "CONNECTION-STRING",
            "DatabaseId": "DATABASE-ID",
            "ReportCollection": "REPORT-COLLECTION",
            "RabbitMQUsername": "RABBITMQ-USERNAME",
            "RabbitMQPassword": "RABBITMQ-PASSWORD",
            "RabbitMQHostname": "RABBITMQ-HOSTNAME-OR-IP-ADDRESS",
            "RabbitMQPort": "RABBITMQ-PORT",
            "DispatchQueueName": "DISPATCH-QUEUE-NAME",
            "KeyVaultCertificateName": "KEY-VAULT-CERTIFICATE-NAME",
            "KeyVaultClientId": "KEY-VAULT-CLIENT-ID",
            "KeyVaultClientSecret": "KEY-VAULT-CLIENT-SECRET",
            "KeyVaultIdentifier": "KEY-VAULT-IDENTIFIER",
            "KeyVaultEncryptionKey": "KEY-VAULT-ENCRIPTION-KEY",
            "SendGridAPIKey": "SENDGRID-API-KEY"
        }
    }
    ```

    Set the values:

    ```bash
    [ConnectionString] = cosmosdb connection string (previously defined)
    [DatabaseId] = cosmosdb database id (previously defined)
    [ReportCollection] = report
    [RabbitMQUsername] = guest
    [RabbitMQPassword] = guest
    [RabbitMQHostname] = rabbitmq service public ip (previously defined)
    [RabbitMQPort] = 5672
    [DispatchQueueName] = dispatch
    [KeyVaultCertificateName] = key vault certificate name (previously defined)
    [KeyVaultClientId] = AAD application app Id (previously defined)
    [KeyVaultClientSecret] = AAD application client secret (previously defined)
    [KeyVaultIdentifier] = key vault identifier (previously defined) (e.g. https://keyvaultname.vault.azure.net)
    [KeyVaultEncryptionKey] = ENCRYPTION-KEY
    [SendGridAPIKey] = sendgrid api key (previously defined)
    ```

- In the same folder path execute.

    ```bash
    kubectl create secret generic appsettings-secrets-reporting-service-processor --from-file=appsettings.secrets.json --namespace microservices-aks
    ```

    <b>Having issues? </b> review the <a href="https://kubernetes.io/docs/concepts/configuration/secret/" target="_blank">cheat link!</a>

Step 16:
- Go to secrets\reporting-service-website and configure the settings.
    <br />
    ```json
    {
        "ApplicationSettings": {
            "APIHostname": "API-HOSTNAME-OR-IP-ADDRESS"
        },
        "Logging": {
            "LogLevel": {
            "Default": "Warning"
            }
        },
        "AllowedHosts": "*"
    }
    ```

    Set the values:

    ```bash
    [APIHostname] = api service public ip (previously defined)
    ```

- In the same path folder execute.

    ```bash
    kubectl create secret generic appsettings-secrets-reporting-service-website --from-file=appsettings.secrets.json --namespace microservices-aks
    ```

    <b>Having issues? </b> review the <a href="https://kubernetes.io/docs/concepts/configuration/secret/" target="_blank">cheat link!</a>

Step 17:
- <b>This step is optional</b>, in case you want to debug and run the source applications locally ensure you have Visual Studio or Visual Code installed and the right the appsettings for each application.

    <b>Important:</b> You will need to have Docker installed to start RabbitMQ container since there are no source code in this challenge for this service.

    <b>Having issues? </b> review the <a href="https://www.freecodecamp.org/news/docker-easy-as-build-run-done-e174cc452599/" target="_blank">cheat link!</a>

Step 18:
- In Azure DevOps and set up a new Azure Resource Manager Service Connection, pointing to the subscription where is the Kubernetes Cluster resource group.

    <b>Important:</b> Ensure you have reviewed the option "Allow all pipelines to use this connection" when create the service connection.

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml" target="_blank">cheat link!</a>

Step 19:
- In Azure DevOps, create a new environment with the name: Testing Environment.

    <b>Note:</b> If you don't see the Environment feature in Azure DevOps Pipelines, you may need to activate the new features: https://docs.microsoft.com/en-us/azure/devops/project/navigation/preview-features?view=azure-devops.
    
    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/devops/pipelines/process/environments?view=azure-devops" target="_blank">cheat link!</a>

Step 20:
- In Azure DevOps, select the environment with the name: Testing Environment and add approval permission.

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/devops/pipelines/process/approvals?view=azure-devops" target="_blank">cheat link!</a>

Step 21:
- In Azure DevOps, create a new pipeline for each application, following the next steps.
    + <b>Connect</b> to the Azure Repo Git.
    + <b>Select the repository</b> where are located the build-deploy yaml scripts.
    + <b>Configure the pipeline</b> using the "Existing Azure Pipelines yaml file" option.
    + <b>Select the existing yaml file</b>, e.g. rabbitmq-build-deploy.yml.
    + <b>Run</b> the pipeline.

    <br />

    + <b>Note:</b> The first time you run the yaml pipeline it will fail because there are some missing variables that need to be set in the pipeline.

    + Azure DevOps needs to have four pipelines added:
        - RabbitMQ Service pipeline (build/deploy).
        - Reporting API Service pipeline (build/deploy).
        - Reporting Processor Service pipeline (build/deploy).
        - Reporting Website pipeline (build/deploy).

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/devops/pipelines/ecosystems/dotnet-core?view=azure-devops" target="_blank">cheat link!</a>

Step 22:
- In Azure DevOps, edit each pipeline and add the environment variables need it. Each script has a commented variables that need to be added in to the pipeline to run succesfully, you don't need to edit the pipeline code, this change should be directly in Azure DevOps.

    Example:
    ```bash
    # define 10 more variables in the build pipeline in UI: 
    # 1. acrRegistryName
    # 2. acrRegistryUsername
    # 3. acrRegistryPassword
    # 4. imagePullSecret
    # 5. replicaCount
    # 6. volumeSecretName
    # 7. azureSubscriptionEndpoint
    # 8. kubernetesClusterResourceGroup
    # 9. kubernetesCluster
    # 10. publicIP
    ```

    In this example  you will set the values:
    ```bash
    [acrRegistryName] = the name of the container registry
    [acrRegistryUsername] = the username of the container registry
    [acrRegistryPassword] = the password of the container registry
    [imagePullSecret] = the name of the docker registry secret
    [replicaCount] = the number of replicas of the pods
    [volumeSecretName] = the name of the secret for the appsettings.secrets.json
    [azureSubscriptionEndpoint] = the name of the service connection
    [kubernetesCluster] = the name of the kubernetes service cluster
    [publicIP] = the public ip assigned previously for each service
    ```

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch" target="_blank">cheat link!</a>

Step 23:
- Repeat the last two steps for each application, review each pipeline to validate if it's correctly configured, with these pipelines applications will be deployed to Kubernetes Service.

Step 24:
- Run each pipeline and approve to deploy, ensure the releases have been deployed successfully, pods and services must be up and running in the Kubernetes Service, validate it by opening the Kubernetes dashboard.

- Get the credentials to the local machine.
    ```bash
    az aks get-credentials --name [kubernetesCluster] --resource-group [kubernetesClusterResourceGroup] 
    ```

- Open the Kubernetes dashboard.
    ```bash
    az aks browse --name [kubernetesCluster] --resource-group [kubernetesClusterResourceGroup] 
    ```

    <b>Having issues? </b> review the <a href="https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard" target="_blank">cheat link!</a>

Step 25:
- Congratulations, you are now able to navigate through the reporting service website app by using the public ip previously assigned.

## HELM Commands

After the automated deployments you may want to list or delete helm charts from the Kubernetes Service, there are some commands here, in case need more details, review the cheat link.

- If need to to list HELM charts installed in Kubernetes.
    ```bash
    helm ls
    ```

- If need to delete a HELM chart in Kubernetes.
    ```bash
    helm del --purge [helmPackage]
    ```

    <b>Having issues? </b> review the <a href="https://helm.sh/docs/helm/#helm" target="_blank">cheat link!</a>

## Closure

You have now the knowledge to create a new Cloud Native DevOps CI/CD pipelines end-to-end with Microservices on Azure Kubernetes Services, I strongly suggest dig into the code to understand how microservices, configuration scripts and charts have been made as part of the practices provided in this content.