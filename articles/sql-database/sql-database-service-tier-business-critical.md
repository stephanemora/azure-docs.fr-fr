---
title: Niveau de service Critique pour l’entreprise
description: En savoir plus sur le niveau Critique pour l’entreprise d’Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79232453"
---
# <a name="business-critical-tier---azure-sql-database"></a>Niveau Critique pour l'entreprise - Azure SQL Database

> [!NOTE]
> Le niveau Critique pour l’entreprise s’appelle Premium dans le modèle d’achat DTU. Pour obtenir une comparaison du modèle d’achat vCore avec le modèle d’achat DTU, consultez [Ressources et modèles d’achat Azure SQL Database](sql-database-purchase-models.md).

Azure SQL Database est basé sur une architecture de moteur de base de données SQL Server. Celle-ci est ajustée pour l’environnement cloud afin de garantir une disponibilité de 99,99 % même en cas de panne d’infrastructure. Trois modèles d’architecture sont utilisés dans Azure SQL Database :
- Usage général/Standard 
- Critique pour l’entreprise/Premium
- Hyperscale

Le modèle de niveau de service Premium/Critique pour l’entreprise est basé sur un cluster de processus de moteur de base de données. Ce modèle architectural s’appuie sur le fait qu’un quorum de nœuds de moteur de base de données est toujours disponible. Il n’a qu’un impact minime sur les performances de votre charge de travail, même durant les activités de maintenance.

Azure met à niveau et corrige le système d’exploitation sous-jacent, les pilotes et le moteur de base de données SQL Server en toute transparence avec un temps d’arrêt minimal pour les utilisateurs finaux. 

La disponibilité Premium, activée dans les niveaux de service Premium et Critique pour l’entreprise d’Azure SQL Database, est conçue pour les charges de travail intensives qui ne tolèrent aucun impact sur les performances résultant des opérations de maintenance en cours.

Dans le modèle Azure, la base de données Azure SQL intègre les opérations de calcul et stockage sur le nœud unique. Dans le cadre de ce modèle d’architecture, la haute disponibilité est obtenue par la réplication du calcul (processus du moteur de base de données SQL Server) et du stockage (disque SSD attaché localement). Elle est déployée dans un cluster à quatre nœuds avec une technologie similaire aux [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) de SQL Server.

![Cluster de nœuds de moteur de base de données](media/sql-database-managed-instance/business-critical-service-tier.png)

Le processus du moteur de base de données SQL et les fichiers mdf/ldf sous-jacents sont placés sur le même nœud, et le stockage SSD attaché localement permet à votre charge de travail de bénéficier d’une latence faible. La haute disponibilité est implémentée à l’aide d’une technologie similaire aux [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) SQL Server. Chaque base de données est un cluster de nœuds de base de données, avec une base de données primaire accessible à la charge de travail cliente et trois processus secondaires contenant les copies des données. Le nœud principal envoie (push) constamment les changements apportés aux nœuds secondaires afin de garantir la disponibilité des données sur les réplicas secondaires en cas de plantage du nœud principal, pour quelque raison que ce soit. Le basculement est géré par le moteur de base de données SQL Server : un réplica secondaire devient le nœud principal, et un réplica secondaire est créé pour garantir un nombre suffisant de nœuds dans le cluster. La charge de travail est automatiquement redirigée vers le nouveau nœud principal.

En outre, le cluster Critique pour l’entreprise dispose d’une fonctionnalité [Échelle horizontale en lecture ](sql-database-read-scale-out.md) intégrée qui fournit un nœud intégré en lecture seule gratuit. Ce dernier peut être utilisé pour exécuter des requêtes en lecture seule (des rapports, par exemple) qui ne doivent pas affecter les performances de votre charge de travail principale.

## <a name="when-to-choose-this-service-tier"></a>Quand choisir ce niveau de service ?

Le niveau de service Critique pour l’entreprise a été conçu pour les applications qui exigent des réponses à faible latence du stockage SSD sous-jacent (de 1 à 2 ms en moyenne), une récupération rapide en cas de défaillance de l’infrastructure sous-jacente, ou qui ont besoin de décharger des rapports, des analyses et des requêtes en lecture seule sur le réplica secondaire gratuit de la base de données primaire.

