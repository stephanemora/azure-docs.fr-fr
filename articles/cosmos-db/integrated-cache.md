---
title: Cache intégré Azure Cosmos DB
description: Le cache intégré Azure Cosmos DB est un cache en mémoire qui vous permet de garantir des coûts gérables et une faible latence à mesure que le volume de vos requêtes augmente.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: tisande
ms.openlocfilehash: f0a0556ce2a46f922e387d96d20b6425ab362580
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384862"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Cache intégré Azure Cosmos DB - Vue d’ensemble (préversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le cache intégré Azure Cosmos DB est un cache en mémoire qui vous permet de garantir des coûts gérables et une faible latence à mesure que le volume de vos requêtes augmente. Le cache intégré est facile à configurer et vous n’avez pas besoin de passer du temps à écrire du code personnalisé pour l’invalidation du cache ou la gestion de l’infrastructure principale. Votre cache intégré utilise une [passerelle dédiée](dedicated-gateway.md) au sein de votre compte Azure Cosmos DB. Le cache intégré est le premier des nombreuses fonctionnalités Azure Cosmos DB qui utiliseront une passerelle dédiée pour améliorer les performances. Vous pouvez choisir parmi trois tailles de passerelle dédiées possibles en fonction du nombre de cœurs et de la mémoire nécessaires à votre charge de travail.

Un cache intégré est automatiquement configuré dans la passerelle dédiée. Le cache intégré comporte deux parties : 

* Un cache d’éléments pour les lectures de point 
* Un cache de requêtes pour les requêtes

Le cache intégré est un cache de lecture-écriture, accessible en écriture, avec une stratégie d’éviction Le moins récemment utilisé (LRU). Le cache d’éléments et le cache de requêtes partagent la même capacité dans le cache intégré et la stratégie d’éviction LRU s’applique aux deux. En d’autres termes, les données sont supprimées du cache de façon stricte en fonction du moment où elles ont été utilisées le moins récemment, qu’il s’agisse d’un point de lecture ou d’une requête.

## <a name="workloads-that-benefit-from-the-integrated-cache"></a>Charges de travail qui tirent parti du cache intégré

L’objectif principal du cache intégré est de réduire les coûts liés aux charges de travail lourdes en lecture. Une faible latence, bien qu’utile, n’est pas l’avantage principal du cache intégré, car Azure Cosmos DB est déjà rapide sans mise en cache.

Les lectures de point et les requêtes qui ont une correspondance dans le cache intégré n’utilisent pas de RU. En d’autres termes, les présences dans le cache ont un coût en RU de 0. Les correspondances dans le cache présentent un coût par opération nettement inférieur à celui des lectures de la base de données principale.

Pour les charges de travail qui correspondent aux caractéristiques suivantes, vous devez évaluer si le cache intégré permet de réduire les coûts :

-   Charges de travail nécessitant beaucoup de lectures
-   Nombreuses lectures de point répétées sur des éléments volumineux
-   Nombreuses requêtes à RU élevées répétitives
-   Clé de partition à chaud pour les lectures

Le plus grand facteur d’économie attendu est la mesure dans laquelle les lectures se répètent. Si votre charge de travail exécute régulièrement les mêmes lectures de point ou requêtes dans un laps de temps réduit, il s’agit d’un bon candidat pour le cache intégré. Lorsque vous utilisez le cache intégré pour des lectures répétées, vous utilisez uniquement les RU pour la première lecture. Les lectures suivantes acheminées via le même nœud dédié de passerelle (dans la fenêtre de `MaxIntegratedCacheStaleness` et à condition que les données n’ont pas été supprimées) n’utilisent pas le débit.

Certaines charges de travail ne doivent pas prendre en compte le cache intégré, notamment :

-   Charges de travail nécessitant beaucoup d’écritures
-  Lectures de point ou requêtes rarement répétées

## <a name="item-cache"></a>Cache d’éléments

Vous pouvez utiliser le cache d’éléments pour les lectures de point (en d’autres termes, les recherches de clé/valeur basées sur l’ID d’élément et la clé de partition).

