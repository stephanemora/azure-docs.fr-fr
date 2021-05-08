---
title: Ressources enfants dans les modèles
description: Décrit comment définir le nom et le type des ressources enfants dans un modèle Azure Resource Manager (modèle ARM).
ms.topic: conceptual
ms.date: 04/23/2021
ms.openlocfilehash: 1928a94fbfefc694091a3f09ea577e8250540bf4
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107949521"
---
# <a name="set-name-and-type-for-child-resources"></a>Définir le nom et le type des ressources enfants

Les ressources enfants sont des ressources qui existent uniquement dans le contexte d’une autre ressource. Par exemple, une [extension de machine virtuelle](/azure/templates/microsoft.compute/virtualmachines/extensions) ne peut pas exister sans [machine virtuelle](/azure/templates/microsoft.compute/virtualmachines). La ressource d’extension est un enfant de la machine virtuelle.

Chaque ressource parente accepte uniquement certains types de ressources comme ressources enfants. Le type de ressource de la ressource enfant comprend le type de ressource de la ressource parent. Par exemple, `Microsoft.Web/sites/config` et `Microsoft.Web/sites/extensions` sont des ressources enfant de la ressource `Microsoft.Web/sites`. Les types de ressource acceptés sont spécifiés dans le [schéma de modèle](https://github.com/Azure/azure-resource-manager-schemas) de la ressource parente.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

Dans un modèle Azure Resource Manager (modèle ARM), vous pouvez spécifier la ressource enfant dans la ressource parent ou en dehors de la ressource parent. Les valeurs que vous fournissez pour le nom et le type de la ressource varient selon que la ressource enfant est définie dans la ressource parent ou en dehors de celle-ci.

## <a name="within-parent-resource"></a>Dans la ressource parent

L’exemple suivant illustre la ressource enfant incluse dans la propriété Ressources de la ressource parent.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Les ressources enfants peuvent uniquement être définies sur cinq niveaux.

Lorsqu’elles sont définies dans le type de ressource parent, les valeurs de type et de nom sont formatées en un seul segment sans barres obliques.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

L’exemple suivant montre un réseau virtuel et un sous-réseau. Notez que le sous-réseau est inclus dans le tableau des ressources pour le réseau virtuel. Le nom est défini sur **Subnet1** et le type est défini sur **sous-réseaux**. La ressource enfant est marquée comme étant dépendante de la ressource parent, car la ressource parent doit exister pour que la ressource enfant puisse être déployée.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Le type de ressource complet est toujours `Microsoft.Network/virtualNetworks/subnets`. Vous ne fournissez pas `Microsoft.Network/virtualNetworks/`, car il est déduit du type de ressource parent.

Le nom de la ressource enfant est défini sur **Subnet 1**, mais le nom complet inclut le nom parent. Vous ne fournissez pas **VNet1**, car il est déduit de la ressource parent.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

Une déclaration de ressource imbriquée doit apparaître au niveau supérieur de la syntaxe de la ressource parent. Les déclarations peuvent être imbriquées à une profondeur arbitraire, tant que chaque niveau est un type enfant de sa ressource parent.

Lorsqu’elles sont définies dans le type de ressource parent, les valeurs de type et de nom sont formatées en un seul segment sans barres obliques. L’exemple suivant montre un réseau virtuel et un sous-réseau. Notez que le sous-réseau est inclus dans le tableau des ressources pour le réseau virtuel. Le nom est défini sur **Subnet1** et le type est défini sur **sous-réseaux**.

```bicep
param location string

resource VNet1 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: 'VNet1'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
  }

  resource VNet1_Subnet1 'subnets' = {
    name: 'Subnet1'
    properties: {
      addressPrefix: '10.0.0.0/24'
    }
  }
}
```

Le type de ressource complet est toujours `Microsoft.Network/virtualNetworks/subnets`. Vous ne fournissez pas `Microsoft.Network/virtualNetworks/`, car il est déduit du type et de la version de la ressource parent. La ressource imbriquée peut éventuellement déclarer une version d’API en utilisant la syntaxe `<segment>@<version>`. Si la ressource imbriquée omet la version de l’API, la version d’API de la ressource parent est utilisée. Si la ressource imbriquée spécifie une version d’API, la version d’API spécifiée est utilisée.

Le nom de la ressource enfant est défini sur **Subnet 1**, mais le nom complet inclut le nom parent. Vous ne fournissez pas VNet1, car il est déduit de la ressource parent.

Pour accéder au nom symbolique de la ressource enfant, vous devez utiliser l’opérateur `::`. Par exemple, pour générer une propriété à partir d’une ressource enfant :

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

Une ressource imbriquée peut accéder aux propriétés de sa ressource parent. D’autres ressources déclarées dans le corps de la même ressource parent peuvent se référer les unes aux autres et les règles classiques relatives aux dépendances cycliques s’appliquent. Une ressource parent ne peut pas accéder aux propriétés des ressources qu’elle contient, ce qui entraînerait une dépendance cyclique.

---

## <a name="outside-parent-resource"></a>En dehors de la ressource parent

L’exemple suivant montre la ressource enfant en dehors de la ressource parent. Vous pouvez utiliser cette approche si la ressource parent n’est pas déployée dans le même modèle ou si voulez utiliser une[copie](copy-resources.md) pour créer plusieurs ressources enfants.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

En cas de définition en dehors de la ressource parent, vous mettez en forme le type et ajouter des barres obliques pour inclure le type et le nom du parent.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

L’exemple suivant montre un réseau virtuel et un sous-réseau qui sont tous deux définis au niveau de la racine. Notez que le sous-réseau est inclus dans le tableau des ressources pour le réseau virtuel. Le nom est défini sur **VNet1/Subnet1** et le type est défini sur `Microsoft.Network/virtualNetworks/subnets`. La ressource enfant est marquée comme étant dépendante de la ressource parent, car la ressource parent doit exister pour que la ressource enfant puisse être déployée.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  <child-resource-properties>
}
```

En cas de définition en dehors de la ressource parent, vous mettez en forme le type et ajouter des barres obliques pour inclure le type et le nom du parent.

L’exemple suivant montre un réseau virtuel et un sous-réseau qui sont tous deux définis au niveau de la racine. Notez que le sous-réseau est inclus dans le tableau des ressources pour le réseau virtuel. Le nom est défini sur **VNet1/Subnet1** et le type est défini sur `Microsoft.Network/virtualNetworks/subnets`. La ressource enfant est marquée comme étant dépendante de la ressource parent, car la ressource parent doit exister pour que la ressource enfant puisse être déployée.

```bicep
param location string

resource VNet1 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: 'VNet1'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
  }
}

resource VNet1_Subnet1 'Microsoft.Network/virtualNetworks/subnets@2018-10-01' = {
  name: '${VNet1.name}/Subnet1'
  properties: {
    addressPrefix: '10.0.0.0/24'
  }
}
```

---

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à créer des modèles ARM, consultez [Comprendre la structure et la syntaxe des modèles ARM](template-syntax.md).
* Pour en savoir plus sur le format du nom de la ressource lors du référencement de la ressource, consultez la [fonction de référence](template-functions-resource.md#reference).
