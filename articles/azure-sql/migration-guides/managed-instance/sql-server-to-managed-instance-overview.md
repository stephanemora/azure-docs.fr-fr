---
title: 'SQL Server vers SQL Managed Instance : Vue d’ensemble de la migration'
description: Découvrez les outils et options à disposition pour migrer vos bases de données SQL Server vers Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 02/18/2020
ms.openlocfilehash: c3d4b882a83b457527fcf71424357b8a51a86d84
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783266"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Vue d’ensemble de la migration : de SQL Server vers Azure SQL Managed Instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Découvrez les options et les considérations relatives à la migration de vos bases de données SQL Server vers Azure SQL Managed Instance. 

Vous pouvez migrer des bases de données SQL Server s’exécutant en local ou sur : 

- SQL Server sur les machines virtuelles Azure.  
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2). 
- AWS Relational Database Service (RDS). 
- Compute Engine dans Google Cloud Platform (GCP).  
- Cloud SQL pour SQL Server dans GCP. 

Pour obtenir d’autres guides de migration, consultez [Migration de base de données](/data-migration). 

## <a name="overview"></a>Vue d’ensemble

[Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md) est une option cible recommandée pour les charges de travail SQL Server qui requièrent un service complètement managé sans avoir à gérer des machines virtuelles ou leurs systèmes d’exploitation. SQL Managed Instance vous permet de déplacer vos applications locales vers Azure avec un minimum de modifications des applications ou des bases de données. Le service offre une isolation complète de vos instances avec la prise en charge native des réseaux virtuels. 

Veillez à passer en revue les fonctionnalités du moteur de base de données SQL Server [disponibles dans Azure SQL Managed Instance](../../database/features-comparison.md) pour valider la capacité de prise en charge de votre cible de migration.  

## <a name="considerations"></a>Considérations 

Les facteurs clés à prendre en considération au moment d’évaluer les différentes options de migration dépendent des éléments suivants : 
- Nombre de serveurs et de bases de données
- Taille des bases de données
- Temps d’arrêt acceptable pour l’entreprise pendant le processus de migration 

L’un des principaux avantages de la migration de vos bases de données SQL Server vers SQL Managed Instance est que vous pouvez choisir de migrer la totalité de l’instance ou simplement un sous-ensemble de bases de données individuelles. Prévoyez d’inclure les éléments suivants dans votre processus de migration : 
- Toutes les bases de données qui doivent être colocalisées dans la même instance 
- Les objets au niveau de l’instance dont votre application dépend, notamment les connexions, les informations d’identification, les travaux et opérateurs SQL Agent, ainsi que les déclencheurs au niveau du serveur 

> [!NOTE]
> Azure SQL Managed Instance garantit une disponibilité de 99,99 %, même dans des scénarios critiques. La surcharge causée par certaines fonctionnalités de SQL Managed Instance ne peut pas être désactivée. Pour plus d’informations, consultez l’article de blog intitulé [Principales causes des différences de performances entre SQL Managed Instance et SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). 


## <a name="choose-an-appropriate-target"></a>Choisir une cible appropriée

Les directives générales suivantes peuvent vous aider à choisir le bon niveau de service et les caractéristiques de SQL Managed Instance afin d’atteindre votre [ligne de base en matière de performances](sql-server-to-managed-instance-performance-baseline.md) : 

