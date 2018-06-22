---
title: Créer un cluster Kubernetes avec Azure Kubernetes Service (AKS) et Terraform
description: Tutoriel illustrant comment créer un cluster Kubernetes avec Azure Kubernetes Service et Terraform
keywords: terraform, devops, machine virtuelle, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/11/2018
ms.topic: article
ms.openlocfilehash: bd00a0cc8446802a03570edd58949a46c0769101
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302861"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Créer un cluster Kubernetes avec Azure Kubernetes Service et Terraform
[Azure Kubernetes Service (AKS)](/azure/aks/) gère votre environnement Kubernetes hébergé, ce qui vous permet de déployer et de gérer de manière simple et rapide des applications en conteneur sans avoir à maîtriser l’orchestration de conteneurs. Il élimine également la charge des opérations en cours et la maintenance par configuration, la mise à niveau et la mise à l’échelle des ressources à la demande, sans déconnecter vos applications.

Dans ce tutoriel, découvrez comment effectuer les tâches suivantes pour créer un cluster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) à l’aide de [Terraform](http://terraform.io) et AKS :

> [!div class="checklist"]
> * Utiliser HCL (HashiCorp Language) pour définir un cluster Kubernetes
> * Utiliser Terraform et AKS pour créer un cluster Kubernetes
> * Utiliser l’outil kubectl pour tester la disponibilité d’un cluster Kubernetes

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Configuration de Terraform** : suivez les instructions de l’article [Terraform et configurer l’accès à Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Principal du service Azure** : suivez les instructions de la section **Créer le principal du service** de l’article [Créer un principal du service Azure avec Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Notez les valeurs de l’appId, du nom d’affichage, du mot de passe et du locataire.

## <a name="create-the-directory-structure"></a>Créer la structure de répertoire
La première étape consiste à créer le répertoire qui contient vos fichiers de configuration Terraform pour l’exercice.

1. Accédez au [portail Azure](http://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview). Si vous n’avez pas sélectionné d’environnement précédemment, choisissez **Bash** comme votre environnement.

    ![Invite Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Remplacez le répertoire par le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Créez un répertoire nommé `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Remplacez les répertoires par le nouveau répertoire :

    ```bash
    cd terraform-aks-k8s
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
        version = "=1.5.0"
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

## <a name="define-a-kubernetes-cluster"></a>Définir un cluster Kubernetes
Créez le fichier de configuration Terraform qui déclare les ressources du cluster Kubernetes.

1. Dans Cloud Shell, créez un fichier nommé `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez le code suivant dans l’éditeur :

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
            key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_D2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
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

## <a name="declare-the-variables"></a>Déclarer les variables

1. Dans Cloud Shell, créez un fichier nommé `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez le code suivant dans l’éditeur :

    ```JSON
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
    ```

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
Terraform suit l’état en local via le fichier `terraform.tfstate`. Ce modèle fonctionne bien dans un environnement comptant une seule personne. Toutefois, dans un environnement à plusieurs personnes plus pratique, vous devez suivre l’état sur le serveur en utilisant le [stockage Azure](/azure/storage/). Dans cette section, récupérez les informations de compte de stockage nécessaires (nom de compte et clé de compte) et créez un conteneur de stockage dans lequel les informations d’état de Terraform seront stockées.

1. Dans le portail Azure, sélectionnez **Tous les services** dans le menu à gauche.

1. Sélectionnez **Comptes de stockage**.

1. Sous l’onglet **Comptes de stockage**, sélectionnez le nom du compte de stockage dans lequel Terraform doit stocker l’état. Par exemple, vous pouvez utiliser le compte de stockage créé lorsque vous ouvrez Cloud Shell pour la première fois.  Le nom du compte de stockage créé par Cloud Shell commence généralement par `cs` suivi d’une chaîne aléatoire de chiffres et lettres. **N’oubliez pas le nom du compte de stockage que vous sélectionnez, car vous en aurez besoin plus tard.**

1. Sous l’onglet du compte de stockage, sélectionnez **Clés d’accès**.

    ![Menu du compte de stockage](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Notez la valeur **key1** **key**. (Quand vous sélectionnez l’icône à droite de la clé, la valeur est copiée dans le Presse-papiers.)

    ![Clés d’accès au compte de stockage](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

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

    ![Exemple de résultats « terraform init »](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exécutez la commande `terraform plan` pour créer le plan Terraform qui définit les éléments d’infrastructure. La commande demande deux valeurs : **var.client_id** et **var.client_secret**. Pour la variable **var.client_id**, entrez la valeur **appId** associée à votre principal du service. Pour la variable **var.client_secret**, entrez la valeur **password** associée à votre principal du service.

    ```bash
    terraform plan -out out.plan
    ```

    La commande `terraform plan` affiche les ressources qui seront créées lorsque vous exécuterez la commande `terraform apply` :

    ![Exemple de résultats « terraform plan »](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Exécutez la commande `terraform apply` pour appliquer le plan servant à créer le cluster Kubernetes. Le processus de création d’un cluster Kubernetes peut prendre plusieurs minutes, ce qui peut aboutir à l’expiration de la session Cloud Shell. Si la session Cloud Shell expire, vous pouvez suivre les étapes de la section [« Récupérer suite à l’expiration de Cloud Shell »](#recover-from-a-dloud-shell-timeout) pour vous permettre de poursuivre le tutoriel.

    ```bash
    terraform apply out.plan
    ```

    La commande `terraform apply` affiche les résultats de la création des ressources définies dans vos fichiers de configuration :

    ![Exemple de résultats « terraform apply »](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Dans le portail Azure, sélectionnez **Tous les services** dans le menu de gauche pour voir les ressources créées pour votre nouveau cluster Kubernetes.

    ![Invite Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Récupérer suite à l’expiration de Cloud Shell
Si la session Cloud Shell expire, vous pouvez effectuer les étapes suivantes pour récupérer :

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

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser Terraform et AKS pour créer un cluster Kubernetes. Pour en savoir plus sur Terraform sur Azure, consultez les ressources supplémentaires suivantes : 

 [Hub Terraform sur Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentation Terraform Azure Provider](http://aka.ms/terraform)  
 [Source Terraform Azure Provider](http://aka.ms/tfgit)  
 [Modules Terraform Azure](http://aka.ms/tfmodules)