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
ms.date: 06/10/2019
ms.openlocfilehash: 226b0c1cb11fc872cb7759e0d0e49275b9c2d9bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568151"
---
# <a name="high-availability-and-azure-sql-database"></a>Haute disponibilité et Azure SQL Database

L’objectif de l’architecture de haute disponibilité dans Azure SQL Database est de garantir que votre base de données est opérationnelle 99,99 % du temps, sans que vous vous préoccupiez de l’impact des opérations de maintenance et des pannes. Azure gère automatiquement les tâches de maintenance vitales, telles les mises à jour correctives, sauvegardes, mises à niveau Windows et SQL, ainsi que les événements non planifiés, comme les défaillances de matériel, logiciel ou réseau subordonnées.  Quand l’instance SQL fondamentale est corrigée ou qu’elle bascule, le temps d’arrêt n’est pas perceptible si vous [utilisez une logique de nouvelle tentative](sql-database-develop-overview.md#resiliency) dans votre application. Pour assurer la disponibilité de vos données, Azure SQL Database bénéficie de fonctionnalités de récupération rapide, même dans les situations les plus critiques.

La solution de haute disponibilité est conçue pour garantir que les données validées ne sont jamais perdues pour cause d’échecs, que les opérations de maintenance n’affectent pas votre charge de travail, et que la base de données n’est pas un point de défaillance unique dans votre architecture logicielle. Rien, pas même une fenêtre de maintenance ou un temps d’arrêt, ne doit vous obliger à arrêter la charge de travail pendant la mise à niveau ou la maintenance de la base de données. 

Azure SQL Database utilise deux modèles architecturaux haute disponibilité :

- Le modèle de disponibilité Standard est basé sur la séparation du calcul et du stockage.  Il s’appuie sur la haute disponibilité et la fiabilité du niveau stockage à distance. Cette architecture cible des applications métier économiques, capables de tolérer une baisse de performances pendant les activités de maintenance.
- Le modèle de disponibilité Premium est basé sur un cluster de processus de moteur de base de données. Il repose sur le fait qu’il existe toujours un quorum de nœuds de moteur de base de données disponible. Cette architecture s’adresse à des applications stratégiques ayant des performances d’e/s supérieures et un taux de transactions élevé ; elle garantit un impact minimal sur les performances de votre charge de travail pendant les activités de maintenance.

Comme Azure SQL Database s’exécute sur la dernière version stable du moteur de base de données SQL Server et du système d’exploitation Windows, la plupart des utilisateurs ne se rendent pas compte des mises à niveau qui sont effectuées en continu.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilité des niveaux de service De base, Standard et Usage général

Ces niveaux de service tirent parti de l’architecture de disponibilité standard. L’illustration suivante montre quatre nœuds distincts, avec les couches de calcul et de stockage séparées.

![Séparation du calcul et du stockage](media/sql-database-high-availability/general-purpose-service-tier.png)

Le modèle de disponibilité Standard comprend deux couches :

- Une couche de calcul sans état, qui exécute le processus `sqlserver.exe` et contient uniquement des données transitoires et mises en cache sur le SSD attaché, comme TempDB, la base de données model, le cache du plan, le pool de mémoires tampons et le pool de stockage de colonnes. Ce nœud sans état est géré par Azure Service Fabric qui initialise `sqlserver.exe`, contrôle l’intégrité du nœud et effectue le basculement vers un autre nœud si nécessaire.
- Une couche de données avec état, comprenant les fichiers de base de données (.mdf/.ldf) stockés dans le service Stockage Blob Azure. La fonctionnalité de disponibilité et redondance des données est intégrée au stockage d’objets blob Azure. Celle-ci garantit la conservation de chaque enregistrement dans le fichier journal, ou de chaque page dans le fichier de données, même en cas d’incident du processus SQL Server.

Dès que le moteur de base de données ou que le système d’exploitation est mis à niveau, ou qu’une défaillance est détectée, Azure Service Fabric déplace le processus SQL Server sans état vers un autre nœud de calcul sans état disposant d’une capacité disponible suffisante. Les données conservées dans le stockage Blob Azure ne sont pas affectées par le déplacement, et les fichiers de données ou de journaux sont joints au processus SQL Server nouvellement initialisé. Ce processus garantit une disponibilité de 99,99 %, mais une charge de travail lourde peut accuser une baisse de performances pendant la transition, car la nouvelle instance SQL Server démarre avec le cache à froid.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilité des niveaux de service Premium et Critique pour l’entreprise

Les niveaux de service Premium et Critique pour l’entreprise exploitent le modèle de disponibilité Premium, qui intègre des ressources de calcul (processus de moteur de base de données SQL Server) et du stockage (SSD attaché localement) sur un seul nœud. La haute disponibilité est obtenue en répliquant calcul et stockage sur des nœuds supplémentaires pour la création d’un cluster à trois ou quatre nœuds. 

![Cluster de nœuds de moteur de base de données](media/sql-database-high-availability/business-critical-service-tier.png)

Les fichiers de base de données sous-jacents (.mdf/.ldf) sont placés sur le stockage SSD attaché, afin de fournir une latence des E/S très faible à votre charge de travail. La haute disponibilité est implémentée au moyen d’une technologie similaire aux [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) SQL Server. Le cluster comprend un seul réplica principal (processus SQL Server) qui est accessible pour les charges de travail en lecture-écriture des clients, et jusqu’à trois réplicas secondaires (de calcul et de stockage) contenant les copies des données. Le nœud principal envoie (push) régulièrement et dans l’ordre des modifications sur les nœuds secondaires, et s’assure que les données sont synchronisées sur au moins un réplica secondaire avant de valider chaque transaction. Ce processus garantit qu’en cas de plantage du nœud principal pour une quelconque raison, il existe toujours un nœud entièrement synchronisé vers lequel basculer. Le basculement est initié par Azure Service Fabric. Lorsque le réplica secondaire devient le nouveau nœud principal, un autre réplica secondaire est créé pour garantir que le cluster dispose de suffisamment de nœuds (ensemble du quorum). Une fois le basculement terminé, les connexions SQL sont automatiquement redirigées vers le nouveau nœud principal.

Autre avantage, le modèle de disponibilité Premium comprend la possibilité de rediriger les connexions SQL en lecture seule vers un des réplicas secondaires. Cette fonctionnalité est appelée [Scale-out en lecture](sql-database-read-scale-out.md). Elle fournit 100 % de capacité de calcul, sans frais supplémentaires, pour décharger depuis le réplica principal des opérations en lecture seule, telles que les charges de travail analytiques.

## <a name="zone-redundant-configuration"></a>Configuration de zone redondante

Par défaut, le cluster de nœuds pour le modèle de disponibilité Premium est créé dans le même centre de données. Avec l’introduction des [Zones de disponibilité Azure](../availability-zones/az-overview.md), SQL Database peut placer différents réplicas du cluster dans des zones de disponibilité distinctes au sein de la même région. Pour éliminer un point de défaillance unique, l’anneau de contrôle est également dupliqué sur plusieurs fuseaux horaires sous forme de trois anneaux de passerelle (GW). Le routage vers un anneau de passerelle spécifique est contrôlé par [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Étant donné que la configuration redondante interzone dans les niveaux de service Premium ou Critique pour l’entreprise ne crée pas de redondance de base de données supplémentaire, vous pouvez l’activer sans frais supplémentaires. En sélectionnant une configuration redondante dans une zone, vous rendez vos bases de données Premium ou Critique pour l’entreprise résistantes à un plus grand éventail d’échecs, notamment les pannes graves de centre de données, sans aucune modification à la logique d’application. Vous pouvez également convertir vos bases de données ou pools Premium ou Critique pour l’entreprise en configuration avec redondance dans une zone.

Les bases de données de redondance de zone, ayant des réplicas dans différents centres de données avec une certaine distance entre eux, la latence accrue du réseau peut augmenter le temps de validation et ainsi avoir un impact sur les performances de certaines charges de travail OLTP. Vous pouvez toujours revenir à la configuration de zone unique en désactivant le paramètre de redondance de zone. Ce processus est une opération en ligne, semblable à la mise à niveau des niveaux de service ordinaires. À la fin du processus, la base de données ou le pool est migré à partir d’un anneau de redondance de zone vers un anneau de zone unique, ou vice versa.

> [!IMPORTANT]
> Les bases de données avec redondance de zone et les pools élastiques ne sont actuellement pris en charge que dans les niveaux de service Premium et Critique pour l’entreprise, dans les régions sélectionnées. Lorsque vous utilisez le niveau Critique pour l’entreprise, la configuration de zone redondante est uniquement disponible lorsque le matériel de calcul Gen5 est sélectionné. Pour obtenir des informations à jour sur les régions qui prennent en charge les bases de données redondantes dans une zone, consultez [Prise en charge des services par région](../availability-zones/az-overview.md#services-support-by-region).  

La version avec redondance de zone de l’architecture de haute disponibilité est illustrée dans le diagramme suivant :

![architecture haute disponibilité avec redondance de zone](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Récupération de base de données accélérée (ADR)

La [récupération de base de données accélérée](sql-database-accelerated-database-recovery.md) est une nouvelle fonctionnalité du moteur de base de données SQL qui améliore considérablement la disponibilité des bases de données, particulièrement en présence de transactions durables. La récupération de base de données accélérée est actuellement disponible pour les bases de données uniques, les pools élastiques et Azure SQL Data Warehouse.

## <a name="conclusion"></a>Conclusion

Azure SQL Database présente une solution de haute disponibilité intégrée, qui est incorporée en profondeur à la plateforme Azure. Cette solution dépend de Service Fabric pour la détection et la récupération des défaillances, mais aussi du stockage Blob Azure pour la protection des données et des Zones de disponibilité pour une meilleure tolérance aux pannes. Par ailleurs, Azure SQL Database utilise amplement la technologie des groupes de disponibilité Always On à partir de SQL Server pour la réplication et le basculement. La combinaison de ces technologies permet aux applications de profiter pleinement des avantages d’un modèle de stockage mixte, et de prendre en charge les contrats de niveau de service les plus exigeants.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Zones de disponibilité Azure](../availability-zones/az-overview.md)
- En savoir plus sur [Service Fabric](../service-fabric/service-fabric-overview.md)
- En savoir plus sur [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Pour découvrir plus d’options concernant la haute disponibilité et la reprise d’activité après sinistre, consultez [Continuité d’activité](sql-database-business-continuity.md).