- Utilisez la ligne de base d’utilisation de l’UC pour approvisionner une instance gérée qui correspond au nombre de cœurs utilisés par votre instance SQL Server. Il peut être nécessaire de mettre à l’échelle les ressources pour qu’elles correspondent aux [caractéristiques de la génération du matériel](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Utilisez la ligne de base d’utilisation de la mémoire pour choisir une [option vCore](../../managed-instance/resource-limits.md#service-tier-characteristics) qui correspond de manière appropriée à votre allocation de mémoire. 
- Utilisez la latence d’E/S de ligne de base du sous-système de fichiers pour choisir entre les niveaux de service Usage général (latence supérieure à 5 ms) et Critique pour l’entreprise (latence inférieure à 3 ms). 
- Utilisez le débit de ligne de base pour préallouer la taille des fichiers de données et des fichiers journaux pour atteindre les performances d’E/S attendues. 

Vous pouvez choisir des ressources de calcul et de stockage pendant le déploiement et [en changer par la suite via le portail Azure](../../database/scale-resources.md) sans temps d’arrêt pour votre application. 

> [!IMPORTANT]
> Toute différence dans la [configuration requise du réseau virtuel pour les instances gérées](../../managed-instance/connectivity-architecture-overview.md#network-requirements) peut vous empêcher de créer de nouvelles instances ou d’utiliser celles qui existent déjà. En savoir plus sur la [création de nouveaux réseaux](../../managed-instance/virtual-network-subnet-create-arm-template.md) et sur la [configuration des réseaux existants](../../managed-instance/vnet-existing-add-subnet.md). 

Un autre point important à prendre en compte dans la sélection du niveau de service cible dans Azure SQL Managed Instance (Usage général ou Critique pour l’entreprise) est la disponibilité de certaines fonctionnalités comme l’OLTP en mémoire, qui sont uniquement disponibles dans le niveau Critique pour l’entreprise. 

### <a name="sql-server-vm-alternative"></a>Alternative à la machine virtuelle SQL Server

Votre entreprise a peut-être des exigences qui font de [SQL Server sur les machines virtuelles Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) une cible plus indiquée qu’Azure SQL Managed Instance. 

Si l’une des conditions suivantes s’applique à votre entreprise, envisagez de passer à une machine virtuelle SQL Server à la place : 

- Vous avez besoin d’un accès direct au système d’exploitation ou au système de fichiers, par exemple pour installer des agents tiers ou personnalisés sur une même machine virtuelle avec SQL Server. 
- Vous dépendez obligatoirement de fonctionnalités qui ne sont pas encore prises en charge, comme FileStream/FileTable, PolyBase et les transactions entre plusieurs instances. 
- Vous devez conserver une version spécifique de SQL Server (2012, par exemple). 
- Vos exigences de calcul sont beaucoup plus faibles que ce qu’offre une instance gérée (un seul vCore, par exemple), et la consolidation des bases de données n’est pas une option acceptable. 

## <a name="migration-tools"></a>Outils de migration

Nous vous recommandons d’utiliser les outils de migration suivants : 

|Technology | Description|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Ce service Azure vous permet de découvrir et d’évaluer votre patrimoine de données SQL à grande échelle sur VMware. Il fournit des recommandations sur le déploiement Azure SQL, le dimensionnement cible et les estimations mensuelles. | 
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Ce service Azure prend en charge la migration en mode hors connexion pour les applications qui peuvent se permettre un temps d’arrêt pendant le processus de migration. Contrairement à la migration continue en mode en ligne, la migration en mode hors connexion exécute une restauration unique d’une sauvegarde complète de la base de données de la source vers la cible. | 
|[Sauvegarde et restauration natives](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance prend en charge la restauration des sauvegardes natives des bases de données SQL Server (fichiers .bak). C’est l’option de migration la plus simple pour les clients qui peuvent fournir des sauvegardes complètes des bases de données à Stockage Azure. Les sauvegardes complètes et différentielles sont également prises en charge et documentées dans la [section sur les ressources de migration](#migration-assets) plus loin dans cet article.| 
|[Log Replay Service](../../managed-instance/log-replay-service-migrate.md) | Ce service cloud est activé pour Managed Instance et repose sur la technologie de copie des journaux de transaction de SQL Server. Il s’agit d’une option de migration pour les clients qui peuvent fournir des sauvegardes complètes, différentielles et de journaux des bases de données à Stockage Azure. Log Replay Service est utilisé pour restaurer les fichiers de sauvegarde de Stockage Blob Azure vers SQL Managed Instance.| 
| | |

Le tableau suivant liste d’autres outils de migration : 

|**Technology** |**Description**  |
|---------|---------|
|[Réplication transactionnelle](../../managed-instance/replication-transactional-overview.md) | Réplique les données des tables de la base de données SQL Server source dans Azure SQL Managed Instance en proposant une option de migration de type éditeur-abonné tout en préservant la cohérence transactionnelle. | 
|[Copie en bloc](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| L’[outil bcp (bulk copy program)](/sql/tools/bcp-utility) permet de copier les données d’une instance SQL Server dans un fichier de données. Utilisez l’outil pour exporter les données de votre source et importer le fichier de données dans l’instance gérée SQL cible. </br></br> Pour profiter d’opérations de copie en bloc à haut débit lors du déplacement de données vers Azure SQL Managed Instance, l’[outil Smart Bulk Copy](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) peut être utilisé pour optimiser la vitesse de transfert en tirant parti des tâches de copie en parallèle. | 
|[Assistant Importation et exportation/BACPAC](../../database/database-import.md?tabs=azure-powershell)| Un [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) est un fichier Windows avec une extension .bacpac qui encapsule le schéma et les données d’une base de données. Vous pouvez utiliser un BACPAC à la fois pour exporter des données d’une source SQL Server et pour réimporter ces données dans Azure SQL Managed Instance. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md).|  L’[activité Copy](../../../data-factory/copy-activity-overview.md) d’Azure Data Factory migre les données des bases de données SQL Server sources vers Azure SQL Managed Instance à l’aide de connecteurs intégrés et d’un [runtime d’intégration](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory prend en charge un large éventail de [connecteurs](../../../data-factory/connector-overview.md) pour déplacer les données de sources SQL Server vers Azure SQL Managed Instance. |

## <a name="compare-migration-options"></a>Comparer les options de migration

Comparez les options de migration de façon à emprunter la voie qui vous permettra de répondre à vos besoins métier. 

Le tableau suivant compare les options de migration recommandées : 

|Option de migration  |Quand l’utiliser  |Considérations  |
|---------|---------|---------|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Migration de bases de données uniques ou de plusieurs bases de données à grande échelle. </br> – Acceptation de temps d’arrêt pendant le processus de migration. </br> </br> Sources prises en charge : </br> - SQL Server (2005 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute |  - Les migrations à grande échelle peuvent être automatisées via [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> – La durée de la migration dépend de la taille de la base de données et est influencée par le temps de sauvegarde et de restauration. </br> – Un temps d’arrêt suffisant peut être nécessaire. |
|[Sauvegarde et restauration natives](../../managed-instance/restore-sample-database-quickstart.md) | – Migration de bases de données individuelles d’applications métier.  </br> – Migration rapide et facile sans un service ou un outil de migration distinct.  </br> </br> Sources prises en charge : </br> - SQL Server (2005 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute | – La sauvegarde de base de données utilise plusieurs threads pour optimiser le transfert de données vers Stockage Blob Azure, mais la bande passante du partenaire et la taille de la base de données peuvent avoir un impact sur le débit de transfert. </br> – Les temps d’arrêt doivent tenir compte du temps nécessaire pour effectuer une sauvegarde et une restauration complètes (ce qui correspond à une taille d’une opération de données).| 
|[Log Replay Service](../../managed-instance/log-replay-service-migrate.md) | – Migration de bases de données individuelles d’applications métier.  </br> - Davantage de contrôle est nécessaire pour les migrations de bases de données.  </br> </br> Sources prises en charge : </br> - SQL Server (2008 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute | - La migration implique d’effectuer des sauvegardes complètes de base de données sur SQL Server et de copier les fichiers de sauvegarde dans le Stockage Blob Azure. Log Replay Service est utilisé pour restaurer les fichiers de sauvegarde de Stockage Blob Azure vers SQL Managed Instance. </br> – Les bases de données restaurées pendant le processus de migration sont en mode de restauration et ne peuvent pas être utilisées à des fins de lecture ou d’écriture tant que le processus n’est pas terminé.| 
| | | |

Le tableau suivant compare les autres options de migration : 

|Méthode ou technologie |Quand l’utiliser |Considérations  |
|---------|---------|---------|
|[Réplication transactionnelle](../../managed-instance/replication-transactional-overview.md) | – Migration avec une publication continue des modifications des tables de base de données sources vers les tables de base de données Azure SQL Managed Instance cibles. </br> – Effectuer une migration complète ou partielle des tables sélectionnées (sous-ensemble d’une base de données).  </br> </br> Sources prises en charge : </br> - SQL Server (2012 à 2019) avec certaines limitations </br> - AWS EC2  </br> - Machine virtuelle SQL Server GCP Compute | </br> - La configuration est relativement complexe par rapport à d’autres options de migration.   </br> - Offre une option de réplication continue pour migrer les données (sans mettre les bases de données en mode hors connexion).</br> – La réplication transactionnelle présente des limitations dont vous devez tenir compte si vous configurez l’éditeur sur l’instance SQL source. Pour plus d’informations, consultez [Limitations de la publication d’objets](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects).  </br> – Il est possible de [surveiller l’activité de réplication](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Copie en bloc](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Effectuer des migrations complètes ou partielles de données. </br> - Acceptation de temps d’arrêt. </br> </br> Sources prises en charge : </br> - SQL Server (2005 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute   | – Impose un temps d’arrêt pour exporter les données de la source et les importer dans la cible. </br> – Les formats de fichiers et les types de données utilisés dans l’exportation/importation doivent être cohérents avec les schémas de table. |
|[Assistant Importation et exportation/BACPAC](../../database/database-import.md)| – Migration de bases de données individuelles d’applications métier. </br>– Adapté aux bases de données de plus petite taille.  </br>  – Ne nécessite pas de service ou d’outil de migration distinct. </br> </br> Sources prises en charge : </br> - SQL Server (2005 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute  |   </br> – Impose un temps d’arrêt dans la mesure où les données doivent être exportées à la source et importées à la destination.   </br> – Les formats de fichiers et les types de données utilisés dans l’exportation ou importation doivent être cohérents avec les schémas de table pour éviter les erreurs de troncation ou de non-correspondance de types de données. </br> - L’exportation d’une base de données comportant un grand nombre d’objets peut prendre beaucoup plus de temps. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md).| – Migration et/ou transformation des données à partir des bases de données SQL Server sources.</br> – La fusion de données issues de plusieurs sources de données vers Azure SQL Managed Instance est généralement destinée aux charges de travail de type décisionnel.   </br> – Impose la création de pipelines de déplacement des données dans Data Factory pour déplacer les données de la source vers la destination.   </br> - Le [coût](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) est une considération importante qui varie en fonction des déclencheurs de pipeline, des exécutions d’activité, de la durée de déplacement des données, etc. |
| | | |

## <a name="feature-interoperability"></a>Interopérabilité des fonctionnalités 

D’autres éléments sont à prendre en considération quand vous migrez des charges de travail qui reposent sur d’autres fonctionnalités SQL Server. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Migrez des packages et des projets SQL Server Integration Services (SSIS) dans SSISDB vers Azure SQL Managed Instance à l’aide d’[Azure Database Migration Service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Seuls les packages SSIS dans SSISDB à partir de SQL Server 2012 sont pris en charge pour la migration. Convertissez les anciens packages SSIS avant la migration. Pour en savoir plus, consultez le [tutoriel de conversion de projet](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model). 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Vous pouvez migrer des rapports SQL Server Reporting Services (SSRS) vers des rapports paginés dans Power BI. Utilisez l’ [outil de migration RDL](https://github.com/microsoft/RdlMigration) pour préparer et migrer vos rapports. Microsoft a développé cet outil pour aider les clients à migrer les rapports RDL (Report Definition Language) de leurs serveurs SSRS vers Power BI. Il est disponible sur GitHub et fournit une procédure complète pour un scénario de migration. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

Les modèles tabulaires SQL Server Analysis Services de SQL Server 2012 et versions ultérieures peuvent être migrés vers Azure Analysis Services, qui est un modèle de déploiement PaaS (platform as a service) pour le modèle tabulaire Analysis Services dans Azure. Vous pouvez en savoir plus sur la migration de modèles locaux vers Azure Analysis Services dans [ce tutoriel vidéo](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Vous pouvez également envisager de migrer vos modèles tabulaires Analysis Services locaux vers [Power BI Premium à l’aide des nouveaux points de terminaison en lecture/écriture XMLA](/power-bi/admin/service-premium-connect-tools). 

### <a name="high-availability"></a>Haute disponibilité

Les fonctionnalités de haute disponibilité de SQL Server comme les instances de cluster de basculement Always On et les groupes de disponibilité Always On deviennent obsolètes sur l’instance gérée SQL cible. L’architecture de haute disponibilité est déjà intégrée aux niveaux de service [Usage général (modèle de disponibilité standard)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) et [Critique pour l’entreprise (modèle de disponibilité Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) pour Azure SQL Managed Instance. Le modèle de disponibilité Premium propose également une échelle horizontale en lecture qui permet de se connecter à l’un des nœuds secondaires à des fins de lecture seule.     

Au-delà de l’architecture de haute disponibilité incluse dans Azure SQL Managed Instance, la fonctionnalité des [groupes de basculement automatique ](../../database/auto-failover-group-overview.md) vous permet de gérer la réplication et le basculement des bases de données d’une instance gérée vers une autre région. 

### <a name="sql-agent-jobs"></a>Travaux SQL Agent

Utilisez l’option Azure Database Migration Service en mode hors connexion pour migrer des [travaux SQL Agent](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). Dans le cas contraire, créez un script des travaux en Transact-SQL (T-SQL) à l’aide de SQL Server Management Studio, puis recréez-les manuellement sur l’instance gérée SQL cible. 

> [!IMPORTANT]
> Actuellement, Azure Database Migration Service ne prend en charge que les travaux avec des étapes du sous-système T-SQL. Les travaux comportant des étapes du package SSIS devront être migrés manuellement. 

### <a name="logins-and-groups"></a>Connexions et groupes

Déplacez les connexions SQL de la source SQL Server vers Azure SQL Managed Instance à l’aide de Database Migration Service en mode hors connexion.  Utilisez le volet [Connexions sélectionnées](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) dans l’Assistant Migration pour migrer les connexions vers votre instance gérée SQL cible. 

Par défaut, Azure Database Migration Service prend uniquement en charge la migration des connexions SQL. Toutefois, vous pouvez rendre possible la migration des connexions Windows en procédant comme suit :

- Assurez-vous que l’instance gérée SQL cible dispose d’un accès en lecture à Azure Active Directory (Azure AD). Un utilisateur ayant le rôle Administrateur général peut configurer cet accès via le portail Azure.
- Configurez Azure Database Migration Service pour activer les migrations de connexion d’utilisateurs ou de groupes Windows. Cette configuration s’effectue via le portail Azure, sur la page **Configuration**. Après avoir activé ce paramètre, redémarrez le service pour que les modifications prennent effet.

Après le redémarrage du service, les connexions d’utilisateur ou de groupe Windows apparaissent dans la liste des connexions disponibles pour la migration. Pour toutes les connexions d’utilisateur ou de groupe Windows que vous migrez, vous êtes invité à fournir le nom de domaine associé. Les comptes d’utilisateur de service (comptes avec le nom de domaine NT AUTHORITY) et les comptes d’utilisateur virtuels (comptes avec le nom de domaine NT SERVICE) ne sont pas pris en charge. Pour en savoir plus, consultez [Comment migrer des utilisateurs et des groupes Windows dans une instance SQL vers Azure SQL Managed Instance en utilisant le langage T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Une autre solution consiste à utiliser l’[utilitaire PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) spécialement conçu par les architectes de migration de données Microsoft. Cet utilitaire s’appuie sur PowerShell pour créer un script T-SQL en vue de recréer les connexions et de sélectionner les utilisateurs de base de données de la source vers la cible. 

L’utilitaire PowerShell mappe automatiquement les comptes Windows Server Active Directory aux comptes Azure AD et peut effectuer une recherche d’UPN pour chaque connexion à l’instance Active Directory source. L’utilitaire scripte les rôles personnalisés du serveur et de la base de données, ainsi que l’appartenance aux rôles et les autorisations des utilisateurs. Les bases de données autonomes ne sont pas encore prises en charge et seul un sous-ensemble des autorisations SQL Server possibles est scripté. 

### <a name="encryption"></a>Chiffrement

Lorsque vous migrez des bases de données protégées par  [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md) vers une instance gérée à l’aide de l’option de restauration native, [migrez le certificat correspondant](../../managed-instance/tde-certificate-migrate.md) de l’instance SQL source vers l’instance gérée SQL cible *avant* la restauration de la base de données. 

### <a name="system-databases"></a>Bases de données système

La restauration de bases de données système n’est pas prise en charge. Pour migrer des objets au niveau de l’instance (stockés dans les bases de données MASTER et msdb), scriptez-les en utilisant le langage T-SQL, puis recréez-les sur l’instance gérée cible. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>OLTP en mémoire (tables à mémoire optimisée)

SQL Server fournit une capacité OLTP en mémoire. Elle permet d’utiliser des tables à mémoire optimisée, des types de tables à mémoire optimisée, ainsi que des modules SQL compilés nativement, pour exécuter des charges de travail qui exigent un débit élevé et une faible latence pour le traitement transactionnel. 

> [!IMPORTANT]
> La capacité OLTP en mémoire est prise en charge uniquement dans le niveau Critique pour l’entreprise d’Azure SQL Managed Instance. Elle n’est pas prise en charge dans le niveau Usage général.

Si vous avez des tables à mémoire optimisée ou des types de tables à mémoire optimisée dans votre instance SQL locale et que vous souhaitez migrer vers Azure SQL Managed Instance, vous devez :

- Choisir le niveau Critique pour l’entreprise pour votre instance gérée SQL cible qui prend en charge la capacité OLTP en mémoire.
- Si vous souhaitez effectuer une migration vers le niveau Usage général dans Azure SQL Managed Instance, supprimez les tables à mémoire optimisée, les types de tables à mémoire optimisée et les modules SQL compilés nativement qui interagissent avec les objets à mémoire optimisée avant d’effectuer la migration de vos bases de données. Vous pouvez utiliser la requête T-SQL suivante pour identifier tous les objets qui doivent être supprimés avant la migration vers le niveau Usage général :

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Pour plus d’informations sur les technologies en mémoire, consultez [Optimiser les performances en utilisant les technologies en mémoire d’Azure SQL Database et Azure SQL Managed Instance](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Fonctionnalités avancées 

Veillez à tirer parti des fonctionnalités informatiques avancées d’Azure SQL Managed Instance. Par exemple, vous n’avez plus à vous soucier de la gestion des sauvegardes, car le service le fait à votre place. Vous pouvez effectuer une restauration à n’importe quel [point dans le temps inclus dans la période de rétention](../../database/recovery-using-backups.md#point-in-time-restore). De plus, vous n’avez pas à vous soucier de la configuration de la  [haute disponibilité](../../database/high-availability-sla.md), car cette dernière est intégrée. 

Pour renforcer la sécurité, envisagez d’utiliser l’ [authentification Azure AD](../../database/authentication-aad-overview.md), l’[audit](../../managed-instance/auditing-configure.md), la  [détection des menaces](../../database/azure-defender-for-sql.md), la  [sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security) et le  [masquage dynamique des données](/sql/relational-databases/security/dynamic-data-masking).

En plus des fonctionnalités avancées en matière de sécurité et de gestion, SQL Managed Instance fournit des outils perfectionnés pouvant vous aider dans la [supervision et l’optimisation de votre charge de travail](../../database/monitor-tune-overview.md). [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) vous permet de surveiller un large ensemble d’instances gérées de manière centralisée. Le  [réglage automatique](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) des instances gérées surveille en permanence les performances de l’exécution de vos plans SQL et corrige automatiquement les problèmes de performances identifiés. 

Certaines fonctionnalités sont disponibles uniquement une fois que vous avez fait passer le [niveau de compatibilité de la base de données](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) au dernier niveau (150). 

## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire, consultez les ressources suivantes qui ont été développées pour des projets de migration concrets.

|Asset  |Description  |
|---------|---------|
|[Outil et modèle d’évaluation d’une charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Cet outil fournit des suggestions pour les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données qui sont les mieux adaptés pour une charge de travail. Il propose des fonctionnalités de génération de rapports et de calculs simples en un clic qui permettent d’accélérer l’évaluation des grands patrimoines en fournissant un processus automatisé et uniforme de prise de décision concernant la plateforme cible.|
|[Utilitaire DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Vous pouvez utiliser DBLoader pour charger des données dans SQL Server à partir de fichiers texte délimités. Cet utilitaire de console Windows utilise l’interface de chargement en bloc SQL Server Native Client. L’interface fonctionne sur toutes les versions de SQL Server, ainsi qu’avec Azure SQL Managed Instance.|
|[Utilitaire permettant de déplacer des connexions SQL Server locales vers Azure SQL Managed Instance](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Un script PowerShell peut créer un script de commande T-SQL visant à recréer les connexions et certains utilisateurs de base de données d’un serveur SQL local vers Azure SQL Managed Instance. Cet outil permet de mapper automatiquement des comptes Windows Server Active Directory à des comptes Azure AD et, éventuellement, de migrer des connexions natives SQL Server.|
|[Automatisation de la collecte des données perfmon à l’aide de Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Vous pouvez utiliser l’outil Logman pour collecter des données Perfmon (pour vous aider à comprendre les performances de base) et obtenir des recommandations sur la cible de la migration. Cet outil se sert de logman.exe pour créer la commande qui va créer, démarrer, arrêter et supprimer les compteurs de performances définis sur une instance SQL distante.|
|[Migration de base de données vers Azure SQL Managed Instance en restaurant des sauvegardes complètes et différentielles](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Ce livre blanc fournit une aide et des étapes pour accélérer les migrations de SQL Server vers Azure SQL Managed Instance si vous avez uniquement des sauvegardes différentielles et complètes (et aucune capacité de sauvegarde de fichier journal).|

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme Azure Data de Microsoft.


## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à migrer vos bases de données SQL Server vers Azure SQL Managed Instance, consultez le [guide de migration de SQL Server vers Azure SQL Managed Instance](sql-server-to-managed-instance-guide.md).

- Pour obtenir une matrice des services et des outils qui peuvent vous aider à réaliser des scénarios de migration de bases de données et de données ainsi que des tâches spécialisées, consultez la section [Services et outils de migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL Managed Instance, consultez :
   - [Niveaux de service dans Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Différences entre SQL Server et Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Outil de calcul du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Pour en savoir plus sur l’infrastructure et le cycle d’adoption des migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Pour plus d’informations sur l’exécution des tests A/B au niveau de la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).