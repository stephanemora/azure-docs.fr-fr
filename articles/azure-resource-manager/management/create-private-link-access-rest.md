---
title: Gérer des ressources via une liaison privée
description: Restreindre l’accès de gestion pour une ressource à une liaison privée
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: eb7e81ef739fdb94bc91c65b079d5b76b22636c9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227373"
---
# <a name="use-rest-api-to-create-private-link-for-managing-azure-resources"></a>Utiliser une API REST pour créer une liaison privée pour la gestion de ressources Azure

Cet article explique comment utiliser [Azure Private Link](../../private-link/index.yml) afin de limiter l’accès pour la gestion des ressources dans vos abonnements.

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>Créer une liaison privée de gestion des ressources

Pour créer une liaison privée de gestion des ressources, envoyez la demande suivante :

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

Dans le corps de la demande, incluez l’emplacement souhaité pour la ressource :

```json
{
  "location":"{region}"
}
```

L’opération retourne :

```json
{  
  "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
  "location": "{region}",
  "name": "{rmplName}",
  "properties": {
    "privateEndpointConnections": []
  },
  "resourceGroup": "{rgName}",
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks"
}
```

Notez l’ID renvoyé pour la nouvelle liaison privée de gestion des ressources. Vous allez l’utiliser pour créer l’association de liaison privée.

## <a name="create-private-link-association"></a>Créer une association de liaison privée

Pour créer l’association de liaison privée, utilisez :

```http
PUT
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupId}/providers/Microsoft.Authorization/privateLinkAssociations/{GUID}?api-version=2020-05-01 
```

Dans le corps de la demande, spécifiez :

```json
{
  "properties": {
    "privateLink": "/subscriptions/{subscription-id}/resourceGroups/{rg-name}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}",
    "publicNetworkAccess": "enabled"
  }
}
```

L’opération retourne :

```json
{
  "id": {plaResourceId},
  "name": {plaName},
  "properties": {
    "privateLink": {rmplResourceId},
    "publicNetworkAccess": "Enabled",
    "tenantId": "{tenantId}",
    "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
  },
  "type": "Microsoft.Authorization/privateLinkAssociations"
}
```

## <a name="add-private-endpoint"></a>Ajouter un point de terminaison privé

Cet article part du principe que vous disposez déjà d’un réseau virtuel. Dans le sous-réseau qui sera utilisé pour le point de terminaison privé, vous devez désactiver les stratégies réseau de point de terminaison privé. Si vous n’avez pas désactivé les stratégies réseau de point de terminaison privé, consultez [Désactiver les stratégies réseau pour les points de terminaison privés](../../private-link/disable-private-endpoint-network-policy.md).

Pour créer un point de terminaison privé, utilisez l’opération suivante :

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/privateEndpoints/{privateEndpointName}?api-version=2020-11-01
```

Dans le corps de la demande, définissez l’ID `privateServiceLinkId` sur l’ID de la liaison privée de gestion des ressources. Les `groupIds` doit contenir `ResourceManagement`. L’emplacement du point de terminaison privé doit être identique à celui du sous-réseau.

```json
{
  "location": "westus2",
  "properties": {
    "privateLinkServiceConnections": [
      {
        "name": "{connection-name}",
        "properties": {
           "privateLinkServiceId": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
           "groupIds": [
              "ResourceManagement"
           ]
         }
      }
    ],
    "subnet": {
      "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
    }
  }
}
```

L’étape suivante varie selon que vous utilisez une approbation manuelle ou automatique. Pour plus d’informations sur l’approbation, consultez [Accès à une ressource Private Link à l’aide du flux de travail d’approbation](../../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

La réponse comprend l’état d’approbation.

```json
"privateLinkServiceConnectionState": {
    "actionsRequired": "None",
    "description": "",
    "status": "Approved"
},
```

Si votre demande est approuvée automatiquement, vous pouvez passer à la section suivante. Si votre demande nécessite une approbation manuelle, attendez que l’administrateur réseau approuve votre connexion de point de terminaison privé.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les liaisons privées, consultez [Azure Private Link](../../private-link/index.yml).