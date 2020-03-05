---
title: Définition de plusieurs instances d’une valeur de sortie
description: Utilisez l’opération copy dans un modèle Azure Resource Manager pour retourner une valeur à partir d’un déploiement sur plusieurs itérations.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624512"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Itération sur la sortie dans les modèles Azure Resource Manager

Cet article explique comment créer plusieurs valeurs pour une sortie dans un modèle Azure Resource Manager. En ajoutant l’élément **copy** dans la section outputs de votre modèle, vous pouvez retourner dynamiquement un nombre d’éléments pendant le déploiement.

Il est également possible d’utiliser copy avec des [ressources](copy-resources.md), des [propriétés dans une ressource](copy-properties.md) et des [variables](copy-variables.md).

## <a name="outputs-iteration"></a>Itération sur les sorties

L’élément copy utilise le format général suivant :

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

La propriété **count** indique le nombre d’itérations souhaité pour la valeur de sortie.

La propriété **input** spécifie les propriétés que vous souhaitez répéter. Vous créez un tableau d’éléments construits à partir de la valeur de la propriété **input**. Il peut s’agir d’une propriété unique (par exemple, une chaîne) ou d’un objet avec plusieurs propriétés.

L’exemple suivant crée un nombre variable de comptes de stockage et retourne un point de terminaison pour chacun d’eux :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Le modèle précédent retourne un tableau avec les valeurs suivantes :

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

L’exemple suivant retourne trois propriétés à partir des nouveaux comptes de stockage.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

L’exemple précédent retourne un tableau avec les valeurs suivantes :

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

* Pour effectuer un didacticiel, consultez [Tutoriel : créer plusieurs instances de ressources à l’aide de modèles Resource Manager](template-tutorial-create-multiple-instances.md).
* Pour connaître les autres utilisations de l’élément copy, consultez :
  * [Itération sur la ressource dans les modèles Azure Resource Manager](copy-resources.md)
  * [Itération sur la propriété dans les modèles Azure Resource Manager](copy-properties.md)
  * [Itération sur la variable dans les modèles Azure Resource Manager](copy-variables.md)
* Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](template-syntax.md).
* Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](deploy-powershell.md).

