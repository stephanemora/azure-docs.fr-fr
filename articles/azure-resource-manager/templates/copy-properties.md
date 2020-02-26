---
title: Définir plusieurs instances d’une propriété
description: Utilisez l’opération de copie dans un modèle Azure Resource Manager pour effectuer une itération à plusieurs reprises lors de la création d’une propriété sur une ressource.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b1e31f981f361b4cfbe7e7930f2c70bfce8b8656
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210670"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Itération de propriétés dans des modèles Azure Resource Manager

Cet article explique comment créer plusieurs instances d’une propriété dans votre modèle Azure Resource Manager. En ajoutant l’élément **copier** dans la section Propriétés d’une ressource de votre modèle, vous pouvez définir dynamiquement le nombre d’éléments pour une propriété pendant le déploiement. Vous évitez également de devoir répéter la syntaxe du modèle.

Vous pouvez aussi utiliser la fonction copier avec les [ressources](copy-resources.md) et les [variables](copy-variables.md).

## <a name="property-iteration"></a>Itération de propriété

L’élément copier utilise le format général suivant :

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Pour **name**, indiquez le nom de la propriété de ressource que vous souhaitez créer. La propriété **count** spécifie le nombre d’itérations que vous souhaitez pour la propriété.

La propriété **input** spécifie les propriétés que vous souhaitez répéter. Vous créez un tableau d’éléments construits à partir de la valeur de la propriété **input**.

L’exemple suivant montre comment appliquer `copy` à la propriété dataDisks sur une machine virtuelle :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Notez que, lorsque vous utilisez `copyIndex` à l’intérieur d’une itération de propriété, vous devez fournir le nom de l’itération.

> [!NOTE]
> L’itération de propriété prend également en charge un argument de contrepartie. La contrepartie doit venir après le nom de l’itération, par exemple copyIndex('dataDisks', 1).
>

Le Gestionnaire des ressources développe le tableau `copy` durant le déploiement. Le nom du tableau devient celui de la propriété. Les valeurs d’entrée deviennent les propriétés de l’objet. Le modèle déployé devient :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

L'élément copy est un tableau. Vous pouvez donc spécifier plusieurs propriétés pour la ressource.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Vous pouvez utiliser des itérations de ressource et de propriété ensemble. Référencez l’itération de propriété par son nom.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Limites de copie

Le nombre ne peut pas dépasser 800.

Le nombre ne peut pas être négatif. Si vous déployez un modèle avec Azure PowerShell 2.6 (ou une version ultérieure), l’interface Azure CLI 2.0.74 (ou une version ultérieure) ou l’API REST version **2019-05-10** (ou une version ultérieure), vous pouvez définir le nombre sur zéro. Les versions antérieures de PowerShell, de l’interface CLI et de l’API REST ne prennent pas en charge le nombre zéro.

## <a name="example-templates"></a>Exemples de modèles

L’exemple suivant montre un scénario courant pour la création de plusieurs valeurs pour une propriété.

|Modèle  |Description  |
|---------|---------|
|[Déploiement de machine virtuelle avec un nombre variable de disques de données](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Déploie plusieurs disques de données avec une machine virtuelle. |

## <a name="next-steps"></a>Étapes suivantes

* Pour effectuer un didacticiel, consultez [Tutoriel : créer plusieurs instances de ressources à l’aide de modèles Resource Manager](template-tutorial-create-multiple-instances.md).
* Pour les autres utilisations de l’élément copier, consultez [Itération de ressources dans des modèles Azure Resource Manager](copy-resources.md) et [Itération de variables dans des modèles Azure Resource Manager](copy-variables.md).
* Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](template-syntax.md).
* Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](deploy-powershell.md).

