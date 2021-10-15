---
title: Cache intégré Azure Cosmos DB
description: Le cache intégré Azure Cosmos DB est un cache en mémoire qui vous permet de garantir des coûts gérables et une faible latence à mesure que le volume de vos requêtes augmente.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: tisande
ms.openlocfilehash: ebf9eb5e06b98bdd573d91f0a57daeb9d81b1f50
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230567"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Cache intégré Azure Cosmos DB - Vue d’ensemble (préversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le cache intégré Azure Cosmos DB est un cache en mémoire qui vous permet de garantir des coûts gérables et une faible latence à mesure que le volume de vos requêtes augmente. Le cache intégré est facile à configurer et vous n’avez pas besoin de passer du temps à écrire du code personnalisé pour l’invalidation du cache ou la gestion de l’infrastructure principale. Votre cache intégré utilise une [passerelle dédiée](dedicated-gateway.md) au sein de votre compte Azure Cosmos DB. Le cache intégré est le premier des nombreuses fonctionnalités Azure Cosmos DB qui utiliseront une passerelle dédiée pour améliorer les performances. Vous pouvez choisir parmi trois tailles de passerelle dédiées possibles en fonction du nombre de cœurs et de la mémoire nécessaires à votre charge de travail.

Un cache intégré est automatiquement configuré dans la passerelle dédiée. Le cache intégré comporte deux parties : 

* Un cache d’éléments pour les lectures de point 
* Un cache de requêtes pour les requêtes

Le cache intégré est un cache de lecture-écriture, accessible en écriture, avec une stratégie d’éviction Le moins récemment utilisé (LRU). Le cache d’éléments et le cache de requêtes partagent la même capacité dans le cache intégré et la stratégie d’éviction LRU s’applique aux deux. En d’autres termes, les données sont supprimées du cache de façon stricte en fonction du moment où elles ont été utilisées le moins récemment, qu’il s’agisse d’un point de lecture ou d’une requête.

> [!NOTE]
> Avez-vous des commentaires sur le cache intégré ? Nous attendons vos remarques ! N’hésitez pas à partager vos commentaires directement avec l’équipe d’ingénieurs Azure Cosmos DB : cosmoscachefeedback@microsoft.com

## <a name="workloads-that-benefit-from-the-integrated-cache"></a>Charges de travail qui tirent parti du cache intégré

L’objectif principal du cache intégré est de réduire les coûts liés aux charges de travail lourdes en lecture. Une faible latence, bien qu’utile, n’est pas l’avantage principal du cache intégré, car Azure Cosmos DB est déjà rapide sans mise en cache.

Les lectures de point et les requêtes qui ont une correspondance dans le cache intégré n’utilisent pas de RU. En d’autres termes, les présences dans le cache ont un coût en RU de 0. Les correspondances dans le cache présentent un coût par opération nettement inférieur à celui des lectures de la base de données principale.

Pour les charges de travail qui correspondent aux caractéristiques suivantes, vous devez évaluer si le cache intégré permet de réduire les coûts :

-   Charges de travail nécessitant beaucoup de lectures
-   Nombreuses lectures de point répétées sur des éléments volumineux
-   Nombreuses requêtes à RU élevées répétitives
-   Clé de partition à chaud pour les lectures

Le plus grand facteur d’économie attendu est la mesure dans laquelle les lectures se répètent. Si votre charge de travail exécute régulièrement les mêmes lectures de point ou requêtes dans un laps de temps réduit, il s’agit d’un bon candidat pour le cache intégré. Lorsque vous utilisez le cache intégré pour des lectures répétées, vous utilisez uniquement les RU pour la première lecture. Les lectures suivantes acheminées via le même nœud dédié de passerelle (dans la fenêtre `MaxIntegratedCacheStaleness` et si les données n’ont pas été supprimées) n’utilisent pas le débit.

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

Le cache de requêtes met automatiquement en cache les jetons de continuation de requête, le cas échéant. Si vous avez une requête avec plusieurs pages de résultats, les pages qui sont stockées dans le cache intégré ont une charge de 0 RU. Si vos pages de résultats de requête suivantes requièrent l’exécution du back-end, elles disposeront d’un jeton de continuation de la page précédente afin d’éviter de dupliquer le travail précédent.

