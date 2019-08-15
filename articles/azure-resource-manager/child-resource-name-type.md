---
title: Nom et type des ressources enfants dans les modèles de Azure Resource Manager
description: Décrit comment définir le nom et le type des ressources enfants dans un modèle de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 32d1eb8fdf2b488e5d6ec46d281392ad6185525b
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827341"
---
# <a name="set-name-and-type-for-child-resources"></a>Définir le nom et le type des ressources enfants

Les ressources enfants sont des ressources qui existent uniquement dans le contexte d’une autre ressource. Par exemple, une [extension de machine virtuelle](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) ne peut pas exister sans [machine virtuelle](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). La ressource d’extension est un enfant de la machine virtuelle.

Dans un modèle Resource Manager, vous pouvez spécifier la ressource enfant dans la ressource parent ou en dehors de la ressource parent. L’exemple suivant illustre la ressource enfant incluse dans la propriété Ressources de la ressource parent.

```json
"resources": [
  {
    // parent resource
    "resources": [
      // child resource
    ]
  }
]
```

L’exemple suivant montre la ressource enfant en dehors de la ressource parent. Vous pouvez utiliser cette approche si la ressource parent n’est pas déployée dans le même modèle ou si voulez utiliser une[copie](resource-group-create-multiple.md) pour créer plusieurs ressources enfants.

```json
"resources": [
  {
    // parent resource
  },
  {
    // child resource
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

L’exemple suivant montre un réseau virtuel et un sous-réseau qui sont tous deux définis au niveau de la racine. Notez que le sous-réseau est inclus dans le tableau des ressources pour le réseau virtuel. Le nom est défini sur VNet1/Subnet1 et le type est défini sur **Microsoft.Network/virtualNetworks/subnets**. La ressource enfant est marquée comme étant dépendante de la ressource parent, car la ressource parent doit exister pour que la ressource enfant puisse être déployée.

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

* Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Création de modèles](resource-group-authoring-templates.md). 

* Pour en savoir plus sur le format du nom de la ressource lors du référencement de la ressource, consultez la [fonction de référence](resource-group-template-functions-resource.md#reference).
