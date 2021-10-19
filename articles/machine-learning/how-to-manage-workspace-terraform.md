---
title: Gérer des espaces de travail à l’aide de Terraform
titleSuffix: Azure Machine Learning
description: Découvrez comment gérer des espaces de travail Azure Machine Learning à l’aide de Terraform.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: deeikele
author: denniseik
ms.date: 10/08/2021
ms.topic: how-to
ms.custom: ''
ms.openlocfilehash: e273c2f10ddbc21d12be9eb62e069d77a5b5d65e
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716466"
---
# <a name="manage-azure-machine-learning-workspaces-using-terraform-preview"></a>Gérer des espaces de travail Azure Machine Learning à l’aide de Terraform (préversion)

Dans cet article, vous apprenez à créer et à gérer un espace de travail Azure Machine Learning à l’aide des fichiers config de Terraform. Les fichiers config basés sur un modèle de [Terraform](/azure/developer/terraform/) vous permettent de définir, créer et configurer des ressources Azure de manière reproductible et prévisible. Terraform suit l’état des ressources et est capable de les nettoyer et de les détruire. 

Une configuration Terraform est un document qui définit les ressources nécessaires à un déploiement. Elle peut également spécifier des variables de déploiement. Les variables permettent de fournir des valeurs d’entrée lorsque vous utilisez la configuration.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Si vous n’en avez pas, essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).
* Une version installée d’[Azure CLI](/cli/azure/).
* Configuration de Terraform : suivez les instructions de cet article et de l’article [Terraform et configurer l’accès à Azure](/azure/developer/terraform/get-started-cloud-shell).

## <a name="declare-the-azure-provider"></a>Déclarer le fournisseur Azure

Créez le fichier config Terraform qui déclare le fournisseur Azure :

1. Créez un nouveau fichier appelé `main.tf`. Si vous utilisez Azure Cloud Shell, utilisez Bash :

    ```bash
    code main.tf
    ```

1. Collez le code suivant dans l’éditeur :

    **main.tf** :
    :::code language="terraform" source="~/terraform/quickstart/101-machine-learning/main.tf":::

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="deploy-a-workspace"></a>Déployer un espace de travail

Les configurations Terraform suivantes peuvent être utilisées pour créer un espace de travail Azure Machine Learning. Lorsque vous créez un espace de travail Azure Machine Learning, divers autres services sont requis en tant que dépendances. Le modèle spécifie également ces [ressources associées à l’espace de travail](/azure/machine-learning/concept-workspace#resources). En fonction de vos besoins, vous pouvez choisir d’utiliser le modèle qui crée des ressources avec une connectivité au réseau public ou privé.

# <a name="public-network-connectivity"></a>[Connectivité au réseau public](#tab/publicworkspace)

Certaines ressources dans Azure nécessitent des noms uniques au niveau mondial. Avant de déployer vos ressources à l’aide des modèles suivants, définissez la variable `name` sur une valeur unique.

**variables.tf** :
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/variables.tf":::

**workspace.tf** :
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/workspace.tf":::

# <a name="private-network-connectivity"></a>[Connectivité de réseau privé](#tab/privateworkspace)

La configuration ci-dessous crée un espace de travail dans un environnement réseau isolé à l’aide de points de terminaison Azure Private Link. Des [zones DNS privées](/azure/dns/private-dns-privatednszone) sont incluses afin que les noms de domaine puissent être résolus au sein du réseau virtuel.

Certaines ressources dans Azure nécessitent des noms uniques au niveau mondial. Avant de déployer vos ressources à l’aide des modèles suivants, définissez la variable `resourceprefix` sur une valeur unique.

Lorsque vous utilisez des points de terminaison de liaison privée pour Azure Container Registry et Azure Machine Learning, les tâches d’Azure Container Registry ne peuvent pas être utilisées pour créer des images d’[environnement](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true). Au lieu de cela, vous pouvez créer des images à l’aide d’un cluster de calcul Azure Machine Learning. Pour configurer le nom du cluster à utiliser, définissez l’argument [image_build_compute_name](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace). Vous pouvez configurer l’[autorisation d’accès public](/azure/machine-learning/how-to-configure-private-link?tabs=python#enable-public-access) à un espace de travail doté d’un point de terminaison de liaison privée à l’aide de l’argument [public_network_access_enabled](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace).

**variables.tf** :
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/variables.tf":::

**workspace.tf** :
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/workspace.tf":::

**network.tf** :
```terraform
# Virtual Network
resource "azurerm_virtual_network" "default" {
  name                = "vnet-${var.name}-${var.environment}"
  address_space       = var.vnet_address_space
  location            = azurerm_resource_group.default.location
  resource_group_name = azurerm_resource_group.default.name
}

resource "azurerm_subnet" "snet-training" {
  name                                           = "snet-training"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.training_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-aks" {
  name                                           = "snet-aks"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.aks_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-workspace" {
  name                                           = "snet-workspace"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.ml_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

# ...
# For full reference, see: https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/network.tf
```

Il existe plusieurs options pour vous connecter à votre espace de travail de point de terminaison de liaison privée. Pour en savoir plus sur ces options, consultez [Vous connecter à votre espace de travail de manière sécurisée](/azure/machine-learning/how-to-secure-workspace-vnet#securely-connect-to-your-workspace).

---

## <a name="troubleshooting"></a>Dépannage

### <a name="resource-provider-errors"></a>Erreurs du fournisseur de ressources

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="current-limitations"></a>Limites actuelles

* Il est actuellement impossible d’utiliser Terraform pour le déploiement d’espaces de travail utilisant des clés de chiffrement gérées par le client.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la prise en charge de Terraform sur Azure, consultez la [documentation Terraform sur Azure](/azure/developer/terraform/).
* Pour trouver des exemples de modèles de « démarrage rapide » pour Terraform, consultez [Modèles de démarrage rapide Azure Terraform](https://github.com/Azure/terraform/tree/master/quickstart) :
  
  * [101 : Espace de travail Machine Learning et calcul](https://github.com/Azure/terraform/tree/master/quickstart/101-machine-learning) : Ensemble minimal de ressources nécessaires pour prendre en main Azure ML.
  * [201 : Espace de travail Machine Learning, calcul et ensemble de composants réseau pour l’isolement réseau](https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure) : Toutes les ressources nécessaires pour créer un environnement pilote de production à utiliser avec les données HBI.
  * [202 : Similaire à 201, mais avec la possibilité d’apporter des composants réseau existants](https://github.com/Azure/terraform/tree/master/quickstart/202-machine-learning-moderately-secure-existing-VNet).
  
* Pour plus d’informations sur le fournisseur Azure Terraform, consultez [Terraform Registry Azure Resource Manager Provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs).
* Pour en savoir plus sur les options de configuration du réseau, consultez [Sécuriser les ressources de l’espace de travail Azure Machine Learning à l’aide de réseaux virtuels (VNets)](/azure/machine-learning/how-to-network-security-overview).
* Pour d’autres déploiements basés sur un modèle Azure Resource Manager, consultez [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](../azure-resource-manager/templates/deploy-rest.md).