> [!NOTE]
> Les instances de cache intégrées dans différents nœuds de passerelle dédiés ont des caches indépendants les uns des autres. Si les données sont mises en cache dans un nœud, elles ne sont pas nécessairement mises en cache dans les autres.

## <a name="integrated-cache-consistency"></a>Cohérence du cache intégré

Le cache intégré prend uniquement en charge la session et la [cohérence](consistency-levels.md) éventuelle. Si une lecture a constamment un préfixe, une session, une obsolescence limitée ou une cohérence forte, elle contourne toujours le cache intégré.

Le moyen le plus simple de configurer une session ou une cohérence éventuelle pour toutes les lectures est de [le définir au niveau du compte](consistency-levels.md#configure-the-default-consistency-level). Toutefois, si vous souhaitez que certaines de vos lectures aient une cohérence spécifique, vous pouvez également configurer la cohérence au [niveau de la requête](how-to-manage-consistency.md#override-the-default-consistency-level).

### <a name="session-consistency"></a>Cohérence de session

La [cohérence de Session](consistency-levels.md#session-consistency) est le niveau de cohérence le plus largement utilisé pour les comptes Azure Cosmos DB à région unique et globalement distribués. Lors de l’utilisation de la cohérence de session, les sessions client uniques peuvent lire leurs propres écritures. Les clients en dehors de la session effectuant des écritures verront la cohérence éventuelle.

## <a name="maxintegratedcachestaleness"></a>MaxIntegratedCacheStaleness

`MaxIntegratedCacheStaleness`est l’obsolescence maximale acceptable pour les lectures et les requêtes de points mis en cache, quelle que soit la cohérence sélectionnée. `MaxIntegratedCacheStaleness` est configurable au niveau de la demande. Par exemple, si vous définissez `MaxIntegratedCacheStaleness` sur 2  heures, votre demande retourne uniquement les données mises en cache si celles-ci ont moins de 2 heures. Pour augmenter la probabilité que les lectures répétées utilisent le cache intégré, vous devez définir `MaxIntegratedCacheStaleness` avec une valeur aussi élevée que vos besoins métier le permettent.

Il est important de comprendre que `MaxIntegratedCacheStaleness`, lorsqu’elle est configurée sur une demande qui finit par remplir le cache, n’a pas d’impact sur la durée de mise en cache de la demande. `MaxIntegratedCacheStaleness` applique la cohérence lorsque vous essayez d’utiliser des données mises en cache. Il n’existe pas de paramètre de durée de vie globale ou de conservation du cache, donc les données sont supprimées du cache seulement si le cache intégré est plein ou si une nouvelle lecture est exécutée avec une `MaxIntegratedCacheStaleness` inférieure à l’ancienneté de l’entrée mise en cache actuelle.

Il s’agit d’une amélioration par rapport à la façon dont la plupart des caches fonctionnent et elle permet les personnalisations supplémentaires suivantes :

- Vous pouvez définir différentes exigences d’obsolescence pour chaque lecture ou requête de point.
- Les différents clients, même s’ils exécutent la même lecture ou requête de point, peuvent configurer différentes valeurs pour `MaxIntegratedCacheStaleness`.
- Si vous souhaitez modifier la cohérence de lecture lors de l’utilisation de données mises en cache, changer `MaxIntegratedCacheStaleness` aura un effet immédiat sur la cohérence de lecture.

> [!NOTE]
> Lorsqu’elle n’est pas explicitement configurée, la valeur par défaut de MaxIntegratedCacheStaleness est de 5 minutes.

Pour mieux comprendre le paramètre `MaxIntegratedCacheStaleness`, regardez l’exemple suivant :

| Temps       | Requête                                         | response                                                     |
| ---------- | ----------------------------------------------- | ------------------------------------------------------------ |
| t = 0 sec  | Exécuter la requête A avec MaxIntegratedCacheStaleness = 30 secondes | Retourner les résultats de la base de données back-end (frais RU normaux) et remplir le cache     |
| t = 0 sec  | Exécuter la requête B avec MaxIntegratedCacheStaleness = 60 secondes | Retourner les résultats de la base de données back-end (frais RU normaux) et remplir le cache     |
| t = 20 sec | Exécuter la requête A avec MaxIntegratedCacheStaleness = 30 secondes | Retourner les résultats du cache intégré (0 RU facturée)           |
| t = 20 sec | Exécuter la requête B avec MaxIntegratedCacheStaleness = 60 secondes | Retourner les résultats du cache intégré (0 RU facturée)           |
| t = 40 sec | Exécuter la requête A avec MaxIntegratedCacheStaleness = 30 secondes | Retourner les résultats de la base de données back-end (frais RU normaux) et actualiser le cache |
| t = 40 sec | Exécuter la requête B avec MaxIntegratedCacheStaleness = 60 secondes | Retourner les résultats du cache intégré (0 RU facturée)           |
| t = 50 sec | Exécuter la requête B avec MaxIntegratedCacheStaleness = 20 secondes | Retourner les résultats de la base de données back-end (frais RU normaux) et actualiser le cache |

> [!NOTE]
> La personnalisation de `MaxIntegratedCacheStaleness` est prise en charge uniquement dans les derniers kits SDK .NET et Java en préversion.

[Apprenez à configurer `MaxIntegratedCacheStaleness`.](how-to-configure-integrated-cache.md#adjust-maxintegratedcachestaleness)

## <a name="metrics"></a>Mesures

Lorsque vous utilisez le cache intégré, il est utile de surveiller quelques métriques clés. Les métriques de cache intégrées sont les suivantes :

- `DedicatedGatewayAverageCpuUsage` - Utilisation moyenne du processeur sur les nœuds de passerelle dédiée.
- `DedicatedGatewayMaxCpuUsage` - Utilisation maximale du processeur sur les nœuds de passerelle dédiée.
- `DedicatedGatewayAverageMemoryUsage` - Utilisation moyenne de la mémoire sur les nœuds de passerelle dédiée, qui sont utilisés à la fois pour le routage des demandes et la mise en cache des données.
- `DedicatedGatewayRequests` - Nombre total de demandes de passerelle dédiée sur toutes les instances de passerelle dédiée.
- `IntegratedCacheEvictedEntriesSize` - Quantité moyenne de données supprimées en raison du LRU du cache intégré sur les nœuds de passerelle dédiée. Cette valeur n’inclut pas les données qui ont expiré en raison du dépassement de la durée de `MaxIntegratedCacheStaleness`.
- `IntegratedCacheItemExpirationCount` - Nombre d’éléments supprimés du cache intégré en raison des lectures de point mises en cache qui dépassent la durée de `MaxIntegratedCacheStaleness`. Cette valeur est une moyenne des instances du cache intégré sur tous les nœuds de passerelle dédiée.
- `IntegratedCacheQueryExpirationCount` - Nombre de requêtes supprimées du cache intégré en raison des requêtes mises en cache qui dépassent la durée de `MaxIntegratedCacheStaleness`. Cette valeur est une moyenne des instances du cache intégré sur tous les nœuds de passerelle dédiée.
- `IntegratedCacheItemHitRate` - Proportion de lectures de point qui ont utilisé le cache intégré (par rapport à toutes les lectures de point acheminées via la passerelle dédiée avec session ou cohérence finale). Cette valeur est une moyenne des instances du cache intégré sur tous les nœuds de passerelle dédiée.
- `IntegratedCacheQueryHitRate` - Proportion de requêtes qui ont utilisé le cache intégré (par rapport à toutes les requêtes acheminées via la passerelle dédiée avec session ou cohérence finale). Cette valeur est une moyenne des instances du cache intégré sur tous les nœuds de passerelle dédiée.

Toutes les métriques existantes sont disponibles, par défaut, dans le panneau **Métriques** (pas les métriques classiques) :

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="Image qui montre l’emplacement des métriques du cache intégré" border="false":::

Les métriques représentent une moyenne, une valeur maximale ou une somme pour tous les nœuds de passerelle dédiée. Par exemple, si vous provisionnez un cluster de passerelle dédié avec cinq nœuds, les métriques reflètent la valeur agrégée sur les cinq nœuds. Il n’est pas possible de déterminer les valeurs de métrique pour chaque nœud individuel.

## <a name="troubleshooting-common-issues"></a>Dépannage des problèmes courants

Les exemples ci-dessous montrent comment déboguer certains scénarios courants :

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>Je ne peux pas savoir si mon application utilise la passerelle dédiée

Vérifiez `DedicatedGatewayRequests`. Cette métrique comprend toutes les requêtes qui utilisent la passerelle dédiée, qu’elles aient une correspondance ou non dans le cache intégré. Si votre application utilise la passerelle standard ou le mode direct avec votre chaîne de connexion d’origine, vous ne verrez pas de message d’erreur, mais la valeur `DedicatedGatewayRequests` sera égale à zéro.

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>Je ne peux pas savoir si mes demandes ont une correspondance dans le cache intégré

Regardez `IntegratedCacheItemHitRate` et `IntegratedCacheQueryHitRate`. Si ces deux valeurs sont égales à zéro, les demandes n’atteignent pas le cache intégré. Vérifiez que vous utilisez la chaîne de connexion de passerelle dédiée, que vous vous [connectez avec le mode passerelle](sql-sdk-connection-modes.md) et que vous [avez défini la session ou la cohérence éventuelle](consistency-levels.md#configure-the-default-consistency-level).

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>Je souhaite comprendre si ma passerelle dédiée est trop petite

Regardez `IntegratedCacheItemHitRate` et `IntegratedCacheQueryHitRate`. Si ces valeurs sont élevées (par exemple, au-delà de 0.7-0.8), c’est signe que la passerelle dédiée est suffisamment grande.

Si `IntegratedCacheItemHitRate` ou `IntegratedCacheQueryHitRate` est bas, regardez `IntegratedCacheEvictedEntriesSize`. Si `IntegratedCacheEvictedEntriesSize` a une valeur élevée, cela peut signifier qu’une plus grande taille de passerelle dédiée serait avantageuse. Vous pouvez essayer en agrandissant la taille de la passerelle dédiée et en comparant les nouveaux `IntegratedCacheItemHitRate` et `IntegratedCacheQueryHitRate`. Si une plus grande passerelle dédiée n’améliore pas `IntegratedCacheItemHitRate` ou `IntegratedCacheQueryHitRate`, il est possible que ce soit juste les lectures qui ne se répètent pas assez pour que le cache intégré ait un impact.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>Je souhaite comprendre si ma passerelle dédiée est trop volumineuse

Il est plus difficile de mesurer si une passerelle dédiée est trop grande que de mesurer si une passerelle dédiée est trop petite. En général, vous devez commencer petit et lentement augmenter la taille de la passerelle dédiée jusqu’à ce que `IntegratedCacheItemHitRate` et `IntegratedCacheQueryHitRate` cessent de s’améliorer. Dans certains cas, une seule des deux métriques d’accès au cache est importante, pas les deux. Par exemple, si votre charge de travail est principalement des requêtes plutôt que des lectures de point, `IntegratedCacheQueryHitRate` est bien plus important que `IntegratedCacheItemHitRate`.

Si la plupart des données sont supprimées du cache en raison du dépassement de `MaxIntegratedCacheStaleness`, plutôt que du LRU, votre cache peut être plus grand que nécessaire. Si `IntegratedCacheItemExpirationCount` et `IntegratedCacheQueryExpirationCount` ensemble sont presque aussi élevés que `IntegratedCacheEvictedEntriesSize`, vous pouvez faire des essais avec une plus petite taille de passerelle dédiée et comparer les performances.

### <a name="i-want-to-understand-if-i-need-to-add-more-dedicated-gateway-nodes"></a>Je souhaite savoir si j’ai besoin d’ajouter d’autres nœuds de passerelle dédiée

Dans certains cas, si la latence est anormalement élevée, vous pouvez avoir besoin d’un plus grand nombre de nœuds de passerelle dédiée plutôt que de plus grands nœuds. Regardez `DedicatedGatewayMaxCpuUsage` et `DedicatedGatewayAverageMemoryUsage` pour déterminer si l’ajout de nœuds de passerelle dédiée supplémentaires réduirait la latence. Il est bon de se rappeler que puisque toutes les instances du cache intégré sont indépendantes les unes des autres, l’ajout de nœuds de passerelle dédiée supplémentaires ne réduit pas `IntegratedCacheEvictedEntriesSize`. Toutefois, l’ajout de nœuds supplémentaires améliore le volume de requêtes que votre cluster de passerelle dédiée peut gérer.

## <a name="next-steps"></a>Étapes suivantes

- [FAQ sur le cache intégré](integrated-cache-faq.md)
- [Configurer le cache intégré](how-to-configure-integrated-cache.md)
- [Passerelle dédiée](dedicated-gateway.md)
- Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
    - Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](convert-vcore-to-request-unit.md) 
    - Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)