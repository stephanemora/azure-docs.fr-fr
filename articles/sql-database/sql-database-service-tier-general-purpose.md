---
title: Niveau de service universel
description: En savoir plus sur le niveau Usage général d’Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79228517"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Niveau de service Usage général : Azure SQL Database

> [!NOTE]
> Le niveau de service Usage général du modèle d’achat vCore correspond au niveau de service Standard du modèle d’achat DTU. Pour obtenir une comparaison du modèle d’achat vCore avec le modèle d’achat DTU, consultez [Ressources et modèles d’achat Azure SQL Database](sql-database-purchase-models.md).

Azure SQL Database est basé sur une architecture de moteur de base de données SQL Server. Celle-ci est adaptée à l’environnement cloud afin de garantir une disponibilité de 99,99 % même en cas de panne d’infrastructure. Trois niveaux de service sont utilisés dans Azure SQL Database, chacun doté de modèles architecturaux distincts. Ces niveaux de service sont les suivants :

- Usage général
- Critique pour l'entreprise
- Hyperscale

Le modèle architectural du niveau de service Usage général est basé sur la séparation du calcul et du stockage. Ce modèle architectural s’appuie sur la haute disponibilité et la fiabilité du Stockage Blob Azure qui réplique les fichiers de base de données de façon transparente et qui garantit l’absence de perte de données en cas de panne de l’infrastructure sous-jacente.

L’illustration suivante montre quatre nœuds dans un modèle architectural Standard avec les couches de calcul et de stockage séparées.

![Séparation du calcul et du stockage](media/sql-database-managed-instance/general-purpose-service-tier.png)

Le modèle architectural correspondant au niveau de service Usage général présente deux couches :

- Une couche de calcul sans état qui exécute le processus `sqlservr.exe` et contient uniquement des données transitoires et mises en cache (par exemple : cache du plan, pool de mémoires tampons, pool de stockage de colonnes). Ce nœud SQL Server sans état est géré par Azure Service Fabric qui initialise le processus, contrôle l’intégrité du nœud et effectue le basculement vers un autre emplacement si nécessaire.
- Une couche de données avec état comprenant les fichiers de base de données (.mdf/.ldf) stockés dans le service Stockage Blob Azure. Le service Stockage Blob Azure garantit qu'aucun enregistrement placé dans un fichier de base de données ne subira de perte de données. Stockage Azure est doté de fonctionnalités intégrées de redondance et de disponibilité des données qui garantissent la préservation des enregistrements d’un fichier journal ou des pages d’un fichier de données, même en cas de plantage du processus SQL Server.

Dès que le moteur de base de données ou le système d’exploitation est mis à niveau, qu’une partie de l’infrastructure sous-jacente est défaillante ou qu’un problème critique est détecté dans le processus SQL Server, Azure Service Fabric déplace le processus SQL Server sans état vers un autre nœud de calcul sans état. Afin de réduire le temps de basculement, un ensemble de nœuds de réserve se tient prêt à exécuter le nouveau service de calcul en cas de basculement du nœud principal. Les données dans la couche Stockage Azure ne sont pas affectées, et les fichiers de données/journaux sont attachés à des processus SQL Server nouvellement initialisés. Ce processus garantit une disponibilité de 99,99 %, mais il peut impacter les performances des lourdes charges de travail en cours d’exécution, et ce, en raison des délais de transition et du fait que le nouveau nœud SQL Server démarre avec un cache à froid.

## <a name="when-to-choose-this-service-tier"></a>Quand choisir ce niveau de service ?

Le niveau de service Usage général est un niveau de service par défaut dans Azure SQL Database conçu pour la plupart des charges de travail génériques. Si vous avez besoin d’un moteur de base de données complètement managé, avec un SLA de 99,99 % et une latence de stockage comprise entre 5 et 10 ms, adapté à SQL Azure IaaS dans la majorité des cas, choisissez le niveau Usage général.

## <a name="next-steps"></a>Étapes suivantes

- Recherchez les caractéristiques des ressources (nombre de cœurs, E/S, mémoire) du niveau Usage général/Standard dans [Managed Instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), Base de données unique dans [Modèle vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) ou [Modèle DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes), ou Pool élastique dans [Modèle vCore](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) et [Modèle DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Découvrez les niveaux [Critique pour l’entreprise](sql-database-service-tier-business-critical.md) et [Hyperscale](sql-database-service-tier-hyperscale.md).
- Découvrez [Service Fabric](../service-fabric/service-fabric-overview.md).
- Pour découvrir d’autres options de haute disponibilité et de récupération d’urgence, consultez [Continuité d’activité](sql-database-business-continuity.md).
