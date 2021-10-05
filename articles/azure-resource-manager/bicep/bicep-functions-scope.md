---
title: Fonctions Bicep - étendues
description: Décrit les fonctions à utiliser dans un fichier Bicep pour récupérer les valeurs relatives aux étendues de déploiement.
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 4432aa528dbdd565128a15eb79d4b4c01572c924
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827444"
---
# <a name="scope-functions-for-bicep"></a>Fonctions d’étendue pour Bicep

Cet article décrit les fonctions Bicep pour l’obtention des valeurs d’étendue.

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(name)`

Retourne un objet utilisé pour définir l’étendue à un groupe d’administration.

### <a name="remarks"></a>Remarques

`managementGroup()` ne peut être utilisée que sur des [déploiements de groupe d’administration](deploy-to-management-group.md). Elle retourne le groupe d’administration actuel pour l’opération de déploiement.

`managementGroup(name)` peut être utilisée pour toute étendue de déploiement.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| name |Non |string |Identificateur unique du groupe d’administration sur lequel effectuer le déploiement. N’utilisez pas le nom complet du groupe d’administration. Si vous ne fournissez pas de valeur, le groupe d’administration actuel est retourné. |

### <a name="return-value"></a>Valeur de retour

Objet utilisé pour définir la propriété `scope` sur un type de ressource de [module](modules.md#configure-module-scopes) ou [d’extension](scope-extension-resources.md).

### <a name="management-group-example"></a>Exemple de groupe d’administration

L’exemple suivant définit l’étendue d’un module sur un groupe d’administration.

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
``` 

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

`resourceGroup(resourceGroupName)`

`resourceGroup(subscriptionId, resourceGroupName)`

Retourne un objet utilisé pour définir l’étendue à un groupe de ressources.

ou

Renvoie un objet qui représente le groupe de ressources actuel.

### <a name="remarks"></a>Remarques

La fonction resourceGroup a deux utilisations distinctes. Une utilisation permet de définir l’étendue sur un type de ressource de [module](modules.md#configure-module-scopes) ou [d’extension](scope-extension-resources.md). L’autre utilisation consiste à obtenir des détails sur le groupe de ressources actuel. Le positionnement de la fonction détermine son utilisation. Lorsqu’elle est utilisée pour définir la propriété `scope`, elle retourne un objet d’étendue.

`resourceGroup()` peut être utilisé pour définir l’étendue ou obtenir des détails sur le groupe de ressources.

`resourceGroup(resourceGroupName)` et `resourceGroup(subscriptionId, resourceGroupName)` ne peuvent être utilisés que pour définir l’étendue.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceGroupName |Non |string | Nom du groupe de ressources sur lequel effectuer le déploiement. Si vous ne fournissez pas de valeur, le groupe de ressources actuel est retourné. |
| subscriptionId |Non |string |Identificateur unique de l’abonnement sur lequel effectuer le déploiement. Si vous ne fournissez pas de valeur, l’abonnement actuel est retourné. |

### <a name="return-value"></a>Valeur de retour

Lorsqu’elle est utilisée pour définir l’étendue, la fonction retourne un objet qui est valide pour la propriété `scope` sur un type de ressource de module ou d’extension.

Lorsqu’elle est utilisée pour obtenir des détails sur le groupe de ressources, la fonction retourne le format suivant :

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

La propriété **ManagedBy** est retournée uniquement pour les groupes de ressources qui contiennent des ressources gérées par un autre service. Pour Managed Applications, Databricks et AKS, la valeur de la propriété est l’ID de ressource de la ressource de gestion.

### <a name="resource-group-example"></a>Exemple de groupe de ressources

L’exemple suivant définit l’étendue d’un module à un groupe de ressources.

```bicep
param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

L’exemple suivant retourne les propriétés du groupe de ressources.

```bicep
output resourceGroupOutput object = resourceGroup()
```

Il retourne un objet au format suivant :

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Une utilisation courante de la fonction resourceGroup consiste à créer des ressources dans le même emplacement que le groupe de ressources. L’exemple suivant utilise l’emplacement du groupe de ressources pour une valeur de paramètre par défaut.

```bicep
param location string = resourceGroup().location
```

Vous pouvez également utiliser la fonction resourceGroup pour appliquer des balises du groupe de ressources à une ressource. Pour plus d’informations, voir [Appliquer les balises d’un groupe de ressources](../management/tag-resources.md#apply-tags-from-resource-group).

## <a name="subscription"></a>subscription

`subscription()`

`subscription(subscriptionId)`

Retourne un objet utilisé pour définir l’étendue à un abonnement.

ou

Retourne des détails concernant l’abonnement pour le déploiement actuel.

### <a name="remarks"></a>Remarques

La fonction subscription a deux utilisations distinctes. Une utilisation permet de définir l’étendue sur un type de ressource de [module](modules.md#configure-module-scopes) ou [d’extension](scope-extension-resources.md). L’autre utilisation consiste à obtenir des détails sur l’abonnement actuel. Le positionnement de la fonction détermine son utilisation. Lorsqu’elle est utilisée pour définir la propriété `scope`, elle retourne un objet d’étendue.

`subscription(subscriptionId)` ne peut être utilisé que pour définir l’étendue.

`subscription()` peut être utilisé pour définir l’étendue ou obtenir les détails sur l’abonnement.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |Non |string |Identificateur unique de l’abonnement sur lequel effectuer le déploiement. Si vous ne fournissez pas de valeur, l’abonnement actuel est retourné. |

### <a name="return-value"></a>Valeur de retour

Lorsqu’elle est utilisée pour définir l’étendue, la fonction retourne un objet qui est valide pour la propriété `scope` sur un type de ressource de module ou d’extension.

Lorsqu’elle est utilisée pour obtenir des détails sur l’abonnement, la fonction retourne le format suivant :

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Exemple d’abonnement

L’exemple suivant définit l’étendue d’un module à l’abonnement.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

L’exemple suivant retourne les détails d’un abonnement.

```bicep
output subscriptionOutput object = subscription()
```

## <a name="tenant"></a>tenant

`tenant()`

Retourne un objet utilisé pour définir l’étendue au locataire.

### <a name="remarks"></a>Remarques

`tenant()` peut être utilisée pour toute étendue de déploiement. Elle retourne toujours le locataire actuel.

### <a name="return-value"></a>Valeur de retour

Objet utilisé pour définir la propriété `scope` sur un type de ressource de [module](modules.md#configure-module-scopes) ou [d’extension](scope-extension-resources.md).

### <a name="tenant-example"></a>Exemple de locataire

L’exemple suivant illustre un module déployé sur le locataire.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les étendues de déploiement, consultez :

* [Déploiements de groupes de ressources](deploy-to-resource-group.md)
* [Déploiements d’abonnements](deploy-to-subscription.md)
* [Déploiements de groupes d’administration](deploy-to-management-group.md)
* [Déploiements de locataires](deploy-to-tenant.md)
