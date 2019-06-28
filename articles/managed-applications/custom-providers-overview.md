---
title: Vue d’ensemble des fournisseurs personnalisés Azure en préversion
description: Décrit les concepts nécessaires à la création d’un fournisseur de ressources personnalisé avec Azure Resource Manager.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159854"
---
# <a name="azure-custom-providers-preview-overview"></a>Vue d’ensemble des fournisseurs personnalisés Azure en préversion

Les fournisseurs Azure personnalisés permettent d’étendre Azure de façon à utiliser n’importe quel service. Vous créez votre propre fournisseur de ressources, avec des actions et des types de ressources personnalisés. Ce fournisseur personnalisé est intégré à Azure Resource Manager. Vous pouvez utiliser les fonctionnalités de Resource Manager, notamment les déploiements de modèles et le contrôle d’accès en fonction du rôle, pour déployer et sécuriser votre service.

Cet article donne une vue d’ensemble des fournisseurs personnalisés et de leurs fonctionnalités. L’illustration suivante montre le flux de travail des ressources et des actions définies dans un fournisseur personnalisé.

![Vue d’ensemble du fournisseur personnalisé](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Les fournisseurs personnalisés sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Définir un fournisseur personnalisé

Commencez par informer Azure Resource Manager de l’existence de votre fournisseur personnalisé. Vous déployez sur Azure une ressource de fournisseur personnalisé de type **Microsoft.CustomProviders/resourceProviders**. Dans cette ressource, vous définissez les ressources et les actions de votre service.

Si, par exemple, votre service a besoin d’un type de ressource nommé **users**, incluez-le dans la définition de votre fournisseur personnalisé. Pour chaque type de ressource, indiquez un point de terminaison qui propose les opérations REST (PUT, GET, DELETE) associées. Il peut être hébergé dans n’importe quel environnement, et contient la logique de gestion des opérations appliquée par le service sur le type de ressource.

Vous pouvez également définir des actions personnalisées pour votre fournisseur de ressources. Les actions représentent des opérations POST. Utilisez des actions pour des opérations comme démarrer, arrêter ou redémarrer. Vous fournissez un point de terminaison qui gère la demande.

L’exemple suivant montre comment définir un fournisseur personnalisé avec une action et un type de ressource.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

Les valeurs acceptées pour **routingType** sont `Proxy` et `Cache`. Avec la valeur Proxy, les demandes de type de ressource ou d’action sont gérées par le point de terminaison. Le paramètre de cache n’est pris en charge que pour les types de ressources, et non pour les actions. Spécifier un cache suppose d’indiquer également un proxy. Avec la valeur Cache, les réponses du point de terminaison sont stockées pour optimiser les opérations de lecture. Le paramètre de cache permet d’implémenter une API cohérente et conforme avec d’autres services Resource Manager.

## <a name="deploy-your-resource-types"></a>Déployer les types de ressources

Maintenant que vous avez défini votre fournisseur personnalisé, vous pouvez déployer vos types de ressources personnalisés. L’exemple suivant montre le code JSON à inclure dans le modèle pour déployer le type de ressource du fournisseur personnalisé. Ce type de ressource peut être déployé dans le même modèle que d’autres ressources Azure.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Gérer l’accès

[Utiliser le contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) pour gérer l’accès à votre fournisseur de ressources. Il est possible d’affecter des [rôles intégrés](../role-based-access-control/built-in-roles.md) (par exemple, Propriétaire, Collaborateur ou Lecteur) aux utilisateurs. Vous pouvez également définir des [rôles personnalisés](../role-based-access-control/custom-roles.md) propres aux opérations dans votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les fournisseurs personnalisés. Passez au suivant pour créer un fournisseur personnalisé.

> [!div class="nextstepaction"]
> [Tutoriel : Créer un fournisseur personnalisé et déployer des ressources personnalisées](create-custom-provider.md)
