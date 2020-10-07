---
title: Déployer une spec de modèle en tant que modèle lié
description: Découvrez comment déployer une spec de modèle existante dans un déploiement lié.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 4469e793a7da407f793bfe2885f7bb039e29d736
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369108"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Tutoriel : Déployer une spec de modèle en tant que modèle lié (préversion)

Découvrez comment déployer une [spec de modèle](template-specs.md) existante à l’aide d’un [déploiement lié](linked-templates.md#linked-template). Les specs de modèle permettent de partager des modèles ARM avec d’autres utilisateurs de votre organisation. Une fois que vous avez créé une spec de modèle, vous pouvez la déployer à l’aide d’Azure PowerShell ou Azure CLI. Vous pouvez également déployer la spec de modèle dans le cadre de votre solution à l’aide d’un modèle lié.

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> La fonctionnalité Specs de modèle est actuellement en préversion. Pour l’utiliser, vous devez [vous inscrire à la préversion](https://aka.ms/templateSpecOnboarding).

## <a name="create-a-template-spec"></a>Créer une spec de modèle

Consultez [Démarrage rapide : Créer et déployer une spec de modèle](quickstart-create-template-specs.md) pour créer une spec de modèle pour le déploiement d’un compte de stockage. Vous avez besoin du nom du groupe de ressources de la spec de modèle, du nom de la spec de modèle et de la version de la spec de modèle dans la section suivante.

## <a name="create-the-main-template"></a>Créer le modèle principal

Pour déployer une spec de modèle dans un modèle Resource Manager, ajoutez une [ressource de déploiement](/azure/templates/microsoft.resources/deployments) à votre modèle principal. Dans la propriété `templateLink`, spécifiez l’ID de ressource d’une spec de modèle. Créez un modèle avec le code JSON suivant appelé **azuredeploy.json**. Ce tutoriel part du principe que vous avez enregistré sous le chemin d’accès **c:\Templates\deployTS\azuredeploy.json**, mais vous pouvez utiliser n’importe quel chemin.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

L’ID de spec de modèle est généré à l’aide de la fonction [`resourceID()`](template-functions-resource.md#resourceid). L’argument de groupe de ressources dans la fonction resourceID() est facultatif si le templateSpec se trouve dans le même groupe de ressources que le déploiement actuel.  Vous pouvez également transmettre directement l’ID de ressource en tant que paramètre. Pour récupérer l’ID, utilisez :

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Il existe un problème connu lié à l’obtention de l’ID de la spec de modèle et à son affectation à une variable dans Windows PowerShell.

---

La syntaxe permettant de transmettre des paramètres à la spec de modèle est la suivante :

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> La valeur apiVersion de `Microsoft.Resources/deployments` doit être 2020-06-01 ou une version ultérieure.

## <a name="deploy-the-template"></a>Déployer le modèle

Lorsque vous déployez le modèle lié, il déploie l’application web et le compte de stockage. Le déploiement est identique au déploiement d’autres modèles ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json"
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json"

```

---

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir la création d’une spec de modèle incluant des modèles liés, consultez [Créer une spec de modèle d’un modèle lié](template-specs-create-linked.md).
