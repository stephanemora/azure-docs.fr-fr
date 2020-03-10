---
title: Pools d’instance (préversion)
description: Cet article décrit les pools d’instances Azure SQL Database (préversion).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360380"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Que sont les pools d’instances SQL Database (préversion) ?

Les pools d’instances sont une nouvelle ressource dans Azure SQL Database qui offre un moyen pratique et économique de migrer des instances SQL plus petites vers le cloud à l’échelle.

Les pools d’instances vous permettent de préconfigurer les ressources de calcul en fonction de vos besoins de migration totaux. Vous pouvez ensuite déployer plusieurs instances gérées individuelles jusqu’à votre niveau de calcul préconfiguré. Par exemple, si vous préconfigurez 8 vCores, vous pouvez déployer deux instances de 2 vCores et une instance de 4 vCores, puis migrer les bases de données vers ces instances. Avant la disponibilité des pools d’instances, les charges de travail nécessitant beaucoup plus de ressources de calcul doivent souvent être consolidées dans une plus grande instance gérée lors de la migration vers le cloud. La nécessité de migrer des groupes de bases de données vers une grande instance exige généralement une planification minutieuse des capacités et une gouvernance des ressources, des considérations supplémentaires sur la sécurité et des tâches de consolidation des données supplémentaires au niveau de l’instance.

En outre, les pools d’instances prennent en charge l’intégration au réseau virtuel de façon native, ce qui vous permet de déployer plusieurs pools d’instances et plusieurs instances uniques dans le même sous-réseau.


## <a name="key-capabilities-of-instance-pools"></a>Principales caractéristiques des pools d’instances

La fonctionnalité Pools d’instances permet de bénéficier des avantages suivants :

1. Possibilité d’héberger des instances de 2 vCore. *\*Uniquement pour les instances dans les pools d’instances*.
2. Temps de déploiement d’instances prévisible et rapide (jusqu’à 5 minutes).
3. Allocation minimale d’adresses IP.

Le diagramme suivant illustre un pool d’instances avec plusieurs instances déployées dans un sous-réseau de réseau virtuel.

![Pool d’instances avec plusieurs instances](./media/sql-database-instance-pools/instance-pools1.png)

Les pools d’instances permettent le déploiement de plusieurs instances sur la même machine virtuelle, où la taille de calcul de la machine virtuelle est basée sur le nombre total de vCores alloués pour le pool. Cette architecture permet de *partitionner* la machine virtuelle en plusieurs instances, qui peuvent être de toute taille prise en charge, y compris 2 vCores (les instances à 2 vCore sont uniquement disponibles pour les instances dans des pools).

