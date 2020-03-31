---
title: Améliorer les performances des applications Azure avec Azure Advisor
description: Utilisez Advisor pour optimiser les performances de vos déploiements Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443055"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Améliorer les performances des applications Azure avec Azure Advisor

Les recommandations d’Azure Advisor en matière de performances vous aident à optimiser la vitesse et la réactivité de vos applications stratégiques. Vous pouvez obtenir des recommandations en matière de performances à l’aide d’Advisor dans l’onglet **Performances** du tableau de bord d’Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Diminuer la durée de vie DNS dans votre profil Traffic Manager pour passer plus rapidement à des points de terminaison intègres

Les [paramètres TTL (durée de vie)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) de votre profil Traffic Manager permettent de spécifier l’intervalle de temps nécessaire avant de changer de point de terminaison lorsque celui-ci ne répond plus. Lorsque l’on réduit les valeurs TTL, les clients sont redirigés plus rapidement vers des points de terminaison opérationnels.

Azure Advisor identifie les profils Traffic Manager ayant une valeur TTL élevée et recommande de la régler à 20 secondes ou 60 secondes selon que le profil est configuré pour une [Bascule rapide](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Améliorer les performances de base de données avec SQL DB Advisor

Le conseiller vous offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure. Il s’intègre à SQL Database Advisor pour vous proposer des recommandations en vue d’améliorer les performances de votre base de données SQL Azure. SQL Database Advisor évalue les performances de vos bases de données SQL Azure en analysant votre historique d’utilisation. Il propose alors les recommandations les plus adaptées pour exécuter la charge de travail standard de la base de données.

> [!NOTE]
> Pour obtenir des recommandations, une base de données doit avoir été utilisée pendant environ une semaine et avoir fait l’objet d’une activité cohérente au cours de cette semaine. SQL Database Advisor peut plus facilement optimiser les modèles de requête cohérents que les pics d’activité aléatoires.

Pour plus d’informations sur SQL Database Advisor, consultez la page [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Améliorer la fiabilité et les performances d’App Service

Le conseiller Azure intègre des recommandations quant aux meilleures pratiques pour améliorer votre expérience App Services et vous faire découvrir des fonctionnalités appropriées de la plateforme. Exemples de recommandations App Services :
* Détection des instances où les ressources de mémoire ou de processeur sont épuisées par des exécutions d’application avec options d’atténuation.
* Détection des instances où la colocalisation de ressources telles que les applications web et les bases de données peut améliorer les performances et réduire les coûts.

Pour plus d’informations sur les recommandations App Services, consultez [Meilleures pratiques pour Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Utiliser la fonctionnalité Disques managés pour éviter la limitation des E/S de disque

Advisor identifie les machines virtuelles appartenant à un compte de stockage qui atteint sa cible de scalabilité. Cette condition rend les machines virtuelles sensibles à la limitation des E/S. Advisor recommande l'utilisation de la fonctionnalité Disques managés pour prévenir toute détérioration des performances.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Améliorer les performances et la fiabilité des disques de machine virtuelle en utilisant le stockage Premium

Advisor identifie les machines virtuelles dont les disques standard ont un volume de transactions élevé sur votre compte de stockage, et recommande de passer à des disques Premium. 

Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Les disques de machine virtuelle qui utilisent des comptes Premium Storage stockent les données sur des disques SSD. Pour que votre application bénéficie de performances optimales, nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’E/S par seconde dans le stockage Premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Supprimez l’asymétrie des données sur votre table d’entrepôt de données SQL pour augmenter les performances de requête

L’asymétrie des données peut provoquer des déplacement des données ou des goulots d’étranglement de ressource inutiles lors de l’exécution de votre charge de travail. Advisor va détecter l’asymétrie des données au-delà de 15 % et vous recommander de redistribuer vos données et de revisiter vos sélections de clé de distribution de table. Pour en savoir plus sur l’identification et la suppression d’une asymétrie, consultez [résolution des problèmes d’asymétrie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Créer ou mettre à jour les statistiques de table obsolètes sur votre table d’entrepôt de données SQL pour augmenter les performances de requête

Advisor identifie les tables qui n’ont pas de [statistiques de table](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) à jour et recommande la création ou mise à jour des statistiques de table. L'optimiseur de requête de SQL Data Warehouse utilise des statiques à jour pour estimer la cardinalité ou le nombre de lignes du résultat de la requête, ce qui permet à l'optimiseur de requête de créer un plan de requête de haute qualité pour des performances optimales.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Effectuer un scale-up pour optimiser l’utilisation du cache sur vos tables SQL Data Warehouse et ainsi améliorer les performances de requête

Azure Advisor détecte si votre instance SQL Data Warehouse enregistre un pourcentage élevé d’utilisation du cache avec un faible pourcentage d’accès. Cette condition indique une éviction élevée du cache, ce qui peut avoir une incidence sur les performances de votre instance SQL Data Warehouse. Advisor suggère d’effectuer un scale-up de votre instance SQL Data Warehouse, pour garantir l’allocation d’une capacité suffisante du cache pour votre charge de travail.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Convertir les tables SQL Data Warehouse en tables répliquées pour augmenter les performances de requête

Advisor identifie les tables qui ne sont pas des tables répliquées, mais qui pourraient tirer parti de la conversion, et vous suggère de convertir ces tables. Ces suggestions sont basées sur la taille de la table répliquée, le nombre de colonnes, le type de distribution de la table et le nombre de partitions de la table SQL Data Warehouse. Une méthode heuristique supplémentaire peut être fournie dans la suggestion pour le contexte. Pour en savoir plus sur la détermination de cette recommandation, consultez [Recommandations de SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrer votre compte de stockage vers Azure Resource Manager pour obtenir toutes les dernières fonctionnalités Azure

Migrez le modèle de déploiement de votre compte de stockage vers Azure Resource Manager (Resource Manager) afin de tirer parti des déploiements de modèles, d'options de sécurité supplémentaires et de la possibilité de mettre à niveau votre compte vers GPv2 pour bénéficier des dernières fonctionnalités de Stockage Azure. Advisor identifie les comptes de stockage autonomes qui utilisent le modèle de déploiement classique et recommande une migration vers le modèle de déploiement Resource Manager.

> [!NOTE]
> Les alertes classiques dans Azure Monitor ont été mises hors service en août 2019. Nous vous recommandons de mettre à niveau votre compte de stockage classique pour utiliser Resource Manager afin de conserver la fonctionnalité d'alerte sur la nouvelle plateforme. Pour plus d’informations, consultez [Suppression des alertes classiques](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Concevoir vos comptes de stockage de manière à éviter d’atteindre le nombre maximum d’abonnements

Une région Azure peut prendre en charge un maximum de 250 comptes de stockage par abonnement. Une fois cette limite atteinte, vous ne pourrez plus créer de comptes de stockage dans cette combinaison région/abonnement. Azure Advisor vérifie vos abonnements et affiche des suggestions pour vous aider à concevoir pour un moindre nombre de comptes de stockage lorsque vous êtes près d’atteindre la limite maximale.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimiser les performances de vos serveurs Azure MySQL, Azure PostgreSQL et Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Corriger la sollicitation du processeur de vos serveurs Azure MySQL, Azure PostgreSQL et Azure MariaDB avec des goulots d’étranglement du processeur
Une utilisation très intense du processeur pendant une période prolongée peut ralentir les performances des requêtes pour votre charge de travail. L’augmentation de la taille du processeur aide à optimiser l’exécution des requêtes de base de données, ainsi qu’à améliorer les performances globales. Azure Advisor identifie les serveurs faisant une utilisation intense de processeur qui exécutent probablement des charges de travail limitées par celui-ci, et recommande une mise à l’échelle de votre capacité de calcul.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Réduire les contraintes de mémoire sur vos serveurs Azure MySQL, Azure PostgreSQL et Azure MariaDB, ou passer à une référence SKU à mémoire optimisée
Un taux d’accès au cache faible peut ralentir les performances de requête et augmenter les IOPS. Cela peut être dû à un mauvais plan de requête ou à l’exécution d’une charge de travail nécessitant beaucoup de mémoire. Une correction du plan de requête ou une  [augmentation de la mémoire](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) du serveur de base de données Azure Database pour PostgreSQL, du serveur de base de données Azure MySQL ou du serveur Azure MariaDB permet d’optimiser l’exécution de la charge de travail de la base de données. Azure Advisor identifie les serveurs affectés par cette variation importante du pool de tampons, et suggère de corriger le plan de requête, de passer à une référence SKU supérieure avec davantage de mémoire, ou d’augmenter la taille de la mémoire afin de pouvoir traiter davantage d’IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Utiliser un réplica en lecture Azure MySQL ou Azure PostgreSQL afin d’effectuer un scale-out des charges de travail intensives en lecture
Azure Advisor exploite des méthodes heuristiques basées sur la charge de travail, telles que le ratio lectures/écritures sur le serveur au cours des sept derniers jours, pour identifier les charges de travail intensives en lecture. Votre ressource de base de données Azure pour PostgreSQL ou de base de données Azure pour MySQL avec un ratio lectures/écritures très élevé peut entraîner des contentions de processeur ou de mémoire qui ralentissent les performances de requête. L’ajout d’un  [réplica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) vous aide à augmenter la taille des instances des lectures vers le serveur réplica, en évitant les contraintes de processeur ou de mémoire sur le serveur principal. Advisor identifie les serveurs traitant de telles charges de travail intenses en lectures et suggère l’ajout d’un  [réplica en lecture](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) pour alléger certaines charges de travail en lecture.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Mettre à l’échelle de votre serveur Azure MySQL, PostgreSQL Azure ou Azure MariaDB vers une référence SKU de niveau supérieur afin d’empêcher les contraintes de connexion
Chaque nouvelle connexion à votre serveur de base de données monopolise de la mémoire. Les performances du serveur de base de données se dégradent si les connexions à votre serveur échouent en raison d’une  [limite supérieure](https://docs.microsoft.com/azure/postgresql/concepts-limits) de la mémoire. Azure Advisor identifie les serveurs s’exécutant avec de nombreuses défaillances de connexion et recommande de mettre à niveau les limites de connexions de votre serveur afin de fournir davantage de mémoire à celui-ci en augmentant la puissance de calcul ou en utilisant des références SKU à mémoire optimisée offrant davantage de puissance de calcul par cœur.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Mettre à l’échelle votre cache vers une taille ou une référence SKU différentes afin d’améliorer les performances du cache et de l’application

Les instances de cache offrent de meilleures performances quand elles ne s’exécutent pas dans des conditions de forte sollicitation de la mémoire, de charge élevée du serveur ou de bande passante réseau élevée, qui peuvent avoir pour effet qu’elles cessent de répondre, perdent des données ou deviennent indisponibles. Advisor identifie les instances de cache présentant ces conditions, et suggère d’appliquer les meilleures pratiques pour réduire la pression sur la mémoire, la charge du serveur ou la bande passante réseau, ou d’adapter la taille ou d’utiliser une référence SKU offrant davantage de capacité.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Ajouter des régions avec du trafic vers votre compte Azure Cosmos DB

Azure Advisor détecte les comptes Azure Cosmos DB recevant du trafic en provenance d’une région non configurée, et suggère d’ajouter cette région. Cela a pour effet d’améliorer la latence des demandes en provenance de cette région et de garantir la disponibilité en cas de panne de la région. [En savoir plus sur la distribution de données mondiale avec Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Configurer votre stratégie d’indexation Azure Cosmos DB avec des chemins inclus ou exclus personnalisés

Azure Advisor identifie les conteneurs Cosmos DB qui utilisent la stratégie d’indexation par défaut, mais qui pourraient bénéficier d’une stratégie d’indexation personnalisée basée sur le modèle de charge de travail. La stratégie d’indexation par défaut indexe toutes les propriétés, mais l’utilisation d’une stratégie d’indexation personnalisée avec des chemins inclus ou exclus explicites utilisés dans les filtres de requêtes peut réduire les unités de requête et le stockage utilisés pour l’indexation. [En savoir plus sur la modification des stratégies d’index](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Définir la taille de page de votre requête Azure Cosmos DB (MaxItemCount) avec la valeur -1 

Azure Advisor identifie les conteneurs de base de données Azure Cosmos qui utilisent la taille de page de requête 100, et suggère d’utiliser la taille de page -1 pour accélérer les analyses. [En savoir plus sur le nombre maximal d’éléments](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Comment accéder aux recommandations en matière de performances dans le conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis ouvrez [Advisor](https://aka.ms/azureadvisordashboard).

2.  Dans le tableau de bord Advisor, cliquez sur l’onglet **Performances**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :

* [Présentation du conseiller](advisor-overview.md)
* [Prise en main d’Advisor](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-cost-recommendations.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
* [Recommandations d’excellence opérationnelle Advisor](advisor-operational-excellence-recommendations.md)
