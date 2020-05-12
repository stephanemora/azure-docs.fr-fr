---
title: Forum aux questions sur le débit approvisionné en mode de mise à l’échelle automatique dans Azure Cosmos DB
description: Forum aux questions sur le débit provisionné en mode de mise à l’échelle automatique pour les bases de données et les conteneurs Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: fa4c2708f34a377a17914c7e6e5abdd709cbb5b1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791780"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Forum aux questions sur le débit provisionné en mode de mise à l’échelle automatique dans Azure Cosmos DB

Grâce au débit approvisionné en mode de mise à l’échelle automatique, Azure Cosmos DB gère et met à l’échelle automatiquement les RU/s de votre conteneur ou base de données en fonction de l’utilisation. Cette page répond aux questions fréquentes à propos de la mise à l’échelle automatique.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="is-autoscale-supported-for-all-apis"></a>La mise à l’échelle automatique est-elle prise en charge pour toutes les API ?
Oui, la mise à l’échelle automatique est prise en charge pour toutes les API : Core (SQL), Gremlin, Table, Cassandra et API pour MongoDB.

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>La mise à l’échelle automatique est-elle prise en charge pour les comptes multimaîtres ?
Oui, la mise à l’échelle automatique est prise en charge pour les comptes multimaîtres. Le nombre maximal de RU/s est disponible dans chaque région ajoutée au compte Cosmos. 

