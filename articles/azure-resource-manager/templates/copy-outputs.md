---
title: Définition de plusieurs instances d’une valeur de sortie
description: Utilisez l’opération copy dans un modèle Azure Resource Manager pour retourner une valeur à partir d’un déploiement sur plusieurs itérations.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617838"
---
# <a name="output-iteration-in-arm-templates"></a>Itération de sortie dans les modèles ARM

Cet article explique comment créer plusieurs valeurs pour une sortie dans votre modèle Azure Resource Manager (ARM). En ajoutant l’élément **copy** dans la section outputs de votre modèle, vous pouvez retourner dynamiquement un nombre d’éléments pendant le déploiement.

Il est également possible d’utiliser copy avec des [ressources](copy-resources.md), des [propriétés dans une ressource](copy-properties.md) et des [variables](copy-variables.md).

## <a name="outputs-iteration"></a>Itération sur les sorties

L’élément copy utilise le format général suivant :

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
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

* Pour suivre un tutoriel, consultez [Tutoriel : Créer plusieurs instances de ressource à l’aide de modèles ARM](template-tutorial-create-multiple-instances.md).
* Pour connaître les autres utilisations de l’élément copy, consultez :
  * [Itération de ressource dans les modèles ARM](copy-resources.md)
  * [Itération de propriété dans les modèles ARM](copy-properties.md)
  * [Itération de variable dans les modèles ARM](copy-variables.md)
* Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles ARM](template-syntax.md).
* Pour savoir comment déployer votre modèle, consultez [Déployer une application avec un modèle ARM](deploy-powershell.md).

