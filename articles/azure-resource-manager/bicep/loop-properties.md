---
title: Définir plusieurs instances d’une propriété dans Bicep
description: Utilisez une boucle de propriété Bicep pour effectuer une itération quand vous créez une propriété de ressource.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 6e9d41136401b28cf330bc828947d35a67c69a43
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225952"
---
# <a name="property-iteration-in-bicep"></a>Itération de propriété dans Bicep

Cet article explique comment créer plusieurs instances d’une propriété dans un fichier Bicep. Vous pouvez ajouter une boucle à la section `properties` d’une ressource et définir de façon dynamique le nombre d’éléments pour une propriété. Vous évitez de répéter la syntaxe dans votre fichier Bicep.

Vous ne pouvez utiliser une boucle qu’avec des ressources de niveau supérieur, même lors de l’application de la boucle à une propriété. Pour plus d’informations sur le remplacement d’une ressource enfant par une ressource de plus haut niveau, consultez [Itération pour une ressource enfant](loop-resources.md#iteration-for-a-child-resource).

Vous pouvez également utiliser une boucle avec les éléments [modules](loop-modules.md), [resources](loop-resources.md), [variables](loop-variables.md) et [outputs](loop-outputs.md).

## <a name="syntax"></a>Syntaxe

Les boucles peuvent être utilisées pour déclarer plusieurs propriétés avec les méthodes suivantes :

- Par l’utilisation de l’index d’une boucle.

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

- Par une itération sur un tableau.

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

  Pour plus d’informations, consultez [Tableau de boucle](#loop-array).

- Itération sur un tableau et un index.

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Limites des boucles

Les itérations de boucle du fichier Bicep ne peuvent pas avoir une valeur négative ni dépasser 800 itérations. 

## <a name="loop-array"></a>Tableau de boucle

Dans le cadre de cet exemple, une itération est effectuée dans un tableau pour que la propriété `subnets` crée deux sous-réseaux au sein d’un réseau virtuel.

```bicep
param rgLocation string = resourceGroup().location

var subnets = [
  {
    name: 'api'
    subnetPrefix: '10.144.0.0/24'
  }
  {
    name: 'worker'
    subnetPrefix: '10.144.1.0/24'
  }
]

resource vnet 'Microsoft.Network/virtualNetworks@2020-07-01' = {
  name: 'vnet'
  location: rgLocation
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.144.0.0/20'
      ]
    }
    subnets: [for subnet in subnets: {
      name: subnet.name
      properties: {
        addressPrefix: subnet.subnetPrefix
      }
    }]
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les autres utilisations possibles des boucles, consultez :
  - [Itération de ressource dans Bicep](loop-resources.md)
  - [Itération de module dans Bicep](loop-modules.md)
  - [Itération de variable dans Bicep](loop-variables.md)
  - [Itération de sortie dans Bicep](loop-outputs.md)
- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Définir des dépendances de ressource](./resource-declaration.md#set-resource-dependencies).
