---
title: Améliorer les performances des applications Azure avec Azure Advisor | Microsoft Docs
description: Utilisez Advisor pour optimiser les performances de vos déploiements Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 6cca6692da37714c76f5241ed14e24c967b00563
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467695"
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
> Les alertes classiques d'Azure Monitor seront mises hors service en juin 2019. Nous vous recommandons de mettre à niveau votre compte de stockage classique pour utiliser Resource Manager afin de conserver la fonctionnalité d'alerte sur la nouvelle plateforme. Pour plus d’informations, consultez [Suppression des alertes classiques](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Concevez vos comptes de stockage pour éviter d’atteindre la limite maximale d’abonnements

Une région Azure peut prendre en charge un maximum de 250 comptes de stockage par abonnement. Une fois que la limite est atteinte, il se peut que vous ne pourrez pas créer des comptes de stockage plus dans cette combinaison de région/un abonnement. L’Assistant vérifie vos abonnements et surfaces recommandations pour vous aider à concevoir des comptes de stockage moins pour ceux qui sont proches d’atteindre la limite maximale.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimiser les performances de vos serveurs MySQL de Azure, Azure PostgreSQL et Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Corriger la pression de l’UC de vos serveurs MySQL de Azure, Azure PostgreSQL et Azure MariaDB avec les goulots d’étranglement du processeur
Une utilisation très élevée du processeur sur une période prolongée peut entraîner des performances de requêtes pour votre charge de travail. Augmentation de la taille de l’UC pour aider à optimiser l’exécution des requêtes de base de données et améliorer les performances globales. Azure Advisor identifie les serveurs avec un utilisation élevée du processeur qui exécutent probablement des charges de travail de contrainte de processeur et recommande la mise à l’échelle de votre calcul.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Réduire les contraintes de mémoire sur vos serveurs MySQL de Azure, Azure PostgreSQL et Azure MariaDB ou passer à une mémoire optimisé de référence (SKU)
Un taux d’accès au cache faible peut entraîner une baisse des performances de requête et les e/s accrue. Cela peut être dû à un mauvais plan de requête ou de l’exécution d’une charge de travail gourmandes en mémoire. Correction du plan de requête ou [augmentation de la mémoire](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) de la base de données Azure pour le serveur de base de données PostgreSQL, serveur de base de données Azure MySQL ou MariaDB d’Azure server aidera à optimiser l’exécution de la charge de travail de base de données. Azure Advisor identifie les serveurs affectés en raison de cet évolution du pool de mémoire tampon haute et recommande la résolution du plan de requête, déplacement vers un SKU supérieur avec davantage de mémoire ou l’augmentation de taille de stockage pour obtenir plus d’IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Utiliser un Azure MySQL ou un réplica de lecture Azure PostgreSQL pour monter en charge de lectures pour les charges de travail intensives en lecture
Azure Advisor s’appuie sur l’heuristique de basée sur la charge de travail telles que le taux de lectures et écritures sur le serveur au cours des sept jours pour identifier les charges de travail gourmandes en lecture. Votre Azure database pour PostgreSQL ressource ou la base de données Azure pour la ressource de MySQL avec un taux très élevé de lectures/écritures peut entraîner des conflits du processeur ou de la mémoire conduisant à ralentir les performances de requête. Ajout d’un [réplica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) vous aidera à la montée en charge les lectures vers le serveur réplica, empêchant les contraintes de processeur ou de la mémoire sur le serveur principal. Advisor identifie les serveurs avec ces charges de travail gourmandes en lecture et vous recommandons d’ajouter un [réplica en lecture](https://docs.microsoft.com/en-us/azure/postgresql/concepts-read-replicas) pour décharger les charges de travail de lecture.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Mettre à l’échelle de votre serveur Azure MySQL, PostgreSQL Azure ou Azure MariaDB vers une référence SKU plus élevé pour empêcher les contraintes de connexion
Chaque nouvelle connexion à votre serveur de base de données occupe la partie de la mémoire. Si l’échec des connexions à votre serveur fait de dégradent les performances du serveur de base de données un [limite supérieure](https://docs.microsoft.com/en-us/azure/postgresql/concepts-limits) en mémoire. Azure Advisor identifie les serveurs en cours d’exécution avec nombreux échecs de connexion et recommandons des limites de connexions de votre serveur pour fournir davantage de mémoire à votre serveur par la mise à l’échelle de calcul ou à l’aide de la mémoire optimisé références (SKU), qui ont un calcul plus par cœur de la mise à niveau.

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Comment accéder aux recommandations en matière de performances dans le conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis ouvrez [Advisor](https://aka.ms/azureadvisordashboard).

2.  Dans le tableau de bord Advisor, cliquez sur l’onglet **Performances**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :

* [Présentation du conseiller](advisor-overview.md)
* [Prise en main d’Advisor](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