### <a name="populating-the-item-cache"></a>Remplissage du cache d’éléments

- Les nouvelles écritures, mises à jour et suppressions sont automatiquement renseignées dans le cache d’éléments
- Si votre application tente de lire un élément spécifique qui n’était pas précédemment dans le cache (absence dans le cache), l’élément est maintenant stocké dans le cache d’éléments

### <a name="item-cache-invalidation-and-eviction"></a>Invalidation et éviction du cache d’éléments

- Mise à jour ou suppression d’élément
- Dernier récemment utilisé (LRU)
- Durée de rétention du cache (en d’autres termes, `MaxIntegratedCacheStaleness`)

## <a name="query-cache"></a>Cache de requête

Le cache de requêtes peut être utilisé pour mettre en cache des requêtes. Le cache de requêtes transforme une requête en une recherche de clé/valeur où la clé est le texte de la requête et la valeur est le résultat de la requête. Le cache intégré ne dispose pas d’un moteur de requête, il stocke uniquement la recherche de clé/valeur pour chaque requête.

### <a name="populating-the-query-cache"></a>Remplissage du cache de requêtes

- Si le cache n’a pas de résultat pour cette requête (absence dans le cache), la requête est envoyée au principal. Une fois la requête exécutée, le cache stocke les résultats de cette requête

### <a name="query-cache-eviction"></a>Éviction du cache de requêtes

- Dernier récemment utilisé (LRU)
- Durée de rétention du cache (en d’autres termes, `MaxIntegratedCacheStaleness`)

### <a name="working-with-the-query-cache"></a>Utilisation du cache de requêtes

Vous n’avez pas besoin de code spécial lorsque vous travaillez avec le cache de requêtes, même si vos requêtes comportent plusieurs pages de résultats. Les meilleures pratiques et le code pour la pagination de requête sont les mêmes, que votre requête accède au cache intégré ou s’exécute sur le moteur de requête principal.

Le cache de requêtes met automatiquement en cache les jetons de continuation de requête, le cas échéant. Si vous avez une requête avec plusieurs pages de résultats, les pages qui sont stockées dans le cache intégré ont une charge de 0 RU. Si vous avez d’autres pages de résultats de requête qui requièrent l’exécution du principal, elles disposeront d’un jeton de continuation de la page précédente afin d’éviter de dupliquer le travail précédent.

> [!NOTE]
> Les instances de cache intégrées dans différents nœuds de passerelle dédiés ont des caches indépendants les uns des autres. Si les données sont mises en cache dans un nœud, elles ne sont pas nécessairement mises en cache dans les autres.

## <a name="integrated-cache-consistency"></a>Cohérence du cache intégré

Le cache intégré prend uniquement en charge la [cohérence](consistency-levels.md) finale. Si une lecture a constamment un préfixe, une session, une obsolescence limitée ou une cohérence forte, elle contourne toujours le cache intégré.

