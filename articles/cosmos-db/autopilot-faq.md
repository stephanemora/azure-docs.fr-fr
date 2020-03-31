---
title: Forum aux questions sur le débit en mode Autopilot Azure Cosmos DB
description: Forum aux questions sur le mode Autopilot pour les bases de données et les conteneurs Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75476007"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Forum aux questions sur le débit provisionné en mode Autopilot Azure Cosmos DB (préversion)
Avec le mode Autopilot, Azure Cosmos DB gère et met à l’échelle automatiquement les RU/s de votre conteneur ou base de données en fonction de l’utilisation. Cette page répond aux questions fréquentes à propos du mode Autopilot. 

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="is-autopilot-mode-supported-for-all-apis"></a>Le mode Autopilot est-il pris en charge pour toutes les API ?
Oui, le mode Autopilot est pris en charge pour toutes les API : Core (SQL), Gremlin, Table, Cassandra et API pour MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>Le mode Autopilot est-il pris en charge pour les comptes multimaîtres ?
Oui, le mode Autopilot est pris en charge pour les comptes multimaîtres. Le nombre maximal de RU/s est disponible dans chaque région ajoutée au compte Cosmos. 

### <a name="what-is-the-pricing-for-autopilot"></a>Quelle est la tarification pour Autopilot ?
Pour plus d’informations, consultez la [page Tarification](https://azure.microsoft.com/pricing/details/cosmos-db/) d’Azure Cosmos DB. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Comment faire pour activer Autopilot pour mes conteneurs ou bases de données ?
Vous pouvez activer le mode Autopilot sur les nouveaux conteneurs et bases de données créés à l’aide du portail Azure. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Y a-t-il une prise en charge de l’interface CLI ou du SDK pour créer des conteneurs ou des bases de données avec le mode Autopilot ?
Actuellement, dans la préversion, vous pouvez uniquement créer des ressources avec le mode Autopilot à partir du portail Azure. La prise en charge de l’interface CLI et du SDK n’est pas encore disponible.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Puis-je activer Autopilot sur un conteneur ou une base de données existant ?
Actuellement, vous pouvez activer Autopilot sur les nouveaux conteneurs et les nouvelles bases de données lors de leur création. La prise en charge de l’activation du mode Autopilot sur les conteneurs et les bases de données existants n’est pas encore disponible. Vous pouvez migrer des conteneurs existants vers un nouveau conteneur à l’aide d’[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) ou du [flux de modification](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Puis-je désactiver le mode Autopilot sur un conteneur ou une base de données ?
Oui, vous pouvez désactiver Autopilot en basculant vers l’option « Manuel » pour le débit provisionné. Dans la préversion, après être passé du mode Autopilot au mode manuel, vous ne pouvez plus réactiver Autopilot pour la même ressource. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>Le mode Autopilot est-il pris en charge pour les bases de données à débit partagé ?
Oui, le mode Autopilot est pris en charge pour les bases de données à débit partagé. Pour activer cette fonctionnalité, sélectionnez le mode Autopilot et l’option **Provisionner le débit** lors de la création de la base de données. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Quel est le nombre de collections autorisées par base de données à débit partagé quand Autopilot est activé ?
Pour les bases de données à débit partagé avec le mode Autopilot activé, le nombre de collections autorisées est le suivant : MIN(25, RU/s max de base de données/1000). Par exemple, si le débit maximal de la base de données est de 20 000 RU/s, la base de données peut avoir MIN(25, 20 000 RU/s / 1000) = 20 collections. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Quelle est la limite de stockage associée à chaque option RU/s max ?  
La limite de stockage en Go pour chaque RU/s max est de : RU/s max de base de données ou conteneur / 100. Par exemple, si le nombre maximal de RU/s est de 20 000, la ressource peut prendre en charge 200 Go de stockage. Pour connaître le nombre maximal de RU/s disponibles et les options de stockage, consultez l’article sur les [limites d’Autopilot](provision-throughput-autopilot.md#autopilot-limits). 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Que se passe-t-il si je dépasse la limite de stockage associée à mon débit maximal ?
Si la limite de stockage associée au débit maximal de la base de données ou du conteneur est dépassée, Azure Cosmos DB augmente automatiquement le débit maximal au niveau élevé suivant pouvant prendre en charge ce niveau de stockage. Supposez par exemple qu’une base de données ou un conteneur est provisionné avec l’option de débit maximal de 4000 RU/s, qui a une limite de stockage de 50 Go. Si le stockage de la ressource augmente à 100 Go, le nombre maximal de RU/s de la base de données ou du conteneur est augmenté automatiquement à 20 000 RU/s, ce qui peut prendre en charge jusqu’à 200 Go. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Quelle est la rapidité du scale-up ou scale-down en mode Autopilot en fonction des pics de trafic ?
Autopilot effectue instantanément un scale-up ou scale-down des RU/s dans les plages minimale et maximale des RU/s, en fonction du trafic entrant. La facturation est effectuée avec une granularité d’une heure (vous êtes facturé pour la valeur RU/s la plus élevée durant une heure donnée). 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Puis-je spécifier une valeur de débit maximal (RU/s) personnalisée pour le mode Autopilot ?
Actuellement, pendant la préversion, vous pouvez choisir parmi [quatre options](provision-throughput-autopilot.md#autopilot-limits) pour le débit maximal (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Puis-je augmenter le nombre maximal de RU/s (passer à un niveau supérieur) sur la base de données ou le conteneur ? 
Oui. À partir de l’option **Mise à l’échelle et paramètres** pour votre conteneur ou de l’option **Mise à l’échelle** pour votre base de données, vous pouvez sélectionner un nombre maximal de RU/s plus élevé pour Autopilot. Il s’agit d’une opération de scale-up asynchrone qui peut prendre un certain temps (généralement de quatre à six heures, en fonction du nombre de RU/s sélectionné), car le service provisionne davantage de ressources pour prendre en charge la plus grande échelle. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Puis-je réduire le nombre maximal de RU/s (passer à un niveau inférieur) sur la base de données ou le conteneur ?
Oui. Tant que le stockage actuel de la base de données ou du conteneur se trouve en dessous de la [limite de stockage](#what-is-the-storage-limit-associated-with-each-max-rus-option) associée au niveau maximal de RU/s vers lequel vous souhaitez effectuer un scale-down, vous pouvez réduire le nombre maximal de RU/s à ce niveau. Par exemple, si vous avez sélectionné 20 000 RU/s comme nombre maximal de RU/s, vous pouvez réduire le nombre maximal de RU/s à 4000 RU/s si vous avez moins de 50 Go de stockage (limite de stockage associée à 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Quel est le mappage entre le nombre maximal de RU/s et les partitions physiques ?
Quand vous sélectionnez initialement le nombre maximal de RU/s, Azure Cosmos DB provisionne : RU/s max / 10 000 RU/s = nombre de partitions physiques. Chaque [partition physique](partition-data.md#physical-partitions) peut prendre en charge jusqu’à 10 000 RU/s et 50 Go de stockage. À mesure que la taille de stockage augmente, Azure Cosmos DB fractionne automatiquement les partitions pour ajouter des partitions physiques supplémentaires afin de gérer l’augmentation du stockage, ou il augmente le niveau de RU/s max si le stockage [dépasse la limite associée](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Le nombre maximal de RU/s de la base de données ou du conteneur est réparti uniformément parmi toutes les partitions physiques. Ainsi, le débit total auquel une partition physique peut être mise à l’échelle est : RU/s max de base de données ou conteneur / nombre de partitions physiques. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Que se passe-t-il si les requêtes entrantes dépassent la valeur RU/s max de la base de données ou du conteneur ?
Si le nombre total de RU/s consommé dépasse le nombre maximal de RU/s du conteneur ou de la base de données, les requêtes qui dépassent le nombre maximal de RU/s sont limitées et retournent un code d’état 429. Les requêtes qui entraînent plus de 100 % d’utilisation normalisée sont également limitées. L’utilisation normalisée est définie comme la quantité maximale d’utilisation de RU/s parmi toutes les partitions physiques. Par exemple, supposez que votre débit maximal est de 20 000 RU/s et que vous disposez de deux partitions physiques, P_1 et P_2, chacune autorisant une mise à l’échelle à 10 000 RU/s. Durant une seconde donnée, si P_1 a utilisé 6 000 RU/s et P_2 8 000 RU/s, l’utilisation normalisée est MAX(6 000 RU / 10 000 RU, 8 000 RU / 10 000 RU) = 0,8.

> [!NOTE]
> Les kits SDK du client Azure Cosmos DB et les outils d’importation de données (Azure Data Factory, bibliothèque d’exécuteur en bloc) réessayent automatiquement en cas d’erreur 429. Des erreurs 429 occasionnelles ne posent donc pas de problème. Un nombre élevé et soutenu d’erreurs 429 peut indiquer que vous devez augmenter le nombre maximal de RU/s ou réexaminer votre stratégie de partitionnement pour une [partition à chaud](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Est-il toujours possible d’observer des erreurs 429 (limitation du débit) quand Autopilot est activé ? 
Oui. Il est possible d’observer des erreurs 429 dans deux scénarios. Tout d’abord, quand le nombre total de RU/s consommé dépasse le nombre maximal de RU/s du conteneur ou de la base de données, le service limite les requêtes en conséquence. 

Ensuite, s’il y a une partition à chaud, c’est-à-dire une valeur de clé de partition logique qui présente un nombre de requêtes beaucoup plus élevé que d’autres valeurs de clé de partition, il est possible que la partition physique sous-jacente dépasse son budget de RU/s. En guise de bonne pratique, pour éviter les partitions à chaud, [choisissez une clé de partition appropriée](partitioning-overview.md#choose-partitionkey) qui entraîne une répartition égale du stockage et du débit. 

Par exemple, si vous sélectionnez l’option de débit maximal de 20 000 RU/s et que vous avez 200 Go de stockage avec quatre partitions physiques, chaque partition physique peut être mise à l’échelle automatiquement jusqu’à 5 000 RU/s. S’il existe une partition à chaud sur une clé de partition logique spécifique, vous verrez des codes d’état 429 quand la partition physique sous-jacente dans laquelle elle se trouve dépasse 5 000 RU/s, c’est-à-dire 100 % de l’utilisation normalisée.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [activer Autopilot sur un conteneur ou une base de données Azure Cosmos](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Découvrez les [avantages du mode Autopilot](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Apprenez-en davantage sur les [partitions logiques et physiques](partition-data.md).
