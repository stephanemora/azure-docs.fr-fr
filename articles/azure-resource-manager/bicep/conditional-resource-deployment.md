---
title: Déploiement conditionnel avec Bicep
description: Décrit comment déployer une ressource de manière conditionnelle dans Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 9636444af81b000443dc72cf6e3fc1d8d6da5093
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537078"
---
# <a name="conditional-deployment-in-bicep"></a>Déploiement conditionnel dans Bicep

Parfois, vous devez déployer une ressource de manière conditionnelle dans Bicep. Utilisez le mot clé `if` pour spécifier si la ressource est déployée. La valeur de la condition est résolue en true ou false. Lorsque la valeur est true, la ressource est créée. Lorsque la valeur est false, la ressource n’est pas créée. La valeur ne peut être appliquée qu’à l’ensemble de la ressource.

> [!NOTE]
> L’exécution du déploiement conditionnel n’inclut pas les [ressources enfants](child-resource-name-type.md). Si vous souhaitez déployer une ressource et ses ressources enfants de manière conditionnelle, vous devez appliquer la même condition à chaque type de ressource.

## <a name="deploy-condition"></a>Condition de déploiement

Vous pouvez transmettre dans un paramètre une valeur indiquant si une ressource est déployée. L’exemple suivant opère un déploiement de manière conditionnelle dans une zone DNS.

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

Les conditions peuvent être utilisées avec des déclarations de dépendance. Si l’identificateur de la ressource conditionnelle est spécifié dans `dependsOn` d’une autre ressource (dépendance explicite), la dépendance est ignorée si la condition prend la valeur false au moment du déploiement du modèle. Si la condition prend la valeur true, la dépendance est respectée. Le référencement d’une propriété d’une ressource conditionnelle (dépendance implicite) est autorisé, mais peut générer une erreur d’exécution dans certains cas.

## <a name="new-or-existing-resource"></a>Ressource nouvelle ou existante

Vous pouvez utiliser un déploiement conditionnel pour créer une ressource ou en utiliser une existante. L’exemple suivant montre comment déployer un nouveau compte de stockage ou utiliser un compte de stockage existant.

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

Lorsque le paramètre `newOrExisting` a la valeur **new**, la condition donne le résultat true. Le compte de stockage est déployé. En revanche, quand le paramètre `newOrExisting` a la valeur **existing**, la condition donne le résultat false et le compte de stockage n’est pas déployé.

Pour un exemple de modèle complet qui utilise l’élément `condition`, consultez [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-new-or-existing-conditions) (Machine virtuelle avec un réseau virtuel, un stockage et une adresse IP publique nouveaux ou existants).

## <a name="runtime-functions"></a>Fonctions de runtime

Si vous utilisez une fonction de [référence](./bicep-functions-resource.md#reference) ou de [liste](./bicep-functions-resource.md#list) avec une ressource qui est déployée conditionnellement, la fonction est évaluée même si la ressource n’est pas déployée. Vous obtenez une erreur si la fonction fait référence à une ressource qui n’existe pas.

Utilisez l’opérateur [d’expression conditionnelle ?:](./operators-logical.md#conditional-expression--) pour vous assurer que la fonction est évaluée uniquement pour les conditions lorsque la ressource est déployée. L’exemple de modèle suivant montre comment utiliser cette fonction avec des expressions qui sont uniquement valides de manière conditionnelle.

```bicep
param vmName string
param location string
param logAnalytics string = ''

resource vmName_omsOnboarding 'Microsoft.Compute/virtualMachines/extensions@2017-03-30' = if (!empty(logAnalytics)) {
  name: '${vmName}/omsOnboarding'
  location: location
  properties: {
    publisher: 'Microsoft.EnterpriseCloud.Monitoring'
    type: 'MicrosoftMonitoringAgent'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    settings: {
      workspaceId: ((!empty(logAnalytics)) ? reference(logAnalytics, '2015-11-01-preview').customerId : json('null'))
    }
    protectedSettings: {
      workspaceKey: ((!empty(logAnalytics)) ? listKeys(logAnalytics, '2015-11-01-preview').primarySharedKey : json('null'))
    }
  }
}

output mgmtStatus string = ((!empty(logAnalytics)) ? 'Enabled monitoring for VM!' : 'Nothing to enable')
```

Vous définissez une [ressource comme étant dépendante](./resource-declaration.md#set-resource-dependencies) d’une ressource conditionnelle exactement comme vous le feriez pour une autre ressource. Quand une ressource conditionnelle n’est pas déployée, Azure Resource Manager la supprime automatiquement des dépendances nécessaires.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un module Microsoft Learn sur les conditions et les boucles, consultez [Créer des modèles Bicep flexibles à l’aide de conditions et de boucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/).
* Pour obtenir des recommandations sur la création de fichiers Bicep, consultez [Bonnes pratiques pour Bicep](best-practices.md).
* Pour créer plusieurs instances d’une ressource, consultez [Itération de ressources dans Bicep](loop-resources.md).
