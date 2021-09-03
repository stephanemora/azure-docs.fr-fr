---
title: Autorisations du contrôle d’accès en fonction du rôle Azure pour Azure Private Link
description: Commencez à vous familiariser avec les autorisations du contrôle d’accès en fonction du rôle Azure nécessaires pour déployer un point de terminaison privé et un service de liaison privée.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 5/25/2021
ms.custom: template-concept
ms.openlocfilehash: 50b648c5419b995f9c808288895af49b12f29b86
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533129"
---
# <a name="azure-rbac-permissions-for-azure-private-link"></a>Autorisations du contrôle d’accès en fonction du rôle Azure pour Azure Private Link

Il est vital pour toute organisation de pouvoir gérer les accès aux ressources situées dans cloud. Le contrôle d’accès en fonction du rôle Azure (Azure RBAC) gère l’accès et les opérations aux ressources Azure.

Pour déployer un point de terminaison privé ou un service de liaison privée, un utilisateur doit avoir attribuer un rôle intégré, tel que : 

* [Propriétaire](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#owner)
* [Contributeur](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#contributor)
* [Contributeur de réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#network-contributor)

Vous pouvez fournir un accès plus granulaire en créant un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json) avec les autorisations décrites dans les sections suivantes.

> [!IMPORTANT]
> Cet article répertorie les autorisations spécifiques pour créer un point de terminaison privé ou un service de liaison privée. Veillez à ajouter les autorisations spécifiques associées au service auquel vous souhaitez accorder l’accès par le biais d’une liaison privée, telle que le rôle Contributeur Microsoft SQL pour Azure SQL. Pour plus d’informations sur les rôles intégrés, consultez [Contrôle d’accès en fonction du rôle](../role-based-access-control/built-in-roles.md).

Microsoft.Network et le fournisseur de ressources spécifique que vous déployez, par exemple Microsoft.Sql, doivent être inscrits au niveau de l’abonnement :

![image](https://user-images.githubusercontent.com/20302679/129105527-b946eee9-038a-46ef-b446-be371eb23ca9.png)

## <a name="private-endpoint"></a>Point de terminaison privé

Cette section répertorie les autorisations granulaires requises pour déployer un point de terminaison privé.

| Action                                                              | Description                                                                      |
| ---------                                                           | -------------                                                                 |
| Microsoft.Resources/deployments/*                                   | Créer et gérer un déploiement                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | Lire les ressources pour le groupe de ressources                                     |
| Microsoft.Network/virtualNetworks/read                              | Lire la définition de réseau virtuel                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | Lire la définition de sous-réseau de réseau virtuel                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | Crée un sous-réseau de réseau virtuel ou met à jour un sous-réseau de réseau virtuel existant.|
| Microsoft.Network/virtualNetworks/subnets/join/action               | Joint un réseau virtuel.                                                       |
| Microsoft.Network/privateEndpoints/read                             | Lire la ressource d’un point de terminaison privé                                             |
| Microsoft.Network/privateEndpoints/write                            | Crée un nouveau point de terminaison privé, ou met à jour un point de terminaison privé existant       |
| Microsoft.Network/locations/availablePrivateEndpointTypes/read      | Lire les ressources de point de terminaison privé disponibles                                     |

Voici le format JSON des autorisations ci-dessus. Entrez votre propre roleName, description et assignableScopes :

```JSON
{
 "properties": {
   "roleName": "Role Name",
   "description": "Description",
   "assignableScopes": [
     "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
   ],
   "permissions": [
     {
       "actions": [
         "Microsoft.Resources/deployments/*",
         "Microsoft.Resources/subscriptions/resourceGroups/read",
         "Microsoft.Network/virtualNetworks/read",
         "Microsoft.Network/virtualNetworks/subnets/read",
         "Microsoft.Network/virtualNetworks/subnets/write",
         "Microsoft.Network/virtualNetworks/subnets/join/action",
         "Microsoft.Network/privateEndpoints/read",
         "Microsoft.Network/privateEndpoints/write",
         "Microsoft.Network/locations/availablePrivateEndpointTypes/read"
       ],
       "notActions": [],
       "dataActions": [],
       "notDataActions": []
     }
   ]
 }
}
```

## <a name="private-link-service"></a>Service de liaison privée

Cette section répertorie les autorisations granulaires requises pour déployer un service de liaison privée.

| Action | Description   |
| --------- | ------------- |
| Microsoft.Resources/deployments/*                                   | Créer et gérer un déploiement                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | Lire les ressources pour le groupe de ressources                                     |
| Microsoft.Network/virtualNetworks/read                              | Lire la définition de réseau virtuel                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | Lire la définition de sous-réseau de réseau virtuel                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | Crée un sous-réseau de réseau virtuel ou met à jour un sous-réseau de réseau virtuel existant.|
| Microsoft.Network/privateLinkServices/read | Lire une ressource de service de liaison privée|
| Microsoft.Network/privateLinkServices/write | Crée un service de liaison privée, ou met à jour un service de liaison privée existant|
| Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Lire une définition de connexion du point de terminaison privé |
| Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Crée une connexion du point de terminaison privé ou met à jour une connexion du point de terminaison privé existante|
| Microsoft.Network/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. |
| Microsoft.Network/loadBalancers/read | Lire une définition de l’équilibreur de charge |
| Microsoft.Network/loadBalancers/write | Crée un équilibrage de charge ou met à jour un équilibrage de charge existant. |

```JSON
{
  "properties": {
    "roleName": "Role Name",
    "description": "Description",
    "assignableScopes": [
      "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/write",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/privateLinkServices/read",
          "Microsoft.Network/privateLinkServices/write",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/read",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/write",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Network/loadBalancers/read",
          "Microsoft.Network/loadBalancers/write"
        ],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le point de terminaison privé et les services de liaison privée dans Azure Private Link, consultez :

- [Qu’est-ce qu’Azure Private Endpoint ?](private-endpoint-overview.md)
- [Qu’est-ce que le service Azure Private Link ?](private-link-service-overview.md)
