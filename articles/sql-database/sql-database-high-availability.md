---
title: 'Haute disponibilité : service Azure SQL Database | Microsoft Docs'
description: En savoir plus sur les fonctionnalités de haute disponibilité du service Azure SQL Database
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: a9874681d59d193fc3c3d0fd4271e2a6a0fb0dc6
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060381"
---
# <a name="high-availability-and-azure-sql-database"></a>Haute disponibilité et Azure SQL Database

Azure SQL Database un service de base de données de type PaaS (Platform as a Service) hautement disponible qui garantit une disponibilité dans le temps de votre base de données de 99,99 %, éliminant tout souci lié à la maintenance et aux temps d’arrêt. Ce processus du moteur de base de données SQL Server, entièrement géré et hébergé dans le cloud Azure, garantit que votre base de données SQL Server est toujours mise à niveau/corrigée sans que votre charge de travail ne soit affectée. Pour assurer la disponibilité de vos données, Azure SQL Database bénéficie de fonctionnalités de récupération rapide, même dans les situations les plus critiques.

La plateforme Azure gère entièrement chaque base de données SQL Azure et garantit un pourcentage élevé de disponibilité des données, sans aucune perte. Azure gère automatiquement les tâches suivantes : mises à jour correctives, sauvegardes, réplication, détection des éventuelles défaillances matérielles, logicielles ou réseau sous-jacentes, déploiement de correctifs de bogues, basculements, mises à niveau de base de données et autres tâches de maintenance. Les ingénieurs de SQL Server ont implémenté les bonnes pratiques connues pour que la durée d’exécution des opérations de maintenance représente moins de 0,01 % de la durée de vie de votre base de données. Cette architecture est conçue de manière à éliminer tout risque de perte des données validées et à mener à bien les opérations de maintenance sans affecter la charge de travail. Rien, pas même une fenêtre de maintenance ou un temps d’arrêt, ne doit vous obliger à arrêter la charge de travail pendant la mise à niveau ou la maintenance de la base de données. La haute disponibilité intégrée à Azure SQL Database garantit que cette base de données ne constitue jamais un point de défaillance unique dans votre architecture logicielle.

Deux modèles haute disponibilité sont appliqués dans SQL Azure :

- Un modèle Standard (usage général) qui offre une disponibilité de 99,99 %, mais qui peut entraîner une détérioration des performances durant les activités de maintenance.
- Un modèle Premium (vital pour l’entreprise) qui fournit également une disponibilité de 99,99 % mais avec un impact minimal sur les performances de votre charge de travail, même pendant les activités de maintenance.

Azure met à niveau et corrige le système d’exploitation sous-jacent, les pilotes et le moteur de base de données SQL Server en toute transparence avec un temps d’arrêt minimal pour les utilisateurs finaux. Azure SQL Database s’exécutant sur la dernière version stable du moteur de base de données SQL Server et du système d’exploitation Windows, la plupart des utilisateurs ne se rendent pas compte que les mises à niveau sont effectuées en continu.

## <a name="standard-availability"></a>Disponibilité Standard

La disponibilité Standard fait référence au contrat SLA de 99,99 % appliqué dans les niveaux Standard/De base/Usage général. Pour obtenir cette disponibilité, les couches de calcul et de stockage sont séparées. Le modèle de disponibilité Standard comprend deux couches :

- Une couche de calcul sans état qui exécute le processus sqlserver.exe et contient uniquement des données transitoires et mises en cache (par exemple : cache du plan, pool de mémoires tampons, pool de stockage de colonnes). Ce nœud SQL Server sans état est géré par Azure Service Fabric qui initialise le processus, contrôle l’intégrité du nœud et effectue le basculement vers un autre emplacement si nécessaire.
- Une couche de données avec état comprenant les fichiers de base de données (.mdf/.ldf) stockés dans des disques Stockage Azure Premium. Stockage Azure garantit que tout enregistrement placé dans un fichier de base de données ne subit aucune perte de données. Stockage Azure est doté de fonctionnalités intégrées de redondance et de disponibilité des données qui garantissent la préservation des enregistrements d’un fichier journal ou des pages d’un fichier de données, même en cas de plantage du processus SQL Server.

