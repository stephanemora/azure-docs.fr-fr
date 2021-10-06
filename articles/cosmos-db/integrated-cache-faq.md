---
title: Forum aux questions sur le cache intégré d’Azure Cosmos DB
description: Forum aux questions sur le cache intégré d’Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: a8d1dc69ef64726f55dffd20221a19c3f3114964
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595139"
---
# <a name="azure-cosmos-db-integrated-cache-frequently-asked-questions"></a>Forum aux questions sur le cache intégré d’Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le cache intégré Azure Cosmos DB est un cache en mémoire intégré à Azure Cosmos DB. Cet article répond aux questions fréquemment posées sur le cache intégré Azure Cosmos DB.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="why-does-the-integrated-cache-require-a-dedicated-gateway"></a>Pourquoi le cache intégré a-t-il besoin d’une passerelle dédiée ?

Si vous êtes connecté à Azure Cosmos DB à l’aide du mode passerelle, vous avez utilisé la passerelle standard. Tandis que le principal Azure Cosmos DB (le débit et le stockage configurés) ont une capacité dédiée par conteneur, la passerelle standard est partagée entre plusieurs clients. Il est pratique pour de nombreux clients de partager une passerelle standard, car les ressources de calcul consommées par chaque client individuel sont minimes. Étant donné que le cache intégré est spécifique à votre compte Azure Cosmos DB et qu’il nécessite beaucoup de ressources processeur et de mémoire, il nécessite un nœud de passerelle dédié.

### <a name="what-is-a-dedicated-gateway"></a>Qu’est-ce qu’une passerelle dédiée ?

Une [passerelle dédiée](dedicated-gateway.md) est un calcul côté serveur qui est un serveur frontal pour les données d’un compte Azure Cosmos DB. Lorsque vous vous connectez à votre point de terminaison de passerelle dédié, votre application envoie une demande à la passerelle dédiée, qui achemine ensuite la demande vers différentes partitions du principal.

### <a name="does-using-the-dedicated-gateway-offer-any-other-performance-benefits-over-using-the-standard-gateway"></a>L’utilisation de la passerelle dédiée offre-t-elle d’autres avantages en matière de performances par rapport à l’utilisation de la passerelle standard ?

En général, les demandes acheminées par la passerelle dédiée auront une latence légèrement inférieure et cohérente par rapport aux demandes acheminées par la passerelle standard. Même les demandes qui n’utilisent pas le cache intégré auront toujours une latence légèrement inférieure à celle de la passerelle standard.

### <a name="what-kind-of-latency-should-i-expect-from-the-integrated-cache"></a>Quel type de latence dois-je attendre du cache intégré ?

Une requête traitée par le cache intégré est plus rapide, car les données mises en cache sont stockées en mémoire sur la passerelle dédiée, plutôt que sur le principal. Pour les lectures de points mis en cache, vous devez vous attendre à une latence de 2-4 ms.

Pour les requêtes mises en cache, la latence dépend de la requête. Le cache de requêtes fonctionne en mettant en cache la réponse du moteur de requête pour une requête particulière. Cette réponse est ensuite renvoyée côté client vers le SDK pour traitement. Pour les requêtes simples, un travail minimal dans le SDK est requis et des latences de 2-4 ms sont typiques. Toutefois, des requêtes plus complexes avec `GROUP BY` ou `DISTINCT` nécessitent davantage de traitement dans le SDK, et la latence peut être plus élevée, même avec le cache de requêtes.

Si vous vous connectiez précédemment à Azure Cosmos DB en mode direct et que vous passez à une connexion avec passerelle dédiée, vous pourriez observer une légère augmentation de la latence pour certaines requêtes. L’utilisation du mode passerelle requiert l’envoi d’une requête à la passerelle (dans ce cas, la passerelle dédiée), puis l’acheminement approprié vers le serveur principal. Le mode direct, comme son nom l’indique, permet au client de communiquer directement avec le principal, en évitant un saut supplémentaire. 

Si votre application utilisait précédemment le mode direct, les avantages de la latence du cache intégré ne seront significatifs que dans les scénarios suivants :

- Latence de lecture de points pour les éléments volumineux (> 16 ko)
- Requêtes à RU élevées ou complexes

Si votre application utilisait précédemment le mode passerelle avec la passerelle standard, le cache intégré offre des réductions de latence dans presque tous les scénarios. 

### <a name="does-the-azure-cosmos-db-availability-sla-extend-to-the-dedicated-gateway-and-integrated-cache"></a>Le contrat SLA de disponibilité d’Azure Cosmos DB s’étend-il à la passerelle dédiée et au cache intégré ?

Nous disposerons d’un contrat SLA/SLO de disponibilité sur la passerelle dédiée (et par conséquent le cache intégré) une fois la fonctionnalité en disponibilité générale. Pour les scénarios qui nécessitent une haute disponibilité, vous devez approvisionner 3 fois le nombre d’instances de passerelle dédiées nécessaires. Par exemple, si un nœud de passerelle dédié est nécessaire en production, vous devez approvisionner deux nœuds de passerelle dédiés supplémentaires pour tenir compte des interruptions de service potentielles ou des pannes.

### <a name="the-integrated-cache-is-only-available-for-sql-core-api-right-now-are-you-planning-on-releasing-it-for-other-apis-as-well"></a>Le cache intégré est disponible uniquement pour l’API SQL (Core) actuellement. Envisagez-vous également de le proposer pour d’autres API ?

Le développement du cache intégré au-delà de l’API SQL est planifié à long terme, mais après la préversion publique initiale du cache intégré.

### <a name="what-consistency-does-the-integrated-cache-support"></a>Quelle est la cohérence prise en charge par le cache intégré ?

Le cache intégré prend en charge la session et la cohérence éventuelle. Vous pouvez également configurer le [MaxIntegratedCacheStaleness](integrated-cache.md#maxintegratedcachestaleness)facultatif, qui place une limite supérieure sur les données mises en cache.

## <a name="next-steps"></a>Étapes suivantes

- [Cache intégré](integrated-cache.md)
- [Configurer le cache intégré](how-to-configure-integrated-cache.md)
- [Passerelle dédiée](dedicated-gateway.md)
- Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
    - Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](convert-vcore-to-request-unit.md) 
    - Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)