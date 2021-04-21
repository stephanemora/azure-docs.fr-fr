---
title: Définir plusieurs instances d’une propriété
description: Utilisez l’opération de copie dans un modèle Azure Resource Manager (modèle ARM) pour effectuer une itération à plusieurs reprises lors de la création d’une propriété sur une ressource.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 16c293f1c3aff64aeb8b6cae4b7f1aa14dcd0a77
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479999"
---
# <a name="property-iteration-in-arm-templates"></a>Itération de propriété dans les modèles ARM

Cet article explique comment créer plusieurs instances d’une propriété dans votre modèle Azure Resource Manager (modèle ARM). En ajoutant une boucle de copie dans la section Propriétés d’une ressource de votre modèle, vous pouvez définir dynamiquement le nombre d’éléments pour une propriété pendant le déploiement. Cela vous évite également de répéter la syntaxe du modèle.

Vous ne pouvez utiliser la boucle de copie qu’avec des ressources de niveau supérieur, même lors de l’application de la boucle de copie à une propriété. Pour plus d’informations sur le remplacement d’une ressource enfant par une ressource de plus haut niveau, consultez [Itération pour une ressource enfant](copy-resources.md#iteration-for-a-child-resource).

Vous pouvez également utiliser la boucle de copie avec des [ressources](copy-resources.md), des [variables](copy-variables.md) et des [sorties](copy-outputs.md).

## <a name="syntax"></a>Syntaxe

# <a name="json"></a>[JSON](#tab/json)

Ajoutez l’élément `copy` à la section Ressources de votre modèle pour définir le nombre d’éléments pour une propriété. L’élément copier utilise le format général suivant :

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Pour `name`, indiquez le nom de la propriété de ressource que vous souhaitez créer.

La propriété `count` spécifie le nombre d’itérations que vous souhaitez pour la propriété.

La propriété `input` spécifie les propriétés que vous souhaitez répéter. Vous créez un tableau d’éléments construits à partir de la valeur de la propriété `input`.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Les boucles peuvent être utilisées pour déclarer plusieurs propriétés via les méthodes suivantes :

- Itération sur un tableau :

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Itération sur les éléments d’un tableau

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Utilisation de l’index de boucle

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limites de copie

Le nombre ne peut pas dépasser 800.

Le nombre ne peut pas être négatif. Il peut être zéro si vous déployez le modèle avec une version récente d’Azure CLI, de PowerShell ou de l’API REST. Plus précisément, vous devez utiliser :

- Azure PowerShell **2.6** ou version ultérieure
- Azure CLI **2.0.74** ou version ultérieure
- API REST version **2019-05-10** ou ultérieure
- Les [déploiements liés](linked-templates.md) doivent utiliser la version **2019-05-10** de l’API ou une version ultérieure pour le type de ressource de déploiement

Les versions antérieures de PowerShell, de l’interface CLI et de l’API REST ne prennent pas en charge le nombre zéro.

## <a name="property-iteration"></a>Itération de propriété

L’exemple suivant montre comment appliquer une boucle de copie à la propriété `dataDisks` sur une machine virtuelle :

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

Notez que, lorsque vous utilisez `copyIndex` à l’intérieur d’une itération de propriété, vous devez fournir le nom de l’itération. L’itération de propriété prend également en charge un argument de contrepartie. L’argument offset doit venir après le nom de l’itération, par exemple `copyIndex('dataDisks', 1)`.

Le modèle déployé devient :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
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

L’opération copy se révèle utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. Utilisez la fonction `length` sur le tableau pour spécifier le nombre d’itérations, et `copyIndex` pour récupérer l’index actuel dans le tableau.

L’exemple de modèle suivant crée un groupe de basculement pour les bases de données passées en tant que tableau.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "primaryServerName": {
      "type": "string"
    },
    "secondaryServerName": {
      "type": "string"
    },
    "databaseNames": {
      "type": "array",
      "defaultValue": [
        "mydb1",
        "mydb2",
        "mydb3"
      ]
    }
  },
  "variables": {
    "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/failoverGroups",
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('failoverName')]",
      "properties": {
        "readWriteEndpoint": {
          "failoverPolicy": "Automatic",
          "failoverWithDataLossGracePeriodMinutes": 60
        },
        "readOnlyEndpoint": {
          "failoverPolicy": "Disabled"
        },
        "partnerServers": [
          {
            "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
          }
        ],
        "copy": [
          {
            "name": "databases",
            "count": "[length(parameters('databaseNames'))]",
            "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
          }
        ]
      }
    }
  ],
  "outputs": {
  }
}
```

L’élément `copy` est un tableau. Vous pouvez donc spécifier plusieurs propriétés pour la ressource.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

Le modèle déployé devient :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
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

---

Vous pouvez utiliser des itérations de ressource et de propriété ensemble. Référencez l’itération de propriété par son nom.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>Exemples de modèles

L’exemple suivant montre un scénario courant pour la création de plusieurs valeurs pour une propriété.

|Modèle  |Description  |
|---------|---------|
|[Déploiement de machine virtuelle avec un nombre variable de disques de données](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Déploie plusieurs disques de données avec une machine virtuelle. |

## <a name="next-steps"></a>Étapes suivantes

- Pour suivre un tutoriel, consultez [Tutoriel : Créer plusieurs instances de ressources grâce à des modèles ARM](template-tutorial-create-multiple-instances.md).
- Pour connaître les autres utilisations de la boucle de copie, consultez :
  - [Itération de ressource dans les modèles ARM](copy-resources.md)
  - [Itération de variable dans les modèles ARM](copy-variables.md)
  - [Itération de sortie dans les modèles ARM](copy-outputs.md)
- Pour plus d’informations sur les différentes sections d’un modèle, consultez [Présentation de la structure et de la syntaxe des modèles ARM](template-syntax.md).
- Pour savoir comment déployer votre modèle, consultez [Déployer des ressources avec des modèles ARM et Azure PowerShell](deploy-powershell.md).
