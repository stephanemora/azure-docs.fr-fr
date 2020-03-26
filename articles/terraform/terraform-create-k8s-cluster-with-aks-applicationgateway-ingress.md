---
title: 'Tutoriel : Créer un contrôleur d’entrée Application Gateway dans Azure Kubernetes Service'
description: Dans ce tutoriel, vous allez créer un cluster Kubernetes avec Azure Kubernetes Service en utilisant Application Gateway comme contrôleur d’entrée.
keywords: azure devops terraform application gateway entrée aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 6b48d0acb654f0b0643c0754e53f6bc6ea76bb45
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945317"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Tutoriel : Créer un contrôleur d’entrée Application Gateway dans Azure Kubernetes Service

[Azure Kubernetes Service (AKS)](/azure/aks/) gère votre environnement Kubernetes hébergé. AKS permet de déployer et de gérer rapidement et facilement des applications conteneurisées sans expertise d’orchestration de conteneurs. De même, AKS vous évite la contrainte de mettre les applications hors connexion pour les tâches opérationnelles et de maintenance. Avec AKS, ces tâches – provisionnement, mise à niveau, mise à l’échelle des ressources, etc. – peuvent être effectuées à la demande.

Un contrôleur d’entrée propose diverses fonctionnalités pour les services Kubernetes, notamment le proxy inversé, le routage configurable du trafic et l’arrêt TLS. Les ressources d’entrée Kubernetes servent à configurer les règles d’entrée et les itinéraires pour chacun des services Kubernetes. En utilisant un contrôleur d’entrée et des règles d’entrée, une même adresse IP peut router le trafic vers plusieurs services au sein d’un cluster Kubernetes. Toutes ces fonctionnalités sont fournies par Azure [Application Gateway](/azure/Application-Gateway/), ce qui en fait un contrôleur d’entrée idéal pour Kubernetes sur Azure. 

Dans ce tutoriel, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un cluster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) en utilisant AKS avec Application Gateway comme contrôleur d’entrée.
> * Utiliser HCL (HashiCorp Language) pour définir un cluster Kubernetes.
> * Utiliser Terraform pour créer une ressource Application Gateway.
> * Utiliser Terraform et AKS pour créer un cluster Kubernetes.
> * Utiliser l’outil kubectl pour tester la disponibilité d’un cluster Kubernetes.

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Configurez Terraform** : Suivez les instructions de l’article [Terraform et configuration de l’accès à Azure ](terraform-install-configure.md)

