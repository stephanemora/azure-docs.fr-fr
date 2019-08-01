---
title: Niveau Critique pour l’entreprise - Service Azure SQL Database | Microsoft Docs
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
ms.openlocfilehash: 208224e10c3acfb17bc5fd89d2d66152943811dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566758"
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

## <a name="next-steps"></a>Étapes suivantes

- Recherchez les caractéristiques des ressources (nombre de cœurs, E/S, mémoire) du niveau Critique pour l’entreprise dans [Instance managée](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), Base de données unique dans [Modèle vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute-tier) ou [Modèle DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), ou Pool élastique dans [Modèle vCore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) et [Modèle DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Découvrez les niveaux [Usage général](sql-database-service-tier-general-purpose.md) et [Hyperscale](sql-database-service-tier-hyperscale.md).
- Découvrez [Service Fabric](../service-fabric/service-fabric-overview.md).
- Pour découvrir d’autres options de haute disponibilité et de récupération d’urgence, consultez [Continuité d’activité](sql-database-business-continuity.md).