Le moyen le plus simple de configurer la cohérence finale pour toutes les lectures est de la [définir au niveau du compte](consistency-levels.md#configure-the-default-consistency-level). Toutefois, si vous souhaitez que certaines de vos lectures aient une cohérence finale, vous pouvez également configurer la cohérence au [niveau de la requête](how-to-manage-consistency.md#override-the-default-consistency-level).

## <a name="integrated-cache-retention-time"></a>Durée de rétention du cache intégré

La durée de rétention du cache est la rétention maximale pour les données mises en cache. Pendant la préversion, `MaxIntegratedCacheStaleness` est toujours défini sur 5 minutes et n’est pas personnalisable.

## <a name="metrics"></a>Mesures

Lorsque vous utilisez le cache intégré, il est utile de surveiller quelques mesures clés. Les métriques de cache intégrées sont les suivantes :

- `DedicatedGatewayCpuUsage` - Utilisation du processeur par chaque nœud de passerelle dédié
- `DedicatedGatewayMemoryUsage` - Utilisation de la mémoire par chaque nœud de passerelle dédié pour les requêtes de routage et la mise en cache
- `DedicatedGatewayRequests` - Le nombre de requêtes routées via chaque nœud de passerelle dédié
- `IntegratedCacheEvictedEntriesSize` - La quantité de données supprimées du cache intégré
- `IntegratedCacheTTLExpirationCount` -Le nombre d’entrées supprimées du cache intégré, spécifiquement en raison des données mises en cache qui dépassent le temps `MaxIntegratedCacheStaleness`.
- `IntegratedCacheHitRate` - Le taux de lectures et de requêtes de point qui ont utilisé le cache intégré (en dehors de toutes les requêtes de passerelle dédiées qui ont tenté d’utiliser le cache intégré).

Toutes les métriques existantes sont disponibles, par défaut, dans le panneau **Métriques** (pas les métriques classiques) :

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="Image qui montre l’emplacement des métriques du cache intégré" border="false":::

Toutes les métriques sont exposées en tant que moyenne sur tous les nœuds de passerelle dédiés. Par exemple, si vous approvisionnez un cluster de passerelle dédié avec cinq nœuds, les métriques reflètent la valeur moyenne sur les cinq nœuds.

## <a name="troubleshooting-common-issues"></a>Dépannage des problèmes courants

Les exemples ci-dessous montrent comment déboguer certains scénarios courants :

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>Je ne peux pas savoir si mon application utilise la passerelle dédiée

Vérifiez `DedicatedGatewayRequests`. Cette métrique comprend toutes les requêtes qui utilisent la passerelle dédiée, qu’elles aient une correspondance ou non dans le cache intégré. Si votre application utilise la passerelle standard ou le mode direct avec votre chaîne de connexion d’origine, vous ne verrez pas de message d’erreur, mais la valeur `DedicatedGatewayRequests` sera égale à zéro.

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>Je ne peux pas savoir si mes demandes ont une correspondance dans le cache intégré

Consultez les `IntegratedCacheHitRate`. Si cette valeur est égale à zéro, les demandes n’ont pas de correspondance dans le cache intégré. Vérifiez que vous utilisez la chaîne de connexion de passerelle dédiée, que vous vous connectez avec le mode passerelle et que vous avez défini la cohérence finale.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>Je souhaite comprendre si ma passerelle dédiée est trop petite

Vérifiez `IntegratedCacheHitRate`. Si cette valeur est élevée (par exemple, au-delà de 0,5-0,6), il s’agit d’un bon signe que la passerelle dédiée est suffisamment grande.

Si `IntegratedCacheHitRate` est faible, vérifiez `IntegratedCacheEvictedEntriesSize`. Si `IntegratedCacheEvictedEntriesSize` a une valeur élevée, cela peut signifier qu’une plus grande taille de passerelle dédiée serait avantageuse.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>Je souhaite comprendre si ma passerelle dédiée est trop volumineuse

Cela est plus compliqué à mesurer. En général, vous devez commencer petit et lentement augmenter la taille de la passerelle dédiée jusqu’à ce que `IntegratedCacheHitRate` cesse de s’améliorer.

Si la plupart des données sont supprimées du cache en raison du dépassement de `MaxIntegratedCacheStaleness`, plutôt que du LRU, votre cache peut être plus grand que nécessaire. Vérifiez si `IntegratedCacheTTLExpirationCount` est presque aussi grand que `IntegratedCacheEvictedEntriesSize`. Si c’est le cas, vous pouvez faire des essais avec une plus petite taille de passerelle dédiée et comparer les performances.

Vérifiez `DedicatedGatewayMemoryUsage` et comparez la valeur à la taille de passerelle dédiée. Si `DedicatedGatewayMemoryUsage` a une valeur inférieure à la taille de passerelle dédiée, vous devriez essayer une plus petite taille de passerelle dédiée.

## <a name="next-steps"></a>Étapes suivantes

- [FAQ sur le cache intégré](integrated-cache-faq.md)
- [Configurer le cache intégré](how-to-configure-integrated-cache.md)
- [Passerelle dédiée](dedicated-gateway.md)