- **Groupe de ressources Azure** : Si vous n’avez pas de groupe de ressources Azure à utiliser pour la démonstration, [créez un groupe de ressources Azure](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Notez le nom et l’emplacement du groupe de ressources, car ces valeurs sont utilisées dans la démonstration.

- **Principal de service Azure** : suivez les instructions de la section **Créer le principal de service** de l’article [Créer un principal de service Azure avec Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Notez les valeurs des éléments appId, displayName et password.

- **Obtenir l’ID d’objet du principal du service** : Exécutez la commande suivante dans Cloud Shell : `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Créer la structure de répertoire

La première étape consiste à créer le répertoire qui contient vos fichiers de configuration Terraform pour l’exercice.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Déplacez-vous dans le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Créez un répertoire nommé `terraform-aks-appgw-ingress`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Déplacez-vous dans le nouveau répertoire :

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Déclarer le fournisseur Azure

Créez le fichier de configuration Terraform qui déclare le fournisseur Azure.

1. Dans Cloud Shell, créez un fichier nommé `main.tf`.

    ```bash
    code main.tf
    ```

1. Collez le code suivant dans l’éditeur :

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x. 
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="define-input-variables"></a>Définir des variables d’entrée

Créez le fichier de configuration Terraform qui liste toutes les variables nécessaires pour ce déploiement.

1. Dans Cloud Shell, créez un fichier nommé `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Collez le code suivant dans l’éditeur :
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = "map"

      default = {
        source = "terraform"
      }
    }
    ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="define-the-resources"></a>Définir les ressources 
Créez un fichier de configuration Terraform qui crée toutes les ressources. 

1. Dans Cloud Shell, créez un fichier nommé `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Collez le bloc de code suivant pour créer un bloc de variables locales pour des variables calculées à réutiliser :

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. Collez le bloc de code suivant pour créer une source de données pour le groupe de ressources, nouvelle identité utilisateur :

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Collez le bloc de code suivant pour créer des ressources réseau de base :

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Collez le bloc de code suivant pour créer des ressources Application Gateway :

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Collez le bloc de code suivant pour créer des attributions de rôle :

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Collez le bloc de code suivant pour créer le cluster Kubernetes :

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

Le code présenté dans cette section définit le nom du cluster, l’emplacement et le resource_group_name. La valeur `dns_prefix`, qui fait partie du nom de domaine complet (FQDN) utilisé pour accéder au cluster, est définie.

L’enregistrement `linux_profile` vous permet de configurer les paramètres servant à vous connecter aux nœuds Worker en utilisant SSH.

Avec AKS, vous payez uniquement pour les nœuds worker. L’enregistrement `agent_pool_profile` configure les détails de ces nœuds Worker. L’enregistrement `agent_pool_profile record` inclut le nombre de nœuds Worker à créer et le type des nœuds Worker. Si vous devez par la suite effectuer un scale-up ou un scale-down du cluster, modifiez la valeur de `count` dans cet enregistrement.

## <a name="create-a-terraform-output-file"></a>Créer un fichier de sortie Terraform

Les [sorties Terraform](https://www.terraform.io/docs/configuration/outputs.html) vous permettent de définir les valeurs présentées en surbrillance à l’utilisateur quand Terraform applique un plan, et elles peuvent être interrogées à l’aide de la commande `terraform output`. Dans cette section, créez un fichier de sortie qui permet d’accéder au cluster avec [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. Dans Cloud Shell, créez un fichier nommé `output.tf`.

    ```bash
    code output.tf
    ```

1. Collez le code suivant dans l’éditeur :

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Configurer le stockage Azure pour stocker l’état de Terraform

Terraform suit l’état en local via le fichier `terraform.tfstate`. Ce modèle fonctionne bien dans un environnement comptant une seule personne. Cependant, dans un environnement à plusieurs personnes plus pratique, vous devez suivre l’état sur le serveur en utilisant le [stockage Azure](/azure/storage/). Dans cette section, vous allez apprendre à récupérer les informations de compte de stockage nécessaires et à créer un conteneur de stockage. Les informations d’état de Terraform sont ensuite stockées dans ce conteneur.

1. Dans le portail Azure, sous **Services Azure**, sélectionnez **Comptes de stockage**. (Si l’option **Comptes de stockage** n’est pas visible dans la page principale, sélectionnez **Plus de services**, puis recherchez et sélectionnez-la.)

1. Dans la page **Comptes de stockage**, sélectionnez le nom du compte de stockage dans lequel Terraform doit stocker l’état. Par exemple, vous pouvez utiliser le compte de stockage créé lorsque vous ouvrez Cloud Shell pour la première fois.  Le nom du compte de stockage créé par Cloud Shell commence généralement par `cs` suivi d’une chaîne aléatoire de chiffres et lettres. 

    Faites attention au compte de stockage que vous sélectionnez, car vous en aurez besoin par la suite.

1. Sur la page du compte de stockage, sélectionnez **Clés d’accès**.

    ![Menu du compte de stockage](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Notez la valeur **key1** **key**. (Quand vous sélectionnez l’icône à droite de la clé, la valeur est copiée dans le Presse-papiers.)

    ![Clés d’accès au compte de stockage](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Dans Cloud Shell, créez un conteneur dans votre compte de stockage Azure. Remplacez les espaces réservés par les valeurs appropriées pour votre compte de stockage Azure.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Créer le cluster Kubernetes
Dans cette section, vous voyez comment utiliser la commande `terraform init` pour créer les ressources définies dans les fichiers de configuration que vous avez créés dans les sections précédentes.

1. Dans Cloud Shell, initialisez Terraform. Remplacez les espaces réservés par les valeurs appropriées pour votre compte de stockage Azure.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    La commande `terraform init` affiche la réussite de l’initialisation du back-end et du plug-in de fournisseur :

    ![Exemple de résultats « terraform init »](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Dans Cloud Shell, créez un fichier sous le nom `terraform.tfvars` :

    ```bash
    code terraform.tfvars
    ```

1. Collez les variables suivantes créés précédemment dans l’éditeur. Pour obtenir la valeur d’emplacement de votre environnement, utilisez `az account list-locations`.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

1. Exécutez la commande `terraform plan` pour créer le plan Terraform qui définit les éléments d’infrastructure. 

    ```bash
    terraform plan -out out.plan
    ```

    La commande `terraform plan` affiche les ressources qui sont créées quand vous exécutez la commande `terraform apply` :

    ![Exemple de résultats « terraform plan »](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Exécutez la commande `terraform apply` pour appliquer le plan servant à créer le cluster Kubernetes. Le processus de création d’un cluster Kubernetes peut prendre plusieurs minutes, ce qui peut aboutir à l’expiration de la session Cloud Shell. Si la session Cloud Shell expire, vous pouvez suivre les étapes de la section « Récupérer suite à l’expiration de Cloud Shell » pour poursuivre le tutoriel.

    ```bash
    terraform apply out.plan
    ```

    La commande `terraform apply` affiche les résultats de la création des ressources définies dans vos fichiers de configuration :

    ![Exemple de résultats « terraform apply »](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Dans le portail Azure, sélectionnez **Groupes de ressources** dans le menu de gauche pour voir les ressources créées pour votre nouveau cluster Kubernetes dans le groupe de ressources sélectionné.

    ![Invite Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Récupérer suite à l’expiration de Cloud Shell

Si la session Cloud Shell expire, vous pouvez utiliser les étapes suivantes pour récupérer :

1. Démarrez une session Cloud Shell.

1. Accédez au répertoire qui contient vos fichiers de configuration Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Exécutez la commande suivante :

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>Tester le cluster Kubernetes
Les outils Kubernetes peuvent être utilisés pour vérifier le cluster nouvellement créé.

1. Obtenez la configuration Kubernetes à partir de l’état Terraform et stockez-la dans un fichier que kubectl peut lire.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Définissez une variable d’environnement pour que kubectl choisisse la bonne configuration.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Vérifiez l’intégrité du cluster.

    ```bash
    kubectl get nodes
    ```

    Vous devriez voir les détails de vos nœuds worker, et tous doivent avoir un état **Ready**, comme illustré dans l’image suivante :

    ![L’outil kubectl vous permet de vérifier l’intégrité de votre cluster Kubernetes](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Installer Azure AD Pod Identity

Azure Active Directory Pod Identity offre un accès à base de jeton à [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

[Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) ajoute les composants suivants à votre cluster Kubernetes :

  - [Définitions CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) Kubernetes : `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
  - Composant [MIC (Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - Composant [NMI (Node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Si RBAC est **activé**, exécutez la commande suivante pour installer Azure AD Pod Identity sur votre cluster :

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Si RBAC est **désactivé**, exécutez la commande suivante pour installer Azure AD Pod Identity sur votre cluster :

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Installer Helm

Le code de cette section utilise [Helm](/azure/aks/kubernetes-helm) – gestionnaire de packages Kubernetes – pour installer le package `application-gateway-kubernetes-ingress` :

1. Si RBAC est **activé**, exécutez les commandes suivantes pour installer et configurer Helm :

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Si RBAC est **désactivé**, exécutez la commande suivante pour installer et configurer Helm :

    ```bash
    helm init
    ```

1. Ajoutez le dépôt Helm AGIC :

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Installer le chart Helm du contrôleur d’entrée

1. Téléchargez `helm-config.yaml` pour configurer AGIC :

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Modifiez `helm-config.yaml` et entrez les valeurs appropriées pour les sections `appgw` et `armAuth`.

    ```bash
    code helm-config.yaml
    ```

    Les valeurs sont décrites comme suit :

    - `verbosityLevel`: définit le niveau de détail de l’infrastructure de journalisation AGIC. Pour connaître les valeurs possibles, consultez [Niveaux de journalisation](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels).
    - `appgw.subscriptionId`: ID d’abonnement Azure pour la passerelle App Gateway. Exemple : `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: nom du groupe de ressources Azure dans lequel la passerelle App Gateway a été créée. 
    - `appgw.name`: nom de la passerelle Application Gateway. Exemple : `applicationgateway1`.
    - `appgw.shared`: cet indicateur booléen doit être défini par défaut sur `false`. Affectez-lui la valeur `true` si vous avez besoin d’une [passerelle App Gateway partagée](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: spécifiez l’espace de noms que doit observer AGIC. L’espace de noms peut être une valeur de chaîne unique ou une liste d’espaces de noms séparés par des virgules. Si vous laissez cette variable avec des commentaires ou si vous la définissez sur une chaîne vide, le contrôleur d’entrée observe tous les espaces de noms accessibles.
    - `armAuth.type`: valeur `aadPodIdentity` ou `servicePrincipal`.
    - `armAuth.identityResourceID`: ID de ressource de l’identité managée.
    - `armAuth.identityClientId`: ID client de l’identité.
    - `armAuth.secretJSON`: nécessaire uniquement quand le type de secret du principal du service est choisi (quand `armAuth.type` a été défini sur `servicePrincipal`).

    Remarques importantes :
    - La valeur de `identityResourceID` est créée dans le script terraform et peut être trouvée en exécutant : `echo "$(terraform output identity_resource_id)"`.
    - La valeur de `identityClientID` est créée dans le script terraform et peut être trouvée en exécutant : `echo "$(terraform output identity_client_id)"`.
    - La valeur de `<resource-group>` est le groupe de ressources de votre passerelle App Gateway.
    - La valeur de `<identity-name>` est le nom de l’identité créée.
    - Toutes les identités d’un abonnement donné peuvent être listées à l’aide de : `az identity list`.

1. Installez le package de contrôleur d’entrée Application Gateway :

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Installer un exemple d’application

Une fois que vous avez installé la passerelle App Gateway, AKS et AGIC, vous pouvez installer un exemple d’application via [Azure Cloud Shell](https://shell.azure.com/) :

1. Utilisez la commande curl pour télécharger le fichier YAML :

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Appliquez le fichier YAML :

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article.  

Remplacez l’espace réservé par la valeur appropriée. Toutes les ressources au sein du groupe de ressources spécifié sont supprimées.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Contrôleur d’entrée Application Gateway](https://azure.github.io/application-gateway-kubernetes-ingress/)