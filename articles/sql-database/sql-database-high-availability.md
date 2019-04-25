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
ms.author: sashan
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392650"
---
# <a name="high-availability-and-azure-sql-database"></a>Haute disponibilité et Azure SQL Database

L’objectif de l’architecture de haute disponibilité dans Azure SQL Database est garantie que votre base de données est opérationnel et en cours d’exécution 99,99 % du temps, sans vous soucier de l’impact des opérations de maintenance et aux pannes. Azure gère automatiquement les tâches de maintenance critiques, comme mise à jour corrective, sauvegardes, mises à jour Windows et SQL, ainsi que les événements non planifiés telles que des défaillances de matériel, logiciel ou réseau sous-jacent.  Lorsque l’instance SQL sous-jacente est corrigée ou bascule, le temps d’arrêt n’est pas perceptible si vous [employer la logique de nouvelle tentative](sql-database-develop-overview.md#resiliency) dans votre application. Pour assurer la disponibilité de vos données, Azure SQL Database bénéficie de fonctionnalités de récupération rapide, même dans les situations les plus critiques.

La solution de haute disponibilité est conçue pour vous assurer que les données validées ne sont jamais perdues en raison d’échecs, que les opérations de maintenance n’affectent pas votre charge de travail, et que la base de données ne seront pas un point unique de défaillance dans votre architecture logicielle. Rien, pas même une fenêtre de maintenance ou un temps d’arrêt, ne doit vous obliger à arrêter la charge de travail pendant la mise à niveau ou la maintenance de la base de données. 

Il existe deux modèles architecturaux haute disponibilité qui sont utilisés dans la base de données SQL Azure :

- Modèle de disponibilité standard qui est basé sur une séparation de calcul et de stockage.  Il s’appuie sur la haute disponibilité et fiabilité de la couche de stockage à distance. Cette architecture cible des applications métier orientées budget qui sont capable de tolérer une baisse de performances pendant les activités de maintenance.
- Modèle de disponibilité Premium qui est basé sur un cluster de processus de moteur de base de données. Il repose sur le fait qu’il y a toujours un quorum de nœuds de moteur de base de données disponible. Cette architecture cible des applications critiques avec des performances d’e/s élevées, taux de transactions élevé et garanties impact minimal sur les performances de votre charge de travail pendant les activités de maintenance.

Base de données SQL Azure s’exécute sur la dernière version stable du moteur de base de données SQL Server et du système d’exploitation Windows, et la plupart des utilisateurs remarquerait pas que les mises à niveau sont exécutées en permanence.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilité des niveaux de service De base, Standard et Usage général

Ces niveaux de service de tirer parti de l’architecture de disponibilité standard. L’illustration suivante montre quatre différents nœuds avec les couches de calcul et de stockage séparés.

![Séparation du calcul et du stockage](media/sql-database-high-availability/general-purpose-service-tier.png)

Le modèle de disponibilité standard inclut deux couches :

- Une couche de calcul sans état qui s’exécute le `sqlserver.exe` traiter et contient des données uniquement temporaires et mis en cache sur le disque SSD attaché, telles que la colonne, base de données model, cache du plan, pool de mémoires tampons et TempDB pool du magasin. Ce nœud sans état est géré par Azure Service Fabric qui initialise `sqlserver.exe`, contrôle d’intégrité du nœud et effectue le basculement vers un autre nœud si nécessaire.
- Une couche de données avec état avec les fichiers de base de données (.mdf/.ldf) qui sont stockés dans le stockage Blob Azure. Stockage d’objets blob Azure a la disponibilité des données intégrée et la fonctionnalité de redondance. Elle garantit que chaque enregistrement dans la page dans le fichier de données ou un fichier journal est conservé même si le processus SQL Server tombe en panne.

Chaque fois que le moteur de base de données ou le système d’exploitation est mis à niveau ou une défaillance est détectée, Azure Service Fabric déplacera le processus SQL Server sans état vers un autre nœud de calcul sans état avec une capacité disponible suffisante. Les données dans le stockage Blob Azure ne sont pas affectées par le déplacement, et les fichiers de données/journaux sont attachés au processus SQL Server qui vient d’être initialisé. Ce processus garantit une disponibilité de 99,99 %, mais une lourde charge de travail peut constater une baisse de performances pendant la transition dans la mesure où la nouvelle instance de SQL Server démarre avec le cache à froid.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilité des niveaux de service Premium et Critique pour l’entreprise

Premium et critique pour l’entreprise exploiter de niveaux de service le modèle de disponibilité Premium, qui intègre des ressources de calcul (processus de moteur de base de données SQL Server) et stockage (SSD attaché localement) sur un seul nœud. Haute disponibilité est obtenue en répliquant de calcul et stockage pour la création d’un cluster de nœud de trois à quatre - des nœuds supplémentaires. 

![Cluster de nœuds de moteur de base de données](media/sql-database-high-availability/business-critical-service-tier.png)

Les fichiers de base de données sous-jacente (.mdf/.ldf) sont placés sur le stockage SSD attaché afin de fournir une latence très faible d’e/s à votre charge de travail. Haute disponibilité est implémentée à l’aide d’une technologie similaire à SQL Server [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Le cluster comprend un seul réplica principal (processus SQL Server) qui est accessible pour les charges de travail en lecture-écriture client et jusqu'à trois réplicas secondaires (de calcul et de stockage) contenant les copies des données. Le nœud principal exécute un push des modifications sur les nœuds secondaires dans l’ordre en permanence et garantit que les données sont synchronisées au moins un réplica secondaire avant de valider chaque transaction. Ce processus garantit que si le nœud principal tombe en panne pour une raison quelconque, il est toujours un nœud entièrement synchronisé vers lequel basculer. Le basculement est initié par l’infrastructure de Service Azure. Une fois le réplica secondaire devient le nouveau nœud principal, un autre réplica secondaire est créé pour garantir que le cluster dispose de suffisamment de nœuds (jeu de quorum). Une fois le basculement terminé, les connexions SQL sont automatiquement redirigées vers le nouveau nœud principal.

Un avantage supplémentaire, le modèle de disponibilité premium offre la possibilité de rediriger les connexions SQL en lecture seule à un des réplicas secondaires. Cette fonctionnalité est appelée [lecture du Scale-Out](sql-database-read-scale-out.md). Il fournit plus de 100 % le calcul sans frais supplémentaires pour décharger des opérations en lecture seule, telles que des charges de travail analytiques, à partir du réplica principal.

## <a name="zone-redundant-configuration"></a>Configuration de zone redondante

Par défaut, le cluster de nœuds pour le modèle de disponibilité premium est créé dans le même centre de données. Avec l’introduction de [Zones de disponibilité Azure](../availability-zones/az-overview.md), base de données SQL peut placer des réplicas différents dans le cluster à différentes zones de disponibilité dans la même région. Pour éliminer un point de défaillance unique, l’anneau de contrôle est également dupliqué sur plusieurs fuseaux horaires sous forme de trois anneaux de passerelle (GW). Le routage vers un anneau de passerelle spécifique est contrôlé par [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Étant donné que la configuration de zone redondante dans les niveaux de service Premium ou critique pour l’entreprise ne crée pas de redondance de base de données supplémentaires, vous pouvez l’activer à aucun coût supplémentaire. En sélectionnant une configuration de zone redondante, vous pouvez apporter vos bases de données Premium ou critique pour l’entreprise résilient à un plus grand éventail d’échecs, notamment les pannes graves de centre de données, sans les modifications apportées à la logique d’application. Vous pouvez également convertir vos bases de données ou pools Premium ou Critique pour l’entreprise en configuration avec redondance dans une zone.

Les bases de données redondant de zone ont des réplicas dans différents centres de données avec une certaine distance entre eux, la latence accrue du réseau peut augmenter le temps de validation et ainsi affecter les performances de certaines charges de travail OLTP. Vous pouvez toujours revenir à la configuration de zone unique en désactivant le paramètre de redondance de zone. Ce processus est une opération en ligne similaire à la mise à niveau de la couche service normal. À la fin du processus, la base de données ou le pool est migré à partir d’un anneau de redondance de zone vers un anneau de zone unique, ou vice versa.

> [!IMPORTANT]
> Les bases de données redondants de zone et les pools élastiques sont actuellement pris en charge uniquement dans les niveaux de service Premium et critique pour l’entreprise. Par défaut, les sauvegardes et enregistrements d’audit sont stockés dans le stockage RA-GRS, et ne sont donc pas automatiquement disponibles en cas d’une panne à l’échelle de la zone. 

La version avec redondance de zone de l’architecture de haute disponibilité est illustrée dans le diagramme suivant :

![architecture haute disponibilité avec redondance de zone](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Récupération de base de données accélérée (ADR)

[Accélération de la récupération de base de données (ADR)](sql-database-accelerated-database-recovery.md) est une nouvelle fonctionnalité du moteur de base de données SQL qui améliore considérablement la disponibilité de base de données, en particulier en présence de longue durée en cours d’exécution des transactions. La récupération de base de données accélérée est actuellement disponible pour les bases de données uniques, les pools élastiques et Azure SQL Data Warehouse.

## <a name="conclusion"></a>Conclusion

Base de données SQL Azure propose une solution de haute disponibilité intégrée, ce qui est profondément intégrée avec la plateforme Azure. Il dépend de Service Fabric pour la détection des défaillances et de récupération, le stockage Blob Azure pour la protection des données et des Zones de disponibilité pour augmenter la tolérance aux pannes. En outre, Azure SQL database s’appuie sur la technologie de groupe de disponibilité AlwaysOn de SQL Server pour la réplication et le basculement. La combinaison de ces technologies permet aux applications d’exploiter pleinement les avantages d’un stockage mixte modéliser et prendre en charge les contrats SLA les plus exigeantes.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Zones de disponibilité Azure](../availability-zones/az-overview.md)
- En savoir plus sur [Service Fabric](../service-fabric/service-fabric-overview.md)
- En savoir plus sur [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Pour découvrir plus d’options concernant la haute disponibilité et la reprise d’activité après sinistre, consultez [Continuité d’activité](sql-database-business-continuity.md).
