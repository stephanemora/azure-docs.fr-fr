---
title: Ressources enfants dans les modèles
description: Décrit comment définir le nom et le type des ressources enfants dans un modèle de Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 058c28329942a1bd2e5d0e12321022fb022ef74f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474867"
---
# <a name="set-name-and-type-for-child-resources"></a>Définir le nom et le type des ressources enfants

Les ressources enfants sont des ressources qui existent uniquement dans le contexte d’une autre ressource. Par exemple, une [extension de machine virtuelle](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) ne peut pas exister sans [machine virtuelle](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). La ressource d’extension est un enfant de la machine virtuelle.

Dans un modèle Resource Manager, vous pouvez spécifier la ressource enfant dans la ressource parent ou en dehors de la ressource parent. L’exemple suivant illustre la ressource enfant incluse dans la propriété Ressources de la ressource parent.

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

L’exemple suivant montre la ressource enfant en dehors de la ressource parent. Vous pouvez utiliser cette approche si la ressource parent n’est pas déployée dans le même modèle ou si voulez utiliser une[copie](create-multiple-instances.md) pour créer plusieurs ressources enfants.

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

Les valeurs que vous fournissez pour le type et le nom de la ressource varient selon que la ressource enfant est définie dans ou en dehors de la ressource parent.

## <a name="within-parent-resource"></a>Dans la ressource parent

Lorsqu’il est défini dans le type de ressource parent, vous mettez en forme les valeurs de type et de nom sous la forme d’un mot unique sans barre oblique.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

L’exemple suivant montre un réseau virtuel et un sous-réseau. Notez que le sous-réseau est inclus dans le tableau des ressources pour le réseau virtuel. Le nom est défini sur **Subnet1** et le type est défini sur **sous-réseaux**. La ressource enfant est marquée comme étant dépendante de la ressource parent, car la ressource parent doit exister pour que la ressource enfant puisse être déployée.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
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
        "apiVersion": "2018-10-01",
        "type": "subnets",
        "location": "[parameters('location')]",
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

Le type de ressource complet est toujours **Microsoft.Network/virtualNetworks/subnets**. Vous ne fournissez pas **Microsoft.Network/virtualNetworks/** , car il est déduit du type de ressource parent.

Le nom de la ressource enfant est défini sur **Subnet 1**, mais le nom complet inclut le nom parent. Vous ne fournissez pas **VNet1**, car il est déduit de la ressource parent.

## <a name="outside-parent-resource"></a>En dehors de la ressource parent

En cas de définition en dehors de la ressource parent, vous mettez en forme le type et ajouter des barres obliques pour inclure le type et le nom du parent.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

L’exemple suivant montre un réseau virtuel et un sous-réseau qui sont tous deux définis au niveau de la racine. Notez que le sous-réseau est inclus dans le tableau des ressources pour le réseau virtuel. Le nom est défini sur **VNet1/Subnet1** et le type est défini sur **Microsoft.Network/virtualNetworks/subnets**. La ressource enfant est marquée comme étant dépendante de la ressource parent, car la ressource parent doit exister pour que la ressource enfant puisse être déployée.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
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
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "location": "[parameters('location')]",
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

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Création de modèles](template-syntax.md). 

* Pour en savoir plus sur le format du nom de la ressource lors du référencement de la ressource, consultez la [fonction de référence](template-functions-resource.md#reference).
