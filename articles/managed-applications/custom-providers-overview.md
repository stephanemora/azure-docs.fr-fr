---
title: Vue d’ensemble de fournisseurs personnalisés Azure Preview
description: Décrit les concepts de création d’un fournisseur de ressources personnalisé avec Azure Resource Manager
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159854"
---
# <a name="azure-custom-providers-preview-overview"></a>Vue d’ensemble de Azure en version préliminaire des fournisseurs personnalisés

Avec des fournisseurs Azure personnalisée, vous pouvez étendre Azure fonctionne avec votre service. Vous créez votre propre fournisseur de ressources, notamment les types de ressources personnalisées et les actions. Le fournisseur personnalisé est intégré avec Azure Resource Manager. Vous pouvez utiliser les fonctionnalités du Gestionnaire de ressources, telles que les déploiements de modèle et de contrôle d’accès en fonction du rôle, à déployer et à sécuriser votre service.

Cet article fournit une vue d’ensemble de fournisseurs personnalisés et de ses fonctionnalités. L’illustration suivante montre le flux de travail pour les ressources et actions définis dans un fournisseur personnalisé.

![Vue d’ensemble du fournisseur personnalisé](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Fournisseurs personnalisés est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Définir votre fournisseur personnalisé

Vous commencez par informer Azure Resource Manager de votre fournisseur personnalisé. Déployer une ressource fournisseur personnalisé, qui utilise le type de ressource dans Azure **Microsoft.CustomProviders/resourceProviders**. Dans cette ressource, vous définissez les ressources et les actions de votre service.

Par exemple, si votre service a besoin d’un type de ressource nommé **utilisateurs**, vous incluez ce type de ressource dans votre définition de fournisseur personnalisé. Pour chaque type de ressource, vous fournir un point de terminaison qui offre les opérations REST (PUT, GET, DELETE) pour ce type de ressource. Le point de terminaison peut être hébergée sur n’importe quel environnement, et il contient la logique de la façon dont votre service gère les opérations sur le type de ressource.

Vous pouvez également définir des actions personnalisées pour votre fournisseur de ressources. Actions représentent des opérations POST. Utiliser des actions pour les opérations telles que de démarrer, arrêter ou redémarrer. Vous fournir un point de terminaison qui gère la demande.

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

Pour **routingType**, les valeurs acceptées sont `Proxy` et `Cache`. Proxy signifie que les demandes pour le type de ressource ou action sont gérés par le point de terminaison. Le paramètre de cache est uniquement pris en charge pour les types de ressources, pas les actions. Pour spécifier le cache, vous devez également spécifier le proxy. Cache signifie que les réponses du point de terminaison sont stockées pour optimiser les opérations de lecture. Le paramètre de cache rend plus facile à implémenter une API qui est cohérent et conforme avec d’autres services de gestionnaire de ressources.

## <a name="deploy-your-resource-types"></a>Déployer vos types de ressources

Après avoir défini votre fournisseur personnalisé, vous pouvez déployer vos types de ressources personnalisées. L’exemple suivant montre le code JSON que vous incluez dans votre modèle pour déployer le type de ressource pour votre fournisseur personnalisé. Ce type de ressource peut être déployé dans le même modèle avec d’autres ressources Azure.

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

Utiliser Azure [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) pour gérer l’accès à votre fournisseur de ressources. Vous pouvez affecter [rôles intégrés](../role-based-access-control/built-in-roles.md) tels que le propriétaire, collaborateur ou lecteur aux utilisateurs. Vous pouvez également définir [des rôles personnalisés](../role-based-access-control/custom-roles.md) qui sont spécifiques aux opérations dans votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à des fournisseurs personnalisés. Accédez à l’article suivant pour créer un fournisseur personnalisé.

> [!div class="nextstepaction"]
> [Tutoriel : Fournisseur personnalisé de créer et déployer des ressources personnalisées](create-custom-provider.md)