Les principales raisons pour lesquelles vous devez choisir le niveau de service Critique pour l’entreprise plutôt que le niveau Usage général sont les suivantes :
-   Exigences de latence faible pour les e/s : une charge de travail nécessitant une réponse rapide de la couche de stockage (1-2 millisecondes en moyenne) doit utiliser le niveau Critique pour l’entreprise. 
-   Communication fréquente entre l’application et la base de données. Les applications qui ne peuvent pas tirer parti de la mise en cache de la couche application ou du [traitement par lot des demandes](sql-database-use-batching-to-improve-performance.md) doivent envoyer de nombreuses requêtes SQL qui doivent être traitées rapidement peuvent bénéficier du niveau Critique pour l’entreprise.
-   Grand nombre de mises à jour : des opérations d’insertion, de mise à jour et de suppression modifient les pages de données en mémoire (page de modifications) qui doivent être enregistrées dans des fichiers de données avec l’opération `CHECKPOINT`. Un incident potentiel du processus du moteur de base de données ou un basculement de la base de données avec un grand nombre de pages de modifications peut augmenter le temps de récupération au niveau Usage général. Utilisez le niveau Critique pour l’entreprise si vous avez une charge de travail qui entraîne de nombreuses modifications en mémoire. 
-   Transactions de longue durée qui modifient les données. Les transactions qui sont ouvertes pour une durée plus longue empêchent la troncation du fichier journal, ce qui peut augmenter la taille du journal et le nombre de [fichiers journaux virtuels](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Un nombre élevé de fichiers journaux virtuels peut ralentir la récupération de la base de données après le basculement.
-   Charge de travail avec des requêtes de rapport et analytiques qui peuvent être redirigées vers le réplica secondaire disponible en lecture seule gratuit.
- Une résilience plus élevée et une récupération plus rapide des défaillances. En cas de défaillance du système, la base de données sur l’instance principale est désactivée et l’un des réplicas secondaires devient immédiatement la nouvelle base de données principale en lecture-écriture, prête à traiter les requêtes. Le moteur de base de données n’a pas besoin d’analyser et de rétablir des transactions à partir du fichier journal et de charger toutes les données dans la mémoire tampon.
- Protection avancée contre l’altération des données : le niveau Critique pour l’entreprise tire parti des réplicas de base de données en arrière-plan à des fins de continuité des activités, ce qui permet au service d’exploiter également la réparation de page automatique, la même technologie que celle utilisée pour la [mise en miroir et les groupes de disponibilité](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) des bases de données SQL Server. Dans le cas où un réplica ne peut pas lire une page en raison d’un problème d’intégrité des données, une nouvelle copie de la page sera extraite d’un autre réplica, remplaçant la page illisible sans perte de données ou temps d’arrêt du client. Cette fonctionnalité s’applique au niveau Usage général si la base de données a un réplica géo-secondaire.
- Disponibilité plus élevée : le niveau Critique pour l’entreprise dans la configuration à plusieurs zones de disponibilité garantit une disponibilité de 99,995 % par rapport aux 99,99 % du niveau Usage général.
- Géo-récupération rapide : le niveau Critique pour l’entreprise configuré avec la géo-réplication a un objectif de point de récupération garanti de 5 s et un objectif de délai de récupération de 30 secondes pour 100 % des heures déployées.

## <a name="next-steps"></a>Étapes suivantes

- Recherchez les caractéristiques des ressources (nombre de cœurs, E/S, mémoire) du niveau Critique pour l’entreprise dans [Instance managée](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), Base de données unique dans [Modèle vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) ou [Modèle DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), ou Pool élastique dans [Modèle vCore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) et [Modèle DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Découvrez les niveaux [Usage général](sql-database-service-tier-general-purpose.md) et [Hyperscale](sql-database-service-tier-hyperscale.md).
- Découvrez [Service Fabric](../service-fabric/service-fabric-overview.md).
- Pour découvrir d’autres options de haute disponibilité et de récupération d’urgence, consultez [Continuité d’activité](sql-database-business-continuity.md).
