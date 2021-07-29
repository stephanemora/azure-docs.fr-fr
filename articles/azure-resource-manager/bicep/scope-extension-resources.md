---
title: Étendue sur les types de ressources d’extension (Bicep)
description: Décrit comment utiliser la propriété scope lors du déploiement de types de ressources d’extension avec Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59b6576dfb1bd5e0ac4f56e6a59b6ea4d5c4b5f4
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026464"
---
# <a name="set-scope-for-extension-resources-in-bicep"></a>Définir l’étendue des ressources d’extension dans Bicep

Une ressource d’extension est une ressource qui modifie une autre ressource. Par exemple, vous pouvez attribuer un rôle à une ressource. L’attribution de rôle est un type de ressource d’extension.

Pour obtenir la liste complète des types de ressources d’extension, consultez [Types de ressources qui étendent les fonctionnalités d’autres ressources](../management/extension-resource-types.md).

Cet article montre comment définir l’étendue d’un type de ressource d’extension lorsqu’il est déployé avec un fichier Bicep. Il décrit la propriété scope qui est disponible pour les ressources d’extension lorsqu’elles s’appliquent à une ressource.

> [!NOTE]
> La propriété scope est uniquement disponible pour les types de ressources d’extension. Pour spécifier une étendue différente pour un type de ressource qui n’est pas un type d’extension, utilisez un [module](modules.md).

## <a name="apply-at-deployment-scope"></a>Appliquer au niveau de l’étendue du déploiement

Pour appliquer un type de ressource d’extension au niveau de l’étendue du déploiement, vous ajoutez la ressource à votre modèle, comme avec n’importe quel type de ressource. Les étendues disponibles sont un [groupe de ressources](deploy-to-resource-group.md), un [abonnement](deploy-to-subscription.md), un [groupe d’administration](deploy-to-management-group.md) et un [locataire](deploy-to-tenant.md). L’étendue de déploiement doit prendre en charge le type de ressource.

Le modèle suivant déploie un verrou.

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

L’exemple suivant attribue un rôle.

```bicep
targetScope = 'subscription'

@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}

resource roleAssignSub 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
}
```

## <a name="apply-to-resource"></a>Appliquer à la ressource

Pour appliquer une ressource d’extension à une ressource, utilisez la propriété `scope`. Définissez la propriété scope sur le nom de la ressource à laquelle vous ajoutez l’extension. La propriété scope est une propriété racine pour le type de ressource d’extension.

L’exemple suivant crée un compte de stockage et lui applique un rôle.

```bicep
@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()
param location string = resourceGroup().location

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}
var uniqueStorageName = 'storage${uniqueString(resourceGroup().id)}'

resource storageName 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}

resource roleAssignStorage 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
  scope: storageName
  dependsOn: [
    storageName
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement sur des étendues, consultez :

* [Déploiements de groupes de ressources](deploy-to-resource-group.md)
* [Déploiements d’abonnements](deploy-to-subscription.md)
* [Déploiements de groupes d’administration](deploy-to-management-group.md)
* [Déploiements de locataires](deploy-to-tenant.md)