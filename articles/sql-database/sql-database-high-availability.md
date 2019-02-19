---
title: 'Haute disponibilité : service Azure SQL Database | Microsoft Docs'
description: En savoir plus sur les fonctionnalités de haute disponibilité du service Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b58c3cc677291c11b93cff439bd669c58735f31e
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892828"
---
# <a name="high-availability-and-azure-sql-database"></a>Haute disponibilité et Azure SQL Database

Azure SQL Database un service de base de données de type PaaS (Platform as a Service) hautement disponible qui garantit une disponibilité dans le temps de votre base de données de 99,99 %, éliminant tout souci lié à la maintenance et aux temps d’arrêt. Ce processus du moteur de base de données SQL Server, entièrement géré et hébergé dans le cloud Azure, garantit que votre base de données SQL Server est toujours mise à niveau/corrigée sans que votre charge de travail ne soit affectée. Quand un correctif est appliqué à une instance ou quand une instance bascule, le temps d’arrêt n’est généralement pas perceptible si vous [utilisez une logique de nouvelle tentative](sql-database-develop-overview.md#resiliency) dans votre application. Si le temps de basculement est supérieur à 60 secondes, vous devriez ouvrir un cas de support. Pour assurer la disponibilité de vos données, Azure SQL Database bénéficie de fonctionnalités de récupération rapide, même dans les situations les plus critiques.

La plateforme Azure gère entièrement chaque base de données SQL Azure et garantit un pourcentage élevé de disponibilité des données, sans aucune perte. Azure gère automatiquement les tâches suivantes : mises à jour correctives, sauvegardes, réplication, détection des éventuelles défaillances matérielles, logicielles ou réseau sous-jacentes, déploiement de correctifs de bogues, basculements, mises à niveau de base de données et autres tâches de maintenance. Les ingénieurs de SQL Server ont implémenté les bonnes pratiques connues pour que la durée d’exécution des opérations de maintenance représente moins de 0,01 % de la durée de vie de votre base de données. Cette architecture est conçue de manière à éliminer tout risque de perte des données validées et à mener à bien les opérations de maintenance sans affecter la charge de travail. Rien, pas même une fenêtre de maintenance ou un temps d’arrêt, ne doit vous obliger à arrêter la charge de travail pendant la mise à niveau ou la maintenance de la base de données. La haute disponibilité intégrée à Azure SQL Database garantit que cette base de données ne constitue jamais un point de défaillance unique dans votre architecture logicielle.

Azure SQL Database est basé sur une architecture de moteur de base de données SQL Server. Celle-ci est ajustée pour l’environnement cloud afin de garantir une disponibilité de 99,99 % même en cas de panne d’infrastructure. Azure SQL Database utilise deux modèles architecturaux haute disponibilité (les deux garantissant une disponibilité de 99,99 %) :

- Le modèle de niveau de service Standard/Usage général est basé sur la séparation du calcul et du stockage. Ce modèle architectural s’appuie sur la haute disponibilité et la haute fiabilité du niveau de stockage. Cependant, il peut faire l’objet d’une détérioration des performances durant les activités de maintenance.
- Le modèle de niveau de service Premium/Critique pour l’entreprise est basé sur un cluster de processus de moteur de base de données. Ce modèle architectural s’appuie sur le fait qu’un quorum de nœuds de moteur de base de données est toujours disponible. Il n’a qu’un impact minime sur les performances de votre charge de travail, même durant les activités de maintenance.

Azure met à niveau et corrige le système d’exploitation sous-jacent, les pilotes et le moteur de base de données SQL Server en toute transparence avec un temps d’arrêt minimal pour les utilisateurs finaux. Azure SQL Database s’exécutant sur la dernière version stable du moteur de base de données SQL Server et du système d’exploitation Windows, la plupart des utilisateurs ne se rendent pas compte que les mises à niveau sont effectuées en continu.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilité des niveaux de service De base, Standard et Usage général

La disponibilité Standard fait référence au contrat SLA garantissant une disponibilité de 99,99 % appliqué dans les niveaux de service De base, Standard et Usage général. Dans le cadre de ce modèle architectural, la haute disponibilité est obtenue par la séparation des couches de calcul et de stockage et la réplication des données dans le niveau de stockage.

L’illustration suivante montre quatre nœuds dans un modèle architectural Standard avec les couches de calcul et de stockage séparées.

![Séparation du calcul et du stockage](media/sql-database-managed-instance/general-purpose-service-tier.png)

Le modèle de disponibilité Standard comprend deux couches :

- Une couche de calcul sans état qui exécute le processus `sqlserver.exe` et contient uniquement des données transitoires et mises en cache (par exemple : cache du plan, pool de mémoires tampons, pool de stockage de colonnes). Ce nœud SQL Server sans état est géré par Azure Service Fabric qui initialise le processus, contrôle l’intégrité du nœud et effectue le basculement vers un autre emplacement si nécessaire.
- Une couche de données avec état comprenant les fichiers de base de données (.mdf/.ldf) stockés dans le service Stockage Blob Azure. Le service Stockage Blob Azure garantit qu'aucun enregistrement placé dans un fichier de base de données ne subira de perte de données. Le service Stockage Blob Azure est doté de fonctionnalités intégrées de redondance et de disponibilité des données qui garantissent la préservation des enregistrements d'un fichier journal ou des pages d'un fichier de données, même en cas de blocage du processus SQL Server.

Dès que le moteur de base de données ou le système d’exploitation est mis à niveau, qu’une partie de l’infrastructure sous-jacente est défaillante ou qu’un problème critique est détecté dans le processus SQL Server, Azure Service Fabric déplace le processus SQL Server sans état vers un autre nœud de calcul sans état. Afin de réduire le temps de basculement, un ensemble de nœuds de réserve se tient prêt à exécuter le nouveau service de calcul en cas de basculement. Les données stockées dans le service Stockage Blob Azure ne sont pas affectées, et les fichiers de données/journaux sont joints au processus SQL Server nouvellement initialisé. Ce processus garantit une disponibilité de 99,99 %, mais il peut impacter les performances des lourdes charges de travail en cours d’exécution, et ce, en raison des délais de transition et du fait que le nouveau nœud SQL Server démarre avec un cache à froid.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilité des niveaux de service Premium et Critique pour l’entreprise

La disponibilité Premium, activée dans les niveaux de service Premium et Critique pour l’entreprise de Azure SQL Database, est conçue pour les charges de travail intensives qui ne tolèrent aucun impact sur les performances résultant des opérations de maintenance en cours.

Dans le modèle Azure, Azure SQL Database intègre les opérations de calcul et stockage sur le nœud unique. Dans le cadre de ce modèle architectural, la haute disponibilité est obtenue par la réplication du calcul (processus du moteur de base de données SQL Server) et du stockage (disque SSD attaché localement). Elle est déployée dans un cluster à 4 nœuds avec une technologie similaire aux [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) SQL Server.

![Cluster de nœuds de moteur de base de données](media/sql-database-managed-instance/business-critical-service-tier.png)

Le processus du moteur de base de données SQL et les fichiers mdf/ldf sous-jacents sont placés sur le même nœud, et le stockage SSD attaché localement permet à votre charge de travail de bénéficier d’une latence faible. La haute disponibilité est implémentée à l’aide d’une technologie similaire aux [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) SQL Server. Chaque base de données est un cluster de nœuds de base de données, avec une base de données primaire accessible à la charge de travail cliente et trois processus secondaires contenant les copies des données. Le nœud principal envoie (push) constamment les changements apportés aux nœuds secondaires afin de garantir la disponibilité des données sur les réplicas secondaires en cas de plantage du nœud principal, pour quelque raison que ce soit. Le basculement est géré par Azure  Service Fabric : un réplica secondaire devient le nœud principal, et un réplica secondaire est créé pour garantir un nombre suffisant de nœuds dans le cluster. La charge de travail est automatiquement redirigée vers le nouveau nœud principal.

En outre, le cluster Critique pour l’entreprise dispose d’une fonctionnalité [Échelle horizontale en lecture ](sql-database-read-scale-out.md) intégrée qui fournit un nœud intégré en lecture seule gratuit. Ce dernier peut être utilisé pour exécuter des requêtes en lecture seule (des rapports, par exemple) qui ne doivent pas affecter les performances de votre charge de travail principale.

## <a name="zone-redundant-configuration"></a>Configuration de zone redondante

Par défaut, les réplicas du jeu de quorum pour les configurations de stockage local sont créés dans le même centre de données. Avec l’introduction des [Zones de disponibilité Azure](../availability-zones/az-overview.md), vous avez la possibilité de placer les différents réplicas dans les jeux de quorum sur des zones de haute disponibilité distinctes dans la même région. Pour éliminer un point de défaillance unique, l’anneau de contrôle est également dupliqué sur plusieurs fuseaux horaires sous forme de trois anneaux de passerelle (GW). Le routage vers un anneau de passerelle spécifique est contrôlé par [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Étant donné que la configuration redondante interzone ne crée pas de redondance de base de données supplémentaire, l’utilisation de Zones de disponibilité dans les niveaux de service Premium ou Critique pour l’entreprise est disponible sans frais supplémentaires. En sélectionnant une base de données redondante dans une zone, vous pouvez rendre vos bases de données Premium ou Critique pour l’entreprise résistantes à un plus grand éventail d’échecs, notamment les pannes graves de centre de données, sans aucune modification de la logique d’application. Vous pouvez également convertir vos bases de données ou pools Premium ou Critique pour l’entreprise en configuration avec redondance dans une zone.

L’ensemble du jeu de quorums de redondance de zone ayant des réplicas dans différents centres de données avec une certaine distance entre eux, la latence accrue du réseau peut augmenter le temps de validation et ainsi avoir un impact sur les performances de certaines charges de travail OLTP. Vous pouvez toujours revenir à la configuration de zone unique en désactivant le paramètre de redondance de zone. Ce processus dépend de la taille des données et il est semblable à la mise à jour des niveaux de service ordinaires. À la fin du processus, la base de données ou le pool est migré à partir d’un anneau de redondance de zone vers un anneau de zone unique, ou vice versa.

> [!IMPORTANT]
> Pour le moment, les bases de données avec redondance de zone et les pools élastiques sont uniquement pris en charge dans le niveau de service Premium. Par défaut, les sauvegardes et enregistrements d’audit sont stockés dans le stockage RA-GRS, et ne sont donc pas automatiquement disponibles en cas d’une panne à l’échelle de la zone. 

La version avec redondance de zone de l’architecture de haute disponibilité est illustrée dans le diagramme suivant :

![architecture haute disponibilité avec redondance de zone](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="accelerated-database-recovery-adr"></a>Récupération de base de données accélérée (ADR)

La [récupération de base de données accélérée](sql-database-accelerated-database-recovery.md) est une nouvelle fonctionnalité du moteur de base de données SQL qui améliore considérablement la disponibilité des bases de données, en particulier en présence de transactions d’une durée d’exécution longue, grâce à une nouvelle conception du processus de récupération du moteur de base de données SQL. La récupération de base de données accélérée est actuellement disponible pour les bases de données uniques, les pools élastiques et Azure SQL Data Warehouse.

## <a name="conclusion"></a>Conclusion

Azure SQL Database est étroitement intégré à la plateforme Azure et dépend très largement de Service Fabric pour la détection et la récupération des défaillances, mais aussi du service Stockage Blob Azure pour la protection des données et des Zones de disponibilité pour une meilleure tolérance aux pannes. Parallèlement à cela, Azure SQL Database utilise pleinement la technologie des groupes de disponibilité Always On intégrée à SQL Server pour la réplication et le basculement. La combinaison de ces technologies permet aux applications de profiter pleinement des avantages d’un modèle de stockage mixte et de prendre en charge les contrats de niveau de service les plus exigeants.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Zones de disponibilité Azure](../availability-zones/az-overview.md)
- En savoir plus sur [Service Fabric](../service-fabric/service-fabric-overview.md)
- En savoir plus sur [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Pour découvrir plus d’options concernant la haute disponibilité et la reprise d’activité après sinistre, consultez [Continuité d’activité](sql-database-business-continuity.md).