### <a name="what-is-the-pricing-for-autoscale-"></a>Quels sont les tarifs de la mise à l’échelle automatique ?
Pour plus d’informations, consultez la [page Tarification](https://azure.microsoft.com/pricing/details/cosmos-db/) d’Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>Comment faire pour activer la mise à l’échelle automatique pour mes conteneurs ou bases de données ?
Vous pouvez activer la mise à l’échelle automatique sur les nouveaux conteneurs et bases de données créés à l’aide du Portail Azure.

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-provisioned-throughput"></a>Y a-t-il une prise en charge de l’interface CLI ou du Kit de développement logiciel (SDK) pour créer des conteneurs ou des bases de données avec le débit approvisionné en mode de mise à l’échelle automatique ?
Actuellement, vous pouvez uniquement créer des ressources avec le débit approvisionné en mode de mise à l’échelle automatique à partir du Portail Azure. La prise en charge de l’interface CLI et du SDK n’est pas encore disponible.

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>Puis-je activer la mise à l’échelle automatique sur un conteneur ou une base de données existant ?
Vous pouvez activer la mise à l’échelle automatique sur les nouveaux conteneurs et les nouvelles bases de données lors de leur création. La prise en charge de l’activation de la mise à l’échelle automatique sur les conteneurs et les bases de données existants n’est pas encore disponible. Vous pouvez migrer des conteneurs existants vers un nouveau conteneur à l’aide d’[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) ou du [flux de modification](change-feed.md). 

### <a name="can-i-turn-off-autoscale-on-a-container-or-database"></a>Puis-je désactiver la mise à l’échelle automatique sur un conteneur ou une base de données ?
Oui, vous pouvez désactiver la mise à l’échelle automatique en basculant vers l’option « standard (manuel) » pour le débit approvisionné. Dans la version actuelle, après être passé du débit approvisionné en mode de mise à l’échelle automatique au débit approvisionné standard, vous ne pouvez plus réactiver la mise à l’échelle automatique pour la même ressource. 

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>La mise à l’échelle automatique est-elle prise en charge pour les bases de données à débit partagé ?
Oui, la mise à l’échelle automatique est prise en charge pour les bases de données à débit partagé. Pour activer cette fonctionnalité, sélectionnez la mise à l’échelle automatique et l’option **Approvisionner le débit** lors de la création de la base de données. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>Quel est le nombre de collections autorisées par base de données à débit partagé quand la mise à l’échelle automatique est activée ?
Pour les bases de données à débit partagé avec la mise à l’échelle automatique activée, le nombre de collections autorisées est le suivant : MIN(25, RU/s max de base de données/1000). Par exemple, si le débit maximal de la base de données est de 20 000 RU/s, la base de données peut avoir MIN(25, 20 000 RU/s / 1000) = 20 collections. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Quelle est la limite de stockage associée à chaque option RU/s max ?  
La limite de stockage en Go pour chaque RU/s max est de : RU/s max de base de données ou conteneur / 100. Par exemple, si le nombre maximal de RU/s est de 20 000, la ressource peut prendre en charge 200 Go de stockage. Pour connaître le nombre maximal de RU/s disponibles et les options de stockage, consultez l’article sur les [limites de la mise à l’échelle automatique](provision-throughput-autoscale.md#autoscale-limits). 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Que se passe-t-il si je dépasse la limite de stockage associée à mon débit maximal ?
Si la limite de stockage associée au débit maximal de la base de données ou du conteneur est dépassée, Azure Cosmos DB augmente automatiquement le débit maximal au niveau élevé suivant pouvant prendre en charge ce niveau de stockage. Supposez par exemple qu’une base de données ou un conteneur est provisionné avec l’option de débit maximal de 4000 RU/s, qui a une limite de stockage de 50 Go. Si le stockage de la ressource augmente à 100 Go, le nombre maximal de RU/s de la base de données ou du conteneur est augmenté automatiquement à 20 000 RU/s, ce qui peut prendre en charge jusqu’à 200 Go. 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>Quelle est la rapidité du scale-up ou scale-down en mode de mise à l’échelle automatique en fonction des pics de trafic ?
Grâce à la mise à l’échelle automatique, vous pouvez effectuer instantanément un scale-up ou scale-down des RU/s dans les plages minimale et maximale des RU/s, en fonction du trafic entrant. La facturation est effectuée avec une granularité d’une heure (vous êtes facturé pour la valeur RU/s la plus élevée durant une heure donnée).

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale"></a>Puis-je spécifier une valeur de débit maximal (RU/s) personnalisée pour la mise à l’échelle automatique ?
Vous pouvez choisir parmi [quatre options](provision-throughput-autoscale.md#autoscale-limits) pour le débit maximal (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Puis-je augmenter le nombre maximal de RU/s (passer à un niveau supérieur) sur la base de données ou le conteneur ? 
Oui. À partir de l’option **Mise à l’échelle et paramètres** pour votre conteneur ou de l’option **Mise à l’échelle** pour votre base de données, vous pouvez sélectionner un nombre maximal de RU/s plus élevé pour la mise à l’échelle automatique. Il s’agit d’une opération de scale-up asynchrone qui peut prendre un certain temps (généralement de quatre à six heures, en fonction du nombre de RU/s sélectionné), car le service provisionne davantage de ressources pour prendre en charge la plus grande échelle. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Puis-je réduire le nombre maximal de RU/s (passer à un niveau inférieur) sur la base de données ou le conteneur ?
Oui. Tant que le stockage actuel de la base de données ou du conteneur se trouve en dessous de la [limite de stockage](#what-is-the-storage-limit-associated-with-each-max-rus-option) associée au niveau maximal de RU/s vers lequel vous souhaitez effectuer un scale-down, vous pouvez réduire le nombre maximal de RU/s à ce niveau. Par exemple, si vous avez sélectionné 20 000 RU/s comme nombre maximal de RU/s, vous pouvez réduire le nombre maximal de RU/s à 4000 RU/s si vous avez moins de 50 Go de stockage (limite de stockage associée à 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Quel est le mappage entre le nombre maximal de RU/s et les partitions physiques ?
Quand vous sélectionnez initialement le nombre maximal de RU/s, Azure Cosmos DB provisionne : RU/s max / 10 000 RU/s = nombre de partitions physiques. Chaque [partition physique](partition-data.md#physical-partitions) peut prendre en charge jusqu’à 10 000 RU/s et 50 Go de stockage. À mesure que la taille de stockage augmente, Azure Cosmos DB fractionne automatiquement les partitions pour ajouter des partitions physiques supplémentaires afin de gérer l’augmentation du stockage, ou il augmente le niveau de RU/s max si le stockage [dépasse la limite associée](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Le nombre maximal de RU/s de la base de données ou du conteneur est réparti uniformément parmi toutes les partitions physiques. Ainsi, le débit total auquel une partition physique peut être mise à l’échelle est : RU/s max de base de données ou conteneur / nombre de partitions physiques. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Que se passe-t-il si les requêtes entrantes dépassent la valeur RU/s max de la base de données ou du conteneur ?
Si le nombre total de RU/s consommé dépasse le nombre maximal de RU/s du conteneur ou de la base de données, les requêtes qui dépassent le nombre maximal de RU/s sont limitées et retournent un code d’état 429. Les requêtes qui entraînent plus de 100 % d’utilisation normalisée sont également limitées. L’utilisation normalisée est définie comme la quantité maximale d’utilisation de RU/s parmi toutes les partitions physiques. Par exemple, supposez que votre débit maximal est de 20 000 RU/s et que vous disposez de deux partitions physiques, P_1 et P_2, chacune autorisant une mise à l’échelle à 10 000 RU/s. Durant une seconde donnée, si P_1 a utilisé 6 000 RU/s et P_2 8 000 RU/s, l’utilisation normalisée est MAX(6 000 RU / 10 000 RU, 8 000 RU / 10 000 RU) = 0,8.

> [!NOTE]
> Les kits SDK du client Azure Cosmos DB et les outils d’importation de données (Azure Data Factory, bibliothèque d’exécuteur en bloc) réessayent automatiquement en cas d’erreur 429. Des erreurs 429 occasionnelles ne posent donc pas de problème. Un nombre élevé et soutenu d’erreurs 429 peut indiquer que vous devez augmenter le nombre maximal de RU/s ou réexaminer votre stratégie de partitionnement pour une [partition à chaud](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Est-il toujours possible d’observer des erreurs 429 (limitation du débit) quand la mise à l’échelle automatique est activée ? 
Oui. Il est possible d’observer des erreurs 429 dans deux scénarios. Tout d’abord, quand le nombre total de RU/s consommé dépasse le nombre maximal de RU/s du conteneur ou de la base de données, le service limite les requêtes en conséquence. 

Ensuite, s’il y a une partition à chaud, c’est-à-dire une valeur de clé de partition logique qui présente un nombre de requêtes beaucoup plus élevé que d’autres valeurs de clé de partition, il est possible que la partition physique sous-jacente dépasse son budget de RU/s. En guise de bonne pratique, pour éviter les partitions à chaud, [choisissez une clé de partition appropriée](partitioning-overview.md#choose-partitionkey) qui entraîne une répartition égale du stockage et du débit. 

Par exemple, si vous sélectionnez l’option de débit maximal de 20 000 RU/s et que vous avez 200 Go de stockage avec quatre partitions physiques, chaque partition physique peut être mise à l’échelle automatiquement jusqu’à 5 000 RU/s. S’il existe une partition à chaud sur une clé de partition logique spécifique, vous verrez des codes d’état 429 quand la partition physique sous-jacente dans laquelle elle se trouve dépasse 5 000 RU/s, c’est-à-dire 100 % de l’utilisation normalisée.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [activer la mise à l’échelle automatique sur un conteneur ou une base de données Azure Cosmos](provision-throughput-autoscale.md#create-db-container-autoscale).
* Découvrez les [avantages du débit approvisionné avec la mise à l’échelle automatique](provision-throughput-autoscale.md#autoscale-benefits).
* Apprenez-en davantage sur les [partitions logiques et physiques](partition-data.md).
