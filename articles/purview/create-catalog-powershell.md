---
title: 'Démarrage rapide : Créer un compte Purview avec PowerShell/Azure CLI'
description: Ce guide de démarrage rapide détaille la création d’un compte Azure Purview avec Azure PowerShell ou Azure CLI.
author: hophanms
ms.author: hophan
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom: mode-api
ms.openlocfilehash: b4bcd427508f3ecf66b0cca4bdade023c3f87d61
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037812"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Démarrage rapide : Créer un compte Azure Purview en utilisant Azure PowerShell ou Azure CLI

Dans ce guide de démarrage rapide, vous allez créer un compte Azure Purview en utilisant Azure PowerShell ou l’interface Azure CLI. La [référence PowerShell pour Purview](/powershell/module/az.purview/) est disponible, mais cet article vous guide dans les étapes à suivre pour créer un compte avec PowerShell.

Azure Purview est un service de gouvernance de données qui vous aide à gérer et régir votre paysage de données. En vous connectant aux données de vos sources locales, multiclouds et SaaS (software-as-a-service), Purview crée un mappage à jour de vos informations. Il identifie et classe les données sensibles, et fournit un lignage de bout en bout. Les consommateurs de données peuvent découvrir des données au sein de votre organisation, et les administrateurs de données sont en mesure d’auditer, de sécuriser et de garantir l’utilisation appropriée de vos données.

Pour plus d’informations sur Purview, [consultez notre page de présentation](overview.md). Pour plus d’informations sur le déploiement de Purview dans votre organisation, [consultez nos bonnes pratiques de déploiement](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### <a name="install-powershell"></a>Installer PowerShell

 Installez Azure PowerShell ou Azure CLI dans votre machine cliente pour déployer le modèle : [Déploiement en ligne de commande](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="create-an-azure-purview-account"></a>Créer un compte Azure Purview

1. Se connecter avec des informations d’identification Azure

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Si vous avez plusieurs abonnements Azure, sélectionnez celui que vous souhaitez utiliser :

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Créez un groupe de ressources pour votre compte Purview. Vous pouvez ignorer cette étape si vous en avez déjà un :

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Créez un fichier de modèle Purview, tel que `purviewtemplate.json`. Vous pouvez mettre à jour `name`, `location` et `capacity` (`4` ou `16`) :

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Déployer le modèle Purview

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. La commande de déploiement retourne des résultats. Recherchez `ProvisioningState` pour voir si le déploiement s’est déroulé correctement.
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez vu comment créer un compte Azure Purview.

Lisez les articles suivants pour apprendre à naviguer dans Purview Studio, à créer une collection et à accorder l’accès à Purview.

* [Comment utiliser Purview Studio](use-purview-studio.md)
* [Création d'une collection](quickstart-create-collection.md)
* [Ajouter des utilisateurs à votre compte Azure Purview](catalog-permissions.md)
