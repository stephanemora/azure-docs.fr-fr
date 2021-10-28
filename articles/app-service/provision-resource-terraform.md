---
title: Créer une application App Service à l’aide d’un modèle Terraform
description: Créez votre première application pour Azure App Service en quelques secondes en utilisant un modèle Terraform, qui est l’un des nombreux modes de déploiement sur App Service.
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.custom: subject-terraform
ms.openlocfilehash: d41b61252abd3e914239a28e78564128a4fbf32d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227147"
---
# <a name="create-app-service-app-using-a-terraform-template"></a>Créer une application App Service à l’aide d’un modèle Terraform

Bien démarrer avec [Azure App Service](overview.md) en déployant une application sur le cloud à l’aide de [Terraform](/azure/developer/terraform/). Comme vous utilisez un niveau App Service gratuit, vous pouvez suivre ce guide de démarrage rapide gratuitement.

Terraform vous permet de définir et de créer des déploiements d’infrastructures complètes dans Azure. Vous générez des modèles Terraform dans un format lisible pour créer et configurer des ressources Azure de manière cohérente et reproductible. Cet article vous explique comment créer une application Windows avec Terraform.

## <a name="prerequisites"></a>Prérequis

Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

Configurez Terraform : Si vous ne l’avez pas déjà fait, configurez Terraform à l’aide de l’une des options suivantes :

* [Configurer Terraform dans Azure Cloud Shell avec Bash](/azure/developer/terraform/get-started-cloud-shell-bash?tabs=bash)
* [Configurer Terraform dans Azure Cloud Shell avec PowerShell](/azure/developer/terraform/get-started-cloud-shell-powershell?tabs=bash)
* [Configurer Terraform dans Windows avec Bash](/azure/developer/terraform/get-started-windows-bash?tabs=bash)
* [Configurer Terraform dans Windows avec PowerShell](/azure/developer/terraform/get-started-windows-powershell?tabs=bash)

L’extension Azure Terraform Visual Studio Code vous permet de travailler avec Terraform à partir de l’éditeur. Avec cette extension, vous pouvez créer, tester et exécuter des configurations Terraform. L’extension prend également en charge la visualisation des graphes de ressources. Consultez [ce guide](/azure/developer/terraform/configure-vs-code-extension-for-terraform) pour configurer l’extension Azure Terraform Visual Studio Code

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est montré ci-dessous. Il déploie un plan de App Service et une application App Service sur Windows et un exemple d’application de Node.js « Hello World » à partir du référentiel d' [exemples Azure](https://github.com/Azure-Samples).

```hcl
# Configure the Azure provider
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 2.65"
    }
  }
  required_version = ">= 0.14.9"
}
provider "azurerm" {
  features {}
}
# Generate a random integer to create a globally unique name
resource "random_integer" "ri" {
  min = 10000
  max = 99999
}
# Create the resource group
resource "azurerm_resource_group" "rg" {
  name     = "myResourceGroup-${random_integer.ri.result}"
  location = "eastus"
}
# Create the Linux App Service Plan
resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "webapp-asp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku {
    tier = "Free"
    size = "F1"
  }
}
# Create the web app, pass in the App Service Plan ID, and deploy code from a public GitHub repo
resource "azurerm_app_service" "webapp" {
  name                = "webapp-${random_integer.ri.result}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
  source_control {
    repo_url           = "https://github.com/Azure-Samples/nodejs-docs-hello-world"
    branch             = "master"
    manual_integration = true
    use_mercurial      = false
  }
}
```

Trois ressources et une sous-ressource Azure sont définies dans le modèle : Liens vers le [Registre Terraform du fournisseur Azure](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs) ci-dessous pour plus d’informations sur l’utilisation :

* [**Microsoft. Resources/resourcegroups**](/azure/templates/microsoft.resources/resourcegroups?tabs=json): créez un groupe de ressources s’il n’en existe pas déjà un.
  * [azurerm_resource_group](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group) 
* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms) : créer un plan App Service.
  * [azurerm_app_service_plan](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service_plan)
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites) : créer une application App Service.
  * [azurerm_app_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service)
* [**Microsoft.Web/sites/sourcecontrols**](/azure/templates/microsoft.web/sites/sourcecontrols) : créer une configuration de déploiement git externe.
  * [source_control](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/app_service#source_control)

Pour plus d’informations sur la construction de modèles Terraform, consultez la[documentation sur Terraform](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS).

## <a name="implement-the-terraform-code"></a>Implémenter le code Terraform

Terraform fournit de nombreuses fonctionnalités pour la gestion, la génération, le déploiement et la mise à jour de l’infrastructure. Les étapes ci-dessous vous guident tout au long du déploiement et de la destruction de vos ressources. Pour plus d’informations, consultez la [Documentation sur Terraform](https://learn.hashicorp.com/collections/terraform/azure-get-started?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) et [Terraform dans la documentation Azure](/azure/developer/terraform/). Vous devez donc vérifier si Terraform fait partie de votre stratégie d’infrastructure Azure.

1. Créez un répertoire dans lequel tester et exécuter l’exemple de code Terraform et définissez-le en tant que répertoire actif.

    ```bash
    mkdir appservice_tf_quickstart
    cd appservice_tf_quickstart
    ```

1. Créez un fichier nommé `main.tf` et insérez le code ci-dessous.

    ```bash
    code main.tf
    ```

1. Initialisez Terraform.

    ```bash
    terraform init
    ```

1. Créez le plan Terraform.

    ```bash
    terraform plan
    ```

1. Approvisionnez les ressources définies dans le fichier de configuration `main.tf` (confirmez l’action en entrant `yes` à l’invite de commandes).

    ```bash
    terraform apply
    ```

## <a name="validate-the-deployment"></a>Valider le déploiement

1. Dans le menu principal de la Portail Azure, sélectionnez **Groupes de ressources** et accédez au groupe de ressources que vous avez créé avec le modèle ci-dessus. Il sera nommé « myResourceGroup- » suivi d’une chaîne d’entiers aléatoires.

1. Vous voyez maintenant toutes les ressources créées par Terraform (un App Service et un plan de App Service).

1. Sélectionnez l' **App service** et accédez à l’url pour vérifier que votre site a été créé correctement. Au lieu de cela, vous pouvez simplement accéder à `http://<app_name>.azurewebsites.net/` où le nom de l’application est « webapp- » suivi de la même chaîne d’entiers aléatoires du groupe de ressources.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, [supprimez le groupe de ressources](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group) ou revenez à votre ligne de commande/terminal et exécutez `terraform destroy` pour supprimer toutes les ressources associées à ce démarrage rapide.

> [!NOTE]
> Vous trouverez d’autres exemples de modèles Azure App ServiceTerraform [ici](./samples-terraform.md). Vous trouverez d’autres exemples de Terraform sur tous les services Azure [ici](https://github.com/hashicorp/terraform-provider-azurerm/tree/main/examples).
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [En savoir plus sur l’utilisation de Terraform dans Azure](/azure/terraform)
> [!div class="nextstepaction"] 
> [Exemples Terraform pour Azure App Service](./samples-terraform.md)