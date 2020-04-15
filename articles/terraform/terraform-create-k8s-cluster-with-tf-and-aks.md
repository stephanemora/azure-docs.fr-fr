---
title: 'Tutoriel : Créer un cluster Kubernetes avec Azure Kubernetes Service (AKS) et Terraform'
description: Dans ce tutoriel, vous allez créer un cluster Kubernetes avec Azure Kubernetes Service et Terraform
keywords: azure devops terraform aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: b7a84d7562e99e53ff7be75b7d40795cd3f9e203
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618936"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Tutoriel : Créer un cluster Kubernetes avec Azure Kubernetes Service et Terraform

[Azure Kubernetes Service (AKS)](/azure/aks/) gère votre environnement Kubernetes hébergé. AKS vous permet de déployer et de gérer des applications conteneurisées sans expertise d’orchestration de conteneurs. AKS vous permet également d’effectuer de nombreuses opérations de maintenance courantes sans mettre votre application hors connexion. Ces opérations incluent le provisionnement, la mise à niveau et la mise à l’échelle des ressources à la demande.

Dans ce tutoriel, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser HCL (HashiCorp Language) pour définir un cluster Kubernetes
> * Utiliser Terraform et AKS pour créer un cluster Kubernetes
> * Utiliser l’outil kubectl pour tester la disponibilité d’un cluster Kubernetes

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Configurez Terraform** : Suivez les instructions de l’article [Terraform et configuration de l’accès à Azure ](terraform-install-configure.md)

- **Principal de service Azure** : Suivez les instructions de la section **Créer le principal de service** de l’article intitulé [Créer un principal de service Azure avec Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Notez les valeurs de l’appId, du nom d’affichage, du mot de passe et du locataire.

## <a name="create-the-directory-structure"></a>Créer la structure de répertoire

La première étape consiste à créer le répertoire qui contient vos fichiers de configuration Terraform pour l’exercice.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview). Si vous n’avez pas sélectionné d’environnement précédemment, choisissez **Bash** comme votre environnement.

    ![Invite Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Déplacez-vous dans le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Créez un répertoire nommé `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Déplacez-vous dans le nouveau répertoire :

    ```bash
    cd terraform-aks-k8s
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

## <a name="define-a-kubernetes-cluster"></a>Définir un cluster Kubernetes

Créez le fichier de configuration Terraform qui déclare les ressources du cluster Kubernetes.

1. Dans Cloud Shell, créez un fichier nommé `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Collez le code suivant dans l’éditeur :

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Le code précédent définit le nom du cluster, l’emplacement et le nom du groupe de ressources. Le préfixe du nom de domaine complet (FQDN) est également défini. Le nom de domaine complet est utilisé pour accéder au cluster.

    L’enregistrement `linux_profile` vous permet de configurer les paramètres servant à vous connecter aux nœuds Worker en utilisant SSH.

    Avec AKS, vous payez uniquement pour les nœuds worker. L’enregistrement `default_node_pool` configure les détails de ces nœuds Worker. L’enregistrement `default_node_pool record` inclut le nombre de nœuds Worker à créer et le type des nœuds Worker. Si vous devez par la suite effectuer un scale-up ou un scale-down du cluster, modifiez la valeur de `count` dans cet enregistrement.

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="declare-the-variables"></a>Déclarer les variables

1. Dans Cloud Shell, créez un fichier nommé `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Collez le code suivant dans l’éditeur :

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="create-a-terraform-output-file"></a>Créer un fichier de sortie Terraform

Les [sorties Terraform](https://www.terraform.io/docs/configuration/outputs.html) vous permettent de définir des valeurs qui sont présentées en surbrillance à l’utilisateur quand Terraform applique un plan, et qui peuvent être interrogées à l’aide de la commande `terraform output`. Dans cette section, créez un fichier de sortie qui permet d’accéder au cluster avec [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

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
    ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Configurer le stockage Azure pour stocker l’état de Terraform

Terraform suit l’état en local via le fichier `terraform.tfstate`. Ce modèle fonctionne bien dans un environnement comptant une seule personne. Dans un environnement à plusieurs personnes, le [stockage Azure](/azure/storage/) est utilisé pour suivre l’état.

Dans cette section, vous allez apprendre à effectuer les tâches suivantes :
- Récupérer les informations de compte de stockage (nom de compte et clé de compte)
- Créer un conteneur de stockage dans lequel les informations d’état de Terraform seront stockées.

1. Dans le portail Azure, sélectionnez **Tous les services** dans le menu à gauche.

1. Sélectionnez **Comptes de stockage**.

1. Sous l’onglet **Comptes de stockage**, sélectionnez le nom du compte de stockage dans lequel Terraform doit stocker l’état. Par exemple, vous pouvez utiliser le compte de stockage créé lorsque vous ouvrez Cloud Shell pour la première fois.  Le nom du compte de stockage créé par Cloud Shell commence généralement par `cs` suivi d’une chaîne aléatoire de chiffres et lettres. Notez le compte de stockage que vous sélectionnez. Vous en aurez besoin par la suite.

1. Sous l’onglet du compte de stockage, sélectionnez **Clés d’accès**.

    ![Menu du compte de stockage](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Notez la valeur **key1** **key**. (Quand vous sélectionnez l’icône à droite de la clé, la valeur est copiée dans le Presse-papiers.)

    ![Clés d’accès au compte de stockage](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Dans Cloud Shell, créez un conteneur dans votre compte de stockage Azure. Remplacez les espaces réservés par les valeurs appropriées pour votre environnement.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Créer le cluster Kubernetes

Dans cette section, vous voyez comment utiliser la commande `terraform init` pour créer les ressources définies dans les fichiers de configuration que vous avez créés dans les sections précédentes.

1. Dans Cloud Shell, initialisez Terraform. Remplacez les espaces réservés par les valeurs appropriées pour votre environnement.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    La commande `terraform init` affiche la réussite de l’initialisation du back-end et du plug-in de fournisseur :

    ![Exemple de résultats « terraform init »](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exportez les informations d’identification du principal de service. Remplacez les espaces réservés par les valeurs appropriées de votre principal de service.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Exécutez la commande `terraform plan` pour créer le plan Terraform qui définit les éléments d’infrastructure. 

    ```bash
    terraform plan -out out.plan
    ```

    La commande `terraform plan` affiche les ressources qui seront créées lorsque vous exécuterez la commande `terraform apply` :

    ![Exemple de résultats « terraform plan »](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Exécutez la commande `terraform apply` pour appliquer le plan servant à créer le cluster Kubernetes. Le processus de création d’un cluster Kubernetes peut prendre plusieurs minutes, ce qui peut aboutir à l’expiration de la session Cloud Shell. Si la session Cloud Shell expire, vous pouvez suivre les étapes de la section « Récupérer suite à l’expiration de Cloud Shell » pour poursuivre le tutoriel.

    ```bash
    terraform apply out.plan
    ```

    La commande `terraform apply` affiche les résultats de la création des ressources définies dans vos fichiers de configuration :

    ![Exemple de résultats « terraform apply »](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Dans le portail Azure, sélectionnez **Toutes les ressources** dans le menu de gauche pour voir les ressources créées pour votre nouveau cluster Kubernetes.

    ![Toutes les ressources dans le portail Azure](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Récupérer suite à l’expiration de Cloud Shell

Si la session Cloud Shell expire, vous pouvez suivre les étapes ci-dessous pour récupérer :

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

    ![L’outil kubectl vous permet de vérifier l’intégrité de votre cluster Kubernetes](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Analyser le fonctionnement et les journaux d’activité

Lorsque de la création du cluster AKS, la supervision a été activée pour capturer les métriques d’intégrité pour les nœuds de cluster et les pods. Ces mesures sont disponibles dans le portail Azure. Pour plus d’informations sur la supervision de l’intégrité des conteneurs, consultez [Superviser l’intégrité d’Azure Kubernetes Service](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [En savoir plus sur l’utilisation de Terraform dans Azure](/azure/terraform)