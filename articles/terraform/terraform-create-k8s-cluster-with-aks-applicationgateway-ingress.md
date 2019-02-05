---
title: Créer un cluster Kubernetes avec Application Gateway comme contrôleur d’entrée avec Azure Kubernetes Service (AKS)
description: Tutoriel montrant comment créer un cluster Kubernetes avec Azure Kubernetes Service et utilisant Application Gateway comme contrôleur d’entrée
services: terraform
ms.service: terraform
keywords: terraform, devops, machine virtuelle, azure, kubernetes, entrée, passerelle d’application
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 1/10/2019
ms.openlocfilehash: 2235c281b5b25390838a8f201481cfbdc9e5c223
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478908"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>Créer un cluster Kubernetes avec Application Gateway comme contrôleur d’entrée, et en utilisant Azure Kubernetes Service (AKS) et Terraform
[Azure Kubernetes Service (AKS)](/azure/aks/) gère votre environnement Kubernetes hébergé. AKS permet de déployer et de gérer rapidement et facilement des applications conteneurisées sans expertise d’orchestration de conteneurs. Il élimine également la charge des opérations en cours et la maintenance par configuration, la mise à niveau et la mise à l’échelle des ressources à la demande, sans déconnecter vos applications.

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes. Toutes les fonctionnalités ci-dessus sont fournies par Azure [Application Gateway](/azure/Application-Gateway/), ce qui en fait le contrôleur d’entrée idéal pour Kubernetes sur Azure. 

Dans ce tutoriel, découvrez comment effectuer les tâches suivantes pour créer un cluster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) en utilisant AKS avec Application Gateway comme contrôleur d’entrée :

> [!div class="checklist"]
> * Utiliser HCL (HashiCorp Language) pour définir un cluster Kubernetes
> * Utiliser Terraform pour créer une ressource Application Gateway
> * Utiliser Terraform et AKS pour créer un cluster Kubernetes
> * Utiliser l’outil kubectl pour tester la disponibilité d’un cluster Kubernetes

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Configurez Terraform** : Suivez les instructions de l’article [Terraform et configuration de l’accès à Azure ](/azure/virtual-machines/linux/terraform-install-configure)

- **Principal de service Azure** : suivez les instructions de la section **Créer le principal de service** de l’article [Créer un principal de service Azure avec Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Notez les valeurs des éléments appId, displayName et password.
    - Notez l’ID d’objet du principal du service en exécutant la commande suivante

    ```bash
     az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>Créer la structure de répertoire
La première étape consiste à créer le répertoire qui contient vos fichiers de configuration Terraform pour l’exercice.

1. Accédez au [portail Azure](http://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview). Si vous n’avez pas sélectionné d’environnement précédemment, choisissez **Bash** comme votre environnement.

    ![Invite Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. Remplacez le répertoire par le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Créez un répertoire nommé `terraform-aks-k8s`.

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
    vi main.tf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez le code suivant dans l’éditeur :

    ```JSON
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Quittez le mode insertion en sélectionnant la touche **Échap**.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```
## <a name="define-input-variables"></a>Définir des variables d’entrée
Créer le fichier de configuration Terraform qui liste toutes les variables nécessaires pour ce déploiement
1. Dans Cloud Shell, créez un fichier nommé `variables.tf`.
    ```bash
    vi variables.tf
    ```
1. Passez en mode insertion en sélectionnant la touche I.

2. Collez le code suivant dans l’éditeur :
    
    ```JSON
    variable "resource_group_name" {
      description = "Name of the resource group already created."
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
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
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

## <a name="define-the-resources"></a>Définir les ressources 
Créez un fichier de configuration Terraform qui crée toutes les ressources. 

1. Dans Cloud Shell, créez un fichier nommé `resources.tf`.

    ```bash
    vi resources.tf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez les blocs de code suivants dans l’éditeur :

    a. Créer un bloc de variables locales pour les variables calculées à réutiliser

    ```JSON
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
    b. Créer une source de données pour le groupe de ressources, nouvelle identité utilisateur
    ```JSON
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```
    c. Créer des ressources réseau de base
   ```JSON
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```
    d. Créer une ressource Application Gateway
    ```JSON
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```
    e. Créer des attributions de rôles
    ```JSON
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```
    f. Créer un cluster Kubernetes
    ```JSON
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }
    
    ```

    Le code précédent définit le nom du cluster, l’emplacement et le resource_group_name. De plus, la valeur dns_prefix, qui fait partie du nom de domaine complet (FQDN) utilisé pour accéder au cluster, est définie.

    L’enregistrement **linux_profile** vous permet de configurer les paramètres servant à vous connecter aux nœuds worker à l’aide de SSH.

    Avec AKS, vous payez uniquement pour les nœuds worker. L’enregistrement **agent_pool_profile** configure les détails de ces nœuds worker. L’enregistrement **agent_pool_profile** inclut le nombre de nœuds worker à créer et le type de nœuds worker. Si vous avez besoin d’augmenter ou de réduire l’échelle du cluster par la suite, modifiez la valeur **count** dans cet enregistrement.

1. Quittez le mode insertion en sélectionnant la touche **Échap**.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```


## <a name="create-a-terraform-output-file"></a>Créer un fichier de sortie Terraform
Les [sorties Terraform](https://www.terraform.io/docs/configuration/outputs.html) vous permettent de définir des valeurs qui sont présentées en surbrillance à l’utilisateur quand Terraform applique un plan, et qui peuvent être interrogées à l’aide de la commande `terraform output`. Dans cette section, créez un fichier de sortie qui permet d’accéder au cluster avec [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. Dans Cloud Shell, créez un fichier nommé `output.tf`.

    ```bash
    vi output.tf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez le code suivant dans l’éditeur :

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. Quittez le mode insertion en sélectionnant la touche **Échap**.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Configurer le stockage Azure pour stocker l’état de Terraform
Terraform suit l’état en local via le fichier `terraform.tfstate`. Ce modèle fonctionne bien dans un environnement comptant une seule personne. Cependant, dans un environnement à plusieurs personnes plus pratique, vous devez suivre l’état sur le serveur en utilisant le [stockage Azure](/azure/storage/). Dans cette section, récupérez les informations de compte de stockage nécessaires (nom de compte et clé de compte) et créez un conteneur de stockage dans lequel les informations d’état de Terraform seront stockées.

1. Dans le portail Azure, sélectionnez **Tous les services** dans le menu à gauche.

1. Sélectionnez **Comptes de stockage**.

1. Sous l’onglet **Comptes de stockage**, sélectionnez le nom du compte de stockage dans lequel Terraform doit stocker l’état. Par exemple, vous pouvez utiliser le compte de stockage créé lorsque vous ouvrez Cloud Shell pour la première fois.  Le nom du compte de stockage créé par Cloud Shell commence généralement par `cs` suivi d’une chaîne aléatoire de chiffres et lettres. **Prenez note du nom du compte de stockage que vous sélectionnez, car nous en avons besoin plus tard.**

1. Sous l’onglet du compte de stockage, sélectionnez **Clés d’accès**.

    ![Menu du compte de stockage](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Notez la valeur **key1** **key**. (Quand vous sélectionnez l’icône à droite de la clé, la valeur est copiée dans le Presse-papiers.)

    ![Clés d’accès au compte de stockage](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Dans Cloud Shell, créez un conteneur dans votre compte de stockage Azure (remplacez les espaces réservés &lt;YourAzureStorageAccountName > et &lt;YourAzureStorageAccountAccessKey > par des valeurs appropriées à votre compte de stockage Azure).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Créer un cluster Kubernetes
Dans cette section, vous voyez comment utiliser la commande `terraform init` pour créer les ressources définies dans les fichiers de configuration que vous avez créés dans les sections précédentes.

1. Dans Cloud Shell, initialisez Terraform (remplacez les espaces réservés &lt;YourAzureStorageAccountName> et &lt;YourAzureStorageAccountAccessKey> par des valeurs appropriées à votre compte de stockage Azure).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    La commande `terraform init` affiche la réussite de l’initialisation du backend et du plug-in du fournisseur :

    ![Exemple de résultats « terraform init »](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Créez un fichier de variables pour fournir des valeurs d’entrée. Dans Cloud Shell, créez un fichier nommé `main.tf`.

    ```bash
    vi terraform.tfvars
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez les variables suivantes créés précédemment dans l’éditeur :

    ```JSON
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. Quittez le mode insertion en sélectionnant la touche **Échap**.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Exécutez la commande `terraform plan` pour créer le plan Terraform qui définit les éléments d’infrastructure. 

    ```bash
    terraform plan -out out.plan
    ```

    La commande `terraform plan` affiche les ressources qui seront créées lorsque vous exécuterez la commande `terraform apply` :

    ![Exemple de résultats « terraform plan »](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Exécutez la commande `terraform apply` pour appliquer le plan servant à créer le cluster Kubernetes. Le processus de création d’un cluster Kubernetes peut prendre plusieurs minutes, ce qui peut aboutir à l’expiration de la session Cloud Shell. Si la session Cloud Shell expire, vous pouvez suivre les étapes de la section [« Récupérer suite à l’expiration de Cloud Shell »](#recover-from-a-dloud-shell-timeout) pour vous permettre de poursuivre le tutoriel.

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


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser Terraform et AKS pour créer un cluster Kubernetes. Pour plus d’informations sur Terraform sur Azure, consultez les ressources supplémentaires suivantes.
 
 > [!div class="nextstepaction"] 
 > [Hub Terraform sur Microsoft.com](https://docs.microsoft.com/azure/terraform/)
 