Dès que le moteur de base de données ou le système d’exploitation est mis à niveau, ou quand un problème critique est détecté dans le processus SQL Server, Azure Service Fabric déplace le processus SQL Server sans état vers un autre nœud de calcul sans état. Les données dans la couche Stockage Azure ne sont pas affectées, et les fichiers de données/journaux sont attachés à des processus SQL Server nouvellement initialisés. Les temps de basculement attendus peuvent être mesurés en secondes. Ce processus garantit une disponibilité de 99,99 %, mais il peut impacter les performances des lourdes charges de travail en cours d’exécution, et ce en raison des délais de transition et du fait que le nouveau nœud SQL Server démarre avec un cache à froid.

## <a name="premium-availability"></a>Disponibilité Premium

La disponibilité Premium, activée dans le niveau Premium de Azure SQL Database, est conçue pour les charges de travail intenses qui ne tolèrent aucun impact sur les performances résultant des opérations de maintenance en cours.

Dans le modèle Azure, Azure SQL Database intègre les opérations de calcul et stockage sur le nœud unique. Le processus du moteur de base de données SQL Server et les fichiers mdf/ldf sous-jacents sont placés sur le même nœud, et le stockage SSD attaché localement permet à votre charge de travail de bénéficier d’une latence faible.

La haute disponibilité est implémentée à l’aide de [groupes de disponibilité Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) standard. Chaque base de données est un cluster de nœuds de base de données, avec une base de données primaire accessible à la charge de travail cliente et quelques processus secondaires contenant les copies des données. Le nœud principal envoie (push) constamment les changements apportés aux nœuds secondaires afin de garantir la disponibilité des données sur les réplicas secondaires en cas de plantage du nœud principal, pour quelque raison que ce soit. Le basculement est géré par le moteur de base de données SQL Server : un réplica secondaire devient le nœud principal, et un réplica secondaire est créé pour garantir un nombre suffisant de nœuds dans le cluster. La charge de travail est automatiquement redirigée vers le nouveau nœud principal. La durée de basculement est mesurée en millisecondes, et la nouvelle instance principale est aussitôt prête à poursuivre le traitement des demandes.

## <a name="zone-redundant-configuration-preview"></a>Configuration de zone redondante (préversion)

