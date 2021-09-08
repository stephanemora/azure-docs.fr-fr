---
title: Gérer les liaisons privées de gestion des ressources
description: Utiliser l’API REST pour gérer les liaisons privées de gestion des ressources existantes
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 376fa600cce4479a0ed2c04e9d3d7b3fbeb9aeaa
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227375"
---
# <a name="manage-resource-management-private-links-with-rest-api"></a>Gérer les liaisons privées de gestion des ressources avec l’API REST

Cet article explique comment utiliser les liaisons privée sde gestion des ressources existantes. Il montre les opérations de l’API REST pour l’obtention et la suppression des ressources existantes.

Si vous avez besoin de créer une liaison privée de gestion des ressources, consultez [Utiliser le portail pour créer un lien privé pour la gestion des ressources Azure](create-private-link-access-portal.md) ou [Utilisez l’API REST pour créer une liaison privée pour gérer les ressources Azure](create-private-link-access-rest.md).

## <a name="resource-management-private-links"></a>Liaisons privées de gestion des ressources

Pour **obtenir une** liaison privée de gestion des ressources spécifique, envoyez la requête suivante :

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01 
```

L’opération retourne :

```json
{
  "properties": {
    "privateEndpointConnections": []
  },
  "id": {rmplResourceId},
  "name": {rmplName},
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
  "location": {region}
}
```

Pour **obtenir toutes les** liaisons privées de gestion des ressources dans un abonnement, utilisez :

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.Authorization/resourceManagementPrivateLinks?api-version=2020-05-01
```

L’opération retourne :

```json
[
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  },
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  }
]
```

Pour **supprimer une** liaison privée de gestion des ressources, utilisez :

```http
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

L’opération retourne : `Status 200 OK`.

## <a name="private-link-association"></a>Association de liaison privée

Pour **obtenir une** association de lien privée spécifique pour un groupe d’administration, utilisez :

```http
GET
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations?api-version=2020-05-01 
```

L’opération retourne :

```json
{
  "value": [
    {
      "properties": {
        "privateLink": {rmplResourceID},
        "tenantId": {tenantId},
        "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
      },
      "id": {plaResourceId},
      "type": "Microsoft.Authorization/privateLinkAssociations",
      "name": {plaName}
    }
  ]
}
```

Pour **supprimer** une association de liaison privée, utilisez :

```http
DELETE 
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations/{plaID}?api-version=2020-05-01
```

L’opération retourne : `Status 200 OK`.

## <a name="private-endpoints"></a>Instances Private Endpoint

Pour **obtenir tous les** points de terminaison privés dans un abonnement, utilisez :

```http
GET 
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/privateEndpoints?api-version=2020-04-01
```

L’opération retourne :

```json
{
  "value": [
    {
      "name": {privateEndpointName},
      "id": {privateEndpointResourceId},
      "etag": {etag},
      "type": "Microsoft.Network/privateEndpoints",
      "location": {region},
      "properties": {
        "provisioningState": "Updating",
        "resourceGuid": {GUID},
        "privateLinkServiceConnections": [
          {
            "name": {connectionName},
            "id": {connectionResourceId},
            "etag": {etag},
            "properties": {
              "provisioningState": "Succeeded",
              "privateLinkServiceId": {rmplResourceId},
              "groupIds": [
                "ResourceManagement"
              ],
              "privateLinkServiceConnectionState": {
                "status": "Approved",
                "description": "",
                "actionsRequired": "None"
              }
            },
            "type": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections"
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": {subnetResourceId}
        },
        "networkInterfaces": [
          {
            "id": {networkInterfaceResourceId}
          }
        ],
        "customDnsConfigs": [
          {
            "fqdn": "management.azure.com",
            "ipAddresses": [
              "10.0.0.4"
            ]
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les liaisons privées, consultez l’article [Azure Private Link](../../private-link/index.yml).
* Si vous avez besoin de créer une liaison privée de gestion des ressources, consultez [Utiliser le portail pour créer un lien privé pour la gestion des ressources Azure](create-private-link-access-portal.md) ou [Utilisez l’API REST pour créer une liaison privée pour gérer les ressources Azure](create-private-link-access-rest.md).