Les opérations de gestion sur les instances d’un pool sont beaucoup plus rapides une fois le pool déployé initialement. Ces opérations sont plus rapides, car le déploiement ou l’extension d’un [cluster virtuel](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (ensemble dédié de machines virtuelles) ne fait pas partie de la configuration de l’instance gérée.

Étant donné que toutes les instances d’un pool partagent la même machine virtuelle, l’allocation d’adresses IP totale ne dépend pas du nombre d’instances déployées, ce qui est pratique pour le déploiement dans les sous-réseaux avec des plages d’adresses IP étroites.

Chaque pool a une allocation fixe d’adresses IP de neuf adresses IP seulement (sans compter les cinq adresses IP du sous-réseau qui sont réservées à ses propres besoins). Pour plus d’informations, consultez [Exigences de taille de sous-réseau pour les instances uniques](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Scénarios d’application pour les pools d’instances

La liste suivante répertorie les principaux cas d’utilisation où les pools d’instances doivent être envisagés :

- La migration *d’un groupe d’instances SQL* en même temps, où la majorité est de petite taille (par exemple 2 ou 4 vCores).
- Les scénarios dans lesquels la *création ou la mise à l’échelle d’instances de façon prévisible et rapide* est importante. Par exemple, il peut s’agir du déploiement d’un nouveau locataire dans un environnement d’application SaaS mutualisée qui requiert des fonctionnalités au niveau de l’instance.
- Les scénarios où un *coût fixe* ou une *limite de dépense* sont importants. Par exemple, si vous exécutez des environnements de développement-test ou de démonstration partagés d’une taille fixe (ou rarement modifiée), où vous déployez régulièrement des instances gérées quand cela est nécessaire.
- Scénarios où *une allocation minimale d’adresses IP* dans un sous-réseau de réseau virtuel est importante. Toutes les instances d’un pool partagent une machine virtuelle ; ainsi, le nombre d’adresses IP allouées est inférieur dans le cas d’instances uniques.


## <a name="architecture-of-instance-pools"></a>Architecture des pools d’instances

Les pools d’instances ont une architecture similaire aux instances gérées standard (*instances uniques*). Pour prendre en charge les  [déploiements dans les réseaux virtuels Azure](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks)  et assurer l’isolation et la sécurité des clients, les pools d'instances s’appuient sur des  [clusters virtuels](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). Les clusters virtuels représentent un ensemble dédié de machines virtuelles isolées déployées dans le sous-réseau du réseau virtuel du client.

La principale différence entre les deux modèles de déploiement est que les pools d’instances permettent plusieurs déploiements de processus SQL Server sur le même nœud de machine virtuelle, qui sont des ressources régies par des [objets de traitement Windows](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), alors que les instances uniques sont toujours seules sur un nœud de machine virtuelle.

Le diagramme suivant montre un pool d’instances et deux instances individuelles déployées dans le même sous-réseau, et illustre les principaux détails de l’architecture pour les deux modèles de déploiement :

![Pool d’instances et deux instances individuelles](./media/sql-database-instance-pools/instance-pools2.png)

Chaque pool d’instances crée un cluster virtuel distinct sous-jacent. Les instances d’un pool et les instances uniques déployées dans le même sous-réseau ne partagent pas les ressources de calcul allouées aux processus SQL Server et aux composants de passerelle, ce qui garantit la prévisibilité des performances.

## <a name="instance-pools-resource-limitations"></a>Limitations de ressources des pools d’instances

Il existe plusieurs limitations de ressources concernant les pools d’instances et les instances dans des pools :

- Les pools d’instances sont disponibles uniquement sur le matériel Gen5.
- Les instances d’un pool disposent d’un processeur et d’une RAM dédiés ; ainsi le nombre agrégé de vCores sur toutes les instances doit être inférieur ou égal au nombre de vCores alloués au pool.
- Toutes [les limites de niveau d’instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) s’appliquent aux instances créées au sein d’un pool.
- Outre les limites au niveau de l’instance, il existe également deux limites imposées *au niveau du pool d’instances* :
  - Taille de stockage totale par pool (8 To).
  - Nombre total de bases de données par pool (100).

L’allocation de stockage totale et le nombre de bases de données sur toutes les instances doivent être inférieurs ou égaux aux limites exposées par les pools d’instances.

- Les pools d’instances prennent en charge 8, 16, 24, 32, 40, 64 et 80 vCores.
- Les instances gérées dans les pools prennent en charge 2, 4, 8, 16, 24, 32, 40, 64 et 80 vCores.
- Les instances gérées dans les pools prennent en charge des tailles de stockage comprises entre 32 Go et 8 To, sauf dans les cas suivants :
  - les instances à 2 vCores prennent en charge des tailles comprises entre 32 Go et 640 Go
  - les instances à 4 vCores prennent en charge des tailles comprises entre 32 Go et 2 To

La [propriété de niveau de service](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) est associée à la ressource de pool d’instances, de sorte que toutes les instances d’un pool doivent être du même niveau de service que le niveau de service du pool. À ce stade, seul le niveau de service Usage général est disponible (consultez la section suivante sur les limitations de la préversion actuelle).

### <a name="public-preview-limitations"></a>Limitations de la version préliminaire publique

La préversion publique présente les limitations suivantes :

- Actuellement, seul le niveau de service Usage général est disponible.
- Les pools d’instances ne peuvent pas être mis à l’échelle au cours de la préversion publique ; ainsi, la planification de capacité est importante avant le déploiement.
- La prise en charge du portail Azure pour la création et la configuration du pool d’instances n’est pas encore disponible. Toutes les opérations sur les pools d’instances sont uniquement prises en charge par le biais de PowerShell. Le déploiement d’instance initial dans un pool créé au préalable est également pris en charge uniquement via PowerShell. Une fois déployées dans un pool, les instances gérées peuvent être mises à jour à l’aide du portail Azure.
- Les instances gérées créées en dehors du pool ne peuvent pas être déplacées dans un pool existant, et les instances créées à l’intérieur d’un pool ne peuvent pas être déplacées en dehors d’une instance unique ou vers un autre pool.
- La tarification des instances réservées (licence incluse ou avec Azure Hybrid Benefit) n’est pas disponible.

## <a name="sql-features-supported"></a>Fonctionnalités SQL prises en charge

Les instances créées dans des pools prennent en charge les mêmes [niveaux de compatibilité et fonctionnalités prises en charge que les instances gérées uniques](sql-database-managed-instance.md#sql-features-supported).

Chaque instance gérée déployée dans un pool a une instance d’agent SQL distincte.

Les fonctionnalités facultatives qui nécessitent que vous choisissiez des valeurs spécifiques (telles que le classement au niveau de l’instance, le fuseau horaire, le point de terminaison public pour le trafic de données, les groupes de basculement) sont configurées au niveau de l’instance et peuvent être différentes pour chaque instance d’un pool.

## <a name="performance-considerations"></a>Considérations relatives aux performances

Bien que les instances gérées au sein des pools disposent de vCores et de RAM dédiés, elles partagent le disque local (pour l’utilisation de tempdb) et les ressources réseau. Si cela est peu probable, il est possible d’observer un effet de *voisin bruyant* si plusieurs instances du pool ont une consommation élevée des ressources en même temps. Si vous observez ce comportement, envisagez de déployer ces instances dans un pool plus grand ou en tant qu’instances uniques.

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Étant donné que les instances déployées dans un pool partagent la même machine virtuelle, vous pouvez envisager de désactiver les fonctionnalités qui introduisent des risques de sécurité plus élevés ou contrôler fermement les autorisations d’accès à ces fonctionnalités. Par exemple, l’intégration du CLR, la sauvegarde et la restauration natives, la messagerie de base de données, etc.

## <a name="instance-pool-support-requests"></a>Demandes de support pour les pools d’instances

Créez et gérez les demandes de support pour les pools d’instances dans le [portail Azure](https://portal.azure.com).

Si vous rencontrez des problèmes liés au déploiement d’un pool d’instances (création ou suppression), assurez-vous de spécifier **Pools d’instances** dans le champ **Sous-type de problème**.

![Demande de support pour les pools d’instances](./media/sql-database-instance-pools/support-request.png)

Si vous rencontrez des problèmes liés à des instances uniques ou à des bases de données au sein d’un pool, vous devez créer un ticket de support standard pour les instances gérées Azure SQL Database.

Pour créer des déploiements d’instances gérées plus grands (avec ou sans pools d’instances), vous devrez peut-être obtenir un quota régional plus grand. Pour plus d’informations, consultez [Demander des augmentations de quota pour Azure SQL Database](quota-increase-request.md). Notez que si vous utilisez des pools d’instances, la logique de déploiement compare la consommation totale de vCores *au niveau du pool* à votre quota pour déterminer si vous êtes autorisé à créer des ressources sans augmenter davantage votre quota.

## <a name="instance-pool-billing"></a>Facturation des pools d’instances

Les pools d’instances permettent de mettre à l’échelle le calcul et le stockage indépendamment. Les clients paient le calcul associé à la ressource de pool mesurée en vCores et le stockage associé à chaque instance, mesuré en gigaoctets (les 32 premiers Go sont gratuits pour chaque instance).

Le prix des vCores pour un pool est facturé quel que soit le nombre d’instances déployées dans ce pool.

Pour le prix du calcul (mesuré en vCores), deux options de tarification sont disponibles :

  1. *Licence incluse* : Le prix des licences SQL est inclus. Cette option est destinée aux clients qui décident de ne pas appliquer de licences SQL Server existantes avec Software Assurance.
  2. *Azure Hybrid Benefit* : Un prix réduit incluant Azure Hybrid Benefit pour SQL Server. Les clients peuvent choisir cette tarification en utilisant leurs licences SQL Server existantes avec Software Assurance. Pour obtenir des informations sur l’éligibilité et d’autres informations, consultez [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

La définition d’options de tarification différentes n’est pas possible pour les instances individuelles d’un pool. Toutes les instances du pool parent doivent être au prix de la licence incluse ou au prix d’Azure Hybrid Benefit. Le modèle de licence du pool peut être modifié après la création du pool.

> [!IMPORTANT]
> Si vous spécifiez un modèle de licence pour l’instance qui est différent de celui du pool, le prix du pool est utilisé et la valeur de niveau d’instance est ignorée.

Si vous créez des pools d’instances sur des [abonnements éligibles à l’avantage dev-test](https://azure.microsoft.com/pricing/dev-test/), vous bénéficiez automatiquement de tarifs réduits jusqu’à 55 % sur les instances gérées Azure SQL.

Pour plus d’informations sur la tarification des pools d’instances, reportez-vous à la section *pools d’instances* sur la [page de tarification des instances gérées](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec les pools d’instances, consultez le [Guide de procédures des pools d’instances SQL Database](sql-database-instance-pools-how-to.md).
- Pour savoir comment créer votre première instance managée, consultez le [Guide de démarrage rapide](sql-database-managed-instance-get-started.md).
- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
- Pour plus d’informations sur la configuration du réseau virtuel, consultez [Configuration de réseau virtuel d’une instance managée](sql-database-managed-instance-connectivity-architecture.md).
- Pour obtenir un guide de démarrage rapide qui crée une instance managée et restaure une base de données à partir d’un fichier de sauvegarde, consultez [Créer une instance managée](sql-database-managed-instance-get-started.md).
- Pour un tutoriel utilisant le service Azure Database Migration Service (DMS) pour la migration, consultez [Migration d’une instance managée à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pour une supervision avancée des performances de base de données Managed Instance avec informations de dépannage intégrées, consultez [Surveiller Azure SQL Database avec Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Pour des informations sur les prix, consultez [Tarifs des instances managées SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