Par défaut, les réplicas du jeu de quorum pour les configurations de stockage local sont créés dans le même centre de données. Avec l’introduction des [Zones de disponibilité Azure](../availability-zones/az-overview.md), vous avez la possibilité de placer les différents réplicas dans les jeux de quorum sur des zones de haute disponibilité distinctes dans la même région. Pour éliminer un point de défaillance unique, l’anneau de contrôle est également dupliqué sur plusieurs fuseaux horaires sous forme de trois anneaux de passerelle (GW). Le routage vers un anneau de passerelle spécifique est contrôlé par [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Étant donné que la configuration de la redondance dans une zone ne crée pas de redondance de base de données supplémentaire, l’utilisation des zones de disponibilité dans les niveaux de service Premium ou Critique pour l’entreprise (préversion) est disponible sans coût supplémentaire. En sélectionnant une base de données redondante dans une zone, vous pouvez rendre vos bases de données Premium ou Critique pour l’entreprise (préversion) résistantes à un plus grand éventail d’échecs, notamment les pannes graves de centre de données, sans aucune modification de la logique d’application. Vous pouvez également convertir vos bases de données ou pools Premium ou Critique pour l’entreprise (préversion) en configuration avec redondance dans une zone.

L’ensemble du jeu de quorums de redondance de zone ayant des réplicas dans différents centres de données avec une certaine distance entre eux, la latence accrue du réseau peut augmenter le temps de validation et ainsi avoir un impact sur les performances de certaines charges de travail OLTP. Vous pouvez toujours revenir à la configuration de zone unique en désactivant le paramètre de redondance de zone. Ce processus dépend de la taille des données et est semblable à la mise à jour des objectifs de niveau de service (SLO) ordinaires. À la fin du processus, la base de données ou le pool est migré à partir d’un anneau de redondance de zone vers un anneau de zone unique, ou vice versa.

> [!IMPORTANT]
> Pour le moment, les bases de données avec redondance de zone et les pools élastiques sont uniquement pris en charge dans le niveau de service Premium. Pour la version préliminaire publique, les sauvegardes et enregistrements d’audit sont stockés dans le stockage de RA-GRS, et ne sont donc pas automatiquement disponibles en cas d’une panne à l’échelle de la zone. 

La version avec redondance de zone de l’architecture de haute disponibilité est illustrée dans le diagramme suivant :
 
![architecture haute disponibilité avec redondance de zone](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Lecture du Scale-out
Comme décrit, les niveaux de service Premium et Critique pour l’entreprise (préversion) tirent parti du quorum et de la technologie Always On pour une haute disponibilité à la fois dans des configurations pour zone unique et redondantes dans une zone. Un des avantages de la technologie Always On est que les réplicas sont toujours dans un état cohérent au niveau transactionnel. Étant donné que les réplicas ont le même niveau de performances que le principal, l’application peut tirer parti de cette capacité supplémentaire pour la maintenance des charges de travail en lecture seule sans coût supplémentaire (lecture du Scale-out). De cette façon, les requêtes en lecture seule seront isolées à partir de la charge de travail principale en lecture-écriture et n’affecteront pas ses performances. La fonctionnalité de lecture du Scale-out est conçue pour les applications incluant des charges de travail en lecture seule séparées logiquement, comme des analyses, et peut par conséquent tirer parti de cette capacité supplémentaire sans connexion au principal. 

Pour utiliser la fonctionnalité d’échelle horizontale en lecture avec une base de données en particulier, vous devez l’activer explicitement lors de la création de la base de données, ou ultérieurement en modifiant sa configuration avec PowerShell par un appel aux cmdlets [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [ New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) ou avec l’API REST Azure Resource Manager suivant la méthode [Bases de données – Créer ou mettre à jour](/rest/api/sql/databases/createorupdate).

Une fois la lecture du Scale Out activée pour une base de données, les applications se connectant à cette base de données seront dirigées vers le réplica en lecture-écriture ou un réplica en lecture seule de cette base de données en fonction de la propriété `ApplicationIntent` configurée dans la chaîne de connexion de l’application. Pour plus d’informations sur la propriété `ApplicationIntent`, consultez [Spécification de l’intention de l’application](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Si l’échelle horizontale en lecture est désactivée ou si la propriété d’échelle lecture est définie dans un niveau de service non pris en charge, toutes les connexions sont dirigées vers le réplica en lecture-écriture, indépendamment de la propriété `ApplicationIntent`.  

> [!NOTE]
> Il est possible d’activer l’échelle horizontale en lecture sur une base de données Standard ou Usage général, même si cela n’aura pas pour effet de router la session visée en lecture seule vers un réplica distinct. L’objectif est de prendre en charge des applications existantes qui évoluent entre les niveaux Standard/Usage général et Premium/Critique pour l’entreprise.  

La fonctionnalité de lecture du Scale-out prend en charge la cohérence au niveau de la session. Si la session en lecture seule se reconnecte après une erreur de connexion engendrée par l’indisponibilité du réplica, elle peut être redirigée vers un autre réplica. Bien qu’improbable, cela peut engendrer le traitement d’un jeu de données périmé. De même, si une application écrit des données à l’aide d’une session en lecture-écriture et les lit immédiatement à l’aide d’une session en lecture seule, il est possible que les nouvelles données ne soient pas visibles immédiatement.

## <a name="conclusion"></a>Conclusion
Azure SQL Database est étroitement intégré à la plateforme Azure et dépend très largement de Service Fabric pour la détection des défaillances et la récupération, mais aussi des objets blob de stockage Azure pour la protection des données et des zones de disponibilité. Parallèlement à cela, Azure SQL Database utilise pleinement la technologie des groupes de disponibilité Always On intégrée à SQL Server pour la réplication et le basculement. La combinaison de ces technologies permet aux applications de profiter pleinement des avantages d’un modèle de stockage mixte et de prendre en charge les contrats de niveau de service les plus exigeants. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Zones de disponibilité Azure](../availability-zones/az-overview.md)
- En savoir plus sur [Service Fabric](../service-fabric/service-fabric-overview.md)
- En savoir plus sur [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
