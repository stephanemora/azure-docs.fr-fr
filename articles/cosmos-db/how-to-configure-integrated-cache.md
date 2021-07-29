---
title: Comment configurer le cache intégré Azure Cosmos DB
description: Découvrez comment configurer le cache intégré Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: tisande
ms.openlocfilehash: ddfdd4897a0cd194465828bba4bea0c002a4e434
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797667"
---
# <a name="how-to-configure-the-azure-cosmos-db-integrated-cache-preview"></a>Comment configurer le cache intégré Azure Cosmos DB (préversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cet article explique comment approvisionner une passerelle dédiée, configurer le cache intégré et connecter votre application. 

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas [d’abonnement Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- Une application existante utilisant Azure Cosmos DB. Si vous n’en avez pas, [voici quelques exemples](https://github.com/AzureCosmosDB/labs).
- Un [compte d’API SQL Azure Cosmos DB (core)](create-cosmosdb-resources-portal.md)existant.

## <a name="provision-a-dedicated-gateway-cluster"></a>Approvisionner un cluster de passerelle dédié

1. Accédez à un compte Azure Cosmos DB dans le portail Azure et sélectionnez l’onglet **Passerelle dédiée**.

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-tab.png" alt-text="Image montrant comment accéder à l’onglet Passerelle dédiée" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-tab.png" border="false":::

2. Remplissez le formulaire **Passerelle dédiée** avec les informations suivantes :

   * **Passerelle dédiée** : activez le bouton bascule sur **Approvisionné**. 
   * **Référence (SKU**) : sélectionnez une référence (SKU) offrant les tailles de calcul et de mémoire requises. 
   *  **Nombre d’instances** : nombre de nœuds. À des fins de développement, nous vous recommandons de commencer avec un nœud de la taille D4. En fonction de la quantité de données à mettre en cache, vous pouvez augmenter la taille du nœud après le test initial.

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-input.png" alt-text="Image montrant des exemples de paramètres d’entrée pour la création d’un cluster de passerelle dédiée" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-input.png" border="false":::

3. Sélectionnez **Enregistrer**, puis attendez environ de 5 à 10 minutes que l’approvisionnement de la passerelle dédiée se termine. Une fois l’approvisionnement terminé, la notification suivante s’affiche :

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-notification.png" alt-text="Image montrant comment vérifier si l’approvisionnement de passerelle dédiée est terminé" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-notification.png" border="false":::

## <a name="configuring-the-integrated-cache"></a>Configuration du cache intégré

1. Lorsque vous créez une passerelle dédiée, un cache intégré est automatiquement approvisionné. Le cache intégré utilise environ 70 % de la mémoire disponible dans la passerelle dédiée. Les 30 % de mémoire restants dans la passerelle dédiée sont utilisés pour router des demandes vers les partitions principales.

2.  Modifiez la chaîne de connexion de votre application pour utiliser le nouveau point de terminaison de passerelle dédiée.

      La chaîne de connexion à la passerelle dédiée mise à jour figure dans le panneau **Clés** :
   
      :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-connection-string.png" alt-text="Image montrant la chaîne de connexion à la passerelle dédiée" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-connection-string.png" border="false":::

      Toutes les chaînes de connexion à la passerelle dédiée suivent le même modèle. Supprimez `documents.azure.com` de la chaîne de connexion d’origine et entrez `sqlx.cosmos.azure.com` à la place. Une passerelle dédiée aura toujours la même chaîne de connexion, même si vous la supprimez et la réapprovisionnez.

      Vous n’avez pas besoin de modifier la chaîne de connexion dans toutes les applications utilisant le même compte Azure Cosmos DB. Par exemple, vous pouvez avoir une connexion `CosmosClient` utilisant le mode passerelle et le point de terminaison de passerelle dédiée tandis qu’un autre `CosmosClient` utilise le mode direct. En d’autres termes, l’ajout d’une passerelle dédiée n’a pas d’impact sur les méthodes existantes de connexion à Azure Cosmos DB.

3. Si vous utilisez le Kit de développement logiciel (SDK) .NET ou Java, définissez le mode de connexion sur [mode passerelle](sql-sdk-connection-modes.md#available-connectivity-modes). Cette étape n’est pas nécessaire pour les kits de développement logiciel (SDK) Python et Node.js, car ils n’ont pas d’options de connexion autres que le mode passerelle.

## <a name="adjust-request-consistency"></a>Ajuster la cohérence des demandes

Vous devez ajuster la cohérence des demandes en optant pour une cohérence éventuelle. Si ce n’est pas le cas, la demande contourne toujours le cache intégré. Le moyen le plus simple de configurer une cohérence éventuelle pour toutes les opérations de lecture est de [la définir au niveau du compte](consistency-levels.md#configure-the-default-consistency-level). Vous pouvez également configurer la cohérence au [niveau de la demande](how-to-manage-consistency.md#override-the-default-consistency-level), ce qui est recommandé si vous souhaitez uniquement qu’un sous-ensemble de vos lectures utilise le cache intégré.

> [!NOTE]
> Si vous utilisez le Kit de développement logiciel (SDK) Python, vous **devez** définir explicitement le niveau de cohérence pour chaque demande. Le paramètre par défaut au niveau du compte ne s’applique pas automatiquement.

## <a name="adjust-maxintegratedcachestaleness"></a>Ajuster MaxIntegratedCacheStaleness

Configurez `MaxIntegratedCacheStaleness`, qui correspond à la durée maximale pendant laquelle vous souhaitez tolérer les données mises en cache obsolètes. Nous vous recommandons de définir la valeur `MaxIntegratedCacheStaleness` la plus élevée possible, car cela augmente la probabilité que les lectures et les requêtes de point répétées puissent être des correspondances de cache. Si vous affectez à `MaxIntegratedCacheStaleness` la valeur 0, votre requête de lecture n’utilisera **jamais** le cache intégré, quel que soit le niveau de cohérence. En l’absence de configuration, la valeur par défaut de `MaxIntegratedCacheStaleness` est 5 minutes.

**.NET**

```csharp
FeedIterator<Food> myQuery = container.GetItemQueryIterator<Food>(new QueryDefinition("SELECT * FROM c"), requestOptions: new QueryRequestOptions
        {
            ConsistencyLevel = ConsistencyLevel.Eventual,
            DedicatedGatewayRequestOptions = new DedicatedGatewayRequestOptions 
            { 
                MaxIntegratedCacheStaleness = TimeSpan.FromMinutes(30) 
            }
        }
);
```

> [!NOTE]
> Actuellement, vous pouvez uniquement ajuster MaxIntegratedCacheStaleness à l’aide de la dernière préversion du kit de développement logiciel .NET et de Java.

## <a name="verify-cache-hits"></a>Vérifier les correspondances dans le cache

Enfin, vous pouvez redémarrer votre application et vérifier les correspondances dans le cache intégré pour des lectures ou requêtes de point répétées. Une fois votre `CosmosClient` modifié pour utiliser le point de terminaison de passerelle dédiée, toutes les demandes sont routées via la passerelle dédiée.

Pour qu’une demande de lecture (lecture ou interrogation de point) utilise le cache intégré, **tous** les critères suivants doivent être vrais :

-   Votre client se connecte au point de terminaison de passerelle dédiée
-  Votre client utilise le mode passerelle (Les kits de développement logiciel (SDK) Python et Node.js utilisent toujours le mode passerelle)
-   La cohérence de la demande doit être définie sur éventuelle.

## <a name="next-steps"></a>Étapes suivantes

- [FAQ sur le cache intégré](integrated-cache-faq.md)
- [Vue d’ensemble du cache intégré](integrated-cache.md)
- [Passerelle dédiée](dedicated-gateway.md)
