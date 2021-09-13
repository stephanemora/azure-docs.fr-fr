---
title: Définir plusieurs instances d’une propriété dans Bicep
description: Utilisez une boucle de propriété Bicep pour effectuer une itération quand vous créez une propriété de ressource.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59ca13e4f6ab29a71c543fcbe779ef616beba43b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025827"
---
# <a name="property-iteration-in-bicep"></a>Itération de propriété dans Bicep

Cet article explique comment créer plusieurs instances d’une propriété dans un fichier Bicep. Vous pouvez ajouter une boucle à la section `properties` d’une ressource et définir de façon dynamique le nombre d’éléments pour une propriété pendant le déploiement. Vous évitez également de répéter la syntaxe dans votre fichier Bicep.

Vous ne pouvez utiliser une boucle qu’avec des ressources de niveau supérieur, même lors de l’application de la boucle à une propriété. Pour plus d’informations sur le remplacement d’une ressource enfant par une ressource de plus haut niveau, consultez [Itération pour une ressource enfant](loop-resources.md#iteration-for-a-child-resource).

Vous pouvez également utiliser une boucle avec des [ressources](loop-resources.md), des [variables](loop-variables.md) et des [sorties](loop-outputs.md).

## <a name="syntax"></a>Syntax

Les boucles peuvent être utilisées pour déclarer plusieurs propriétés avec les méthodes suivantes :

- Par une itération sur un tableau.

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Par une itération sur les éléments d’un tableau.

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Par l’utilisation de l’index d’une boucle.

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>Limites des boucles

Les itérations de boucle du fichier Bicep ne peuvent pas avoir une valeur négative ni dépasser 800 itérations. Pour déployer des fichiers Bicep, installez la dernière version des [outils Bicep](install.md).

## <a name="property-iteration"></a>Itération de propriété

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
  - [Itération de ressource dans les fichiers Bicep](loop-resources.md)
  - [Itération de variable dans les fichiers Bicep](loop-variables.md)
  - [Itération de sortie dans les fichiers Bicep](loop-outputs.md)
- Pour plus d’informations sur les différentes sections d’un fichier Bicep, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](file.md).
- Pour plus d’informations sur le déploiement de plusieurs ressources, consultez [Utiliser des modules Bicep](modules.md).
- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Définir des dépendances de ressource](./resource-declaration.md#set-resource-dependencies).
- Pour savoir comment effectuer un déploiement avec PowerShell, consultez [Déployer des ressources avec Bicep et Azure PowerShell](deploy-powershell.md).
- Pour savoir comment effectuer un déploiement avec Azure CLI, consultez [Déployer des ressources avec Bicep et Azure CLI](deploy-cli.md).
