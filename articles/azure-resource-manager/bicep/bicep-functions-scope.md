---
title: Fonctions Bicep - étendues
description: Décrit les fonctions à utiliser dans un fichier Bicep pour récupérer les valeurs relatives aux étendues de déploiement.
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 809d3b2097e530f92370374be5f15c39bbd70734
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178338"
---
# <a name="scope-functions-for-bicep"></a>Fonctions d’étendue pour Bicep

Cet article décrit les fonctions Bicep pour l’obtention des valeurs d’étendue.

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(identifier)`

Retourne un objet utilisé pour définir l’étendue à un groupe d’administration.

ou

Retourne un objet avec les propriétés du groupe d’administration dans le déploiement actuel.

Espace de noms : [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Remarques

`managementGroup()` ne peut être utilisée que sur des [déploiements de groupe d’administration](deploy-to-management-group.md). Elle retourne le groupe d’administration actuel pour l’opération de déploiement. À utiliser pour obtenir un objet d’étendue ou des propriétés pour le groupe d’administration actuel.

`managementGroup(identifier)` peut être utilisé pour n’importe quelle étendue de déploiement, mais uniquement pour obtenir l’objet d’étendue. Pour récupérer les propriétés d’un groupe d’administration, vous ne pouvez pas fournir l’identificateur du groupe d’administration.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| identificateur |Non |string |Identificateur unique du groupe d’administration sur lequel effectuer le déploiement. N’utilisez pas le nom complet du groupe d’administration. Si vous ne fournissez pas de valeur, le groupe d’administration actuel est retourné. |

### <a name="return-value"></a>Valeur de retour

Objet utilisé pour définir la propriété `scope` sur un type de ressource de [module](modules.md#set-module-scope) ou [d’extension](scope-extension-resources.md). Ou un objet avec les propriétés du groupe d’administration actuel.

### <a name="management-group-example"></a>Exemple de groupe d’administration

L’exemple suivant définit l’étendue d’un module sur un groupe d’administration.

```bicep
param managementGroupIdentifier string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupIdentifier)
}
```

L’exemple suivant renvoie les propriétés du groupe d’administration actuel.

```bicep
targetScope = 'managementGroup'

var mgInfo = managementGroup()

output mgResult object = mgInfo
```

Cela renvoie :

```json
"mgResult": {
  "type": "Object",
  "value": {
    "id": "/providers/Microsoft.Management/managementGroups/examplemg1",
    "name": "examplemg1",
    "properties": {
      "details": {
        "parent": {
          "displayName": "Tenant Root Group",
          "id": "/providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000",
          "name": "00000000-0000-0000-0000-000000000000"
        },
        "updatedBy": "00000000-0000-0000-0000-000000000000",
        "updatedTime": "2020-07-23T21:05:52.661306Z",
        "version": "1"
      },
      "displayName": "Example MG 1",
      "tenantId": "00000000-0000-0000-0000-000000000000"
    },
    "type": "/providers/Microsoft.Management/managementGroups"
  }
}
```

L’exemple suivant crée un nouveau groupe d’administration et utilise cette fonction pour définir le groupe d’administration parent.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: managementGroup().id
      }
    }
  }
}

output newManagementGroup string = mgName
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

`resourceGroup(resourceGroupName)`

`resourceGroup(subscriptionId, resourceGroupName)`

Retourne un objet utilisé pour définir l’étendue à un groupe de ressources.

ou

Renvoie un objet qui représente le groupe de ressources actuel.

Espace de noms : [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Remarques

La fonction resourceGroup a deux utilisations distinctes. Une utilisation permet de définir l’étendue sur un type de ressource de [module](modules.md#set-module-scope) ou [d’extension](scope-extension-resources.md). L’autre utilisation consiste à obtenir des détails sur le groupe de ressources actuel. Le positionnement de la fonction détermine son utilisation. Lorsqu’elle est utilisée pour définir la propriété `scope`, elle retourne un objet d’étendue.

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

Espace de noms : [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Remarques

La fonction subscription a deux utilisations distinctes. Une utilisation permet de définir l’étendue sur un type de ressource de [module](modules.md#set-module-scope) ou [d’extension](scope-extension-resources.md). L’autre utilisation consiste à obtenir des détails sur l’abonnement actuel. Le positionnement de la fonction détermine son utilisation. Lorsqu’elle est utilisée pour définir la propriété `scope`, elle retourne un objet d’étendue.

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

ou

Retourne les propriétés du locataire pour le déploiement actuel.

Espace de noms : [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Remarques

`tenant()` peut être utilisée pour toute étendue de déploiement. Elle retourne toujours le locataire actuel. Vous pouvez utiliser cette fonction pour définir l’étendue d’une ressource ou pour obtenir les propriétés du locataire actuel.

### <a name="return-value"></a>Valeur de retour

Objet utilisé pour définir la propriété `scope` sur un type de ressource de [module](modules.md#set-module-scope) ou [d’extension](scope-extension-resources.md). Ou un objet avec les propriétés du locataire actuel.

### <a name="tenant-example"></a>Exemple de locataire

L’exemple suivant illustre un module déployé sur le locataire.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

L’exemple suivant renvoie les propriétés d’un locataire.

```bicep
var tenantInfo = tenant()

output tenantResult object = tenantInfo
```

Cela renvoie :

```json
"tenantResult": {
  "type": "Object",
  "value": {
    "countryCode": "US",
    "displayName": "Contoso",
    "id": "/tenants/00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000"
  }
}
```

Certaines ressources nécessitent la définition de l’ID de locataire pour une propriété. Plutôt que de fournir l’ID de locataire en tant que paramètre, vous pouvez le récupérer avec la fonction tenant.

```bicep
resource kv 'Microsoft.KeyVault/vaults@2021-06-01-preview' = {
  name: 'examplekeyvault'
  location: 'westus'
  properties: {
    tenantId: tenant().tenantId
    ...
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les étendues de déploiement, consultez :

* [Déploiements de groupes de ressources](deploy-to-resource-group.md)
* [Déploiements d’abonnements](deploy-to-subscription.md)
* [Déploiements de groupes d’administration](deploy-to-management-group.md)
* [Déploiements de locataires](deploy-to-tenant.md)
