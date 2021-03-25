---
title: Guide de migration de SQL Server vers Azure Synapse Analytics
description: Suivez ce guide pour migrer vos bases de données SQL Server vers un pool SQL Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 9a7888d3ccf7e033f15f184227c65c746780aa12
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418026"
---
# <a name="migration-guide-sql-server-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Guide de migration : SQL Server vers un pool SQL dédié dans Azure Synapse Analytics 
Les sections suivantes fournissent une vue d’ensemble des éléments impliqués dans la migration d’une solution de base de données de l'entrepôt de données SQL Server existante vers un pool SQL Azure Synapse Analytics.

## <a name="overview"></a>Vue d’ensemble
Avant la migration, vous devez vérifier qu’Azure Synapse Analytics est la meilleure solution pour votre charge de travail. Azure Synapse Analytics est un système distribué conçu pour réaliser des tâches d’analytique sur de grandes quantités de données. La migration vers Azure Synapse Analytics exige d’apporter quelques modifications à la conception. Ces modifications ne sont pas complexes, mais leur implémentation peut prendre du temps. Si votre entreprise requiert un entrepôt de données de niveau entreprise, les avantages obtenus en valent la peine. Toutefois, si vous n’avez pas besoin de la puissance d’Azure Synapse Analytics, il est plus rentable d’utiliser [SQL Server](/sql/sql-server/) ou [Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview).

Envisagez d’utiliser Azure Synapse Analytics dans les cas suivants :
- Possédez plusieurs To de données.
- Envisagez de réaliser des analyses sur de grandes quantités de données.
- Devez être capable de mettre à l’échelle les calculs et le stockage.
- Souhaitez réduire les coûts en interrompant les ressources de calculs, si vous n’en avez pas besoin.

Envisagez d’autres options plutôt qu’Azure Synapse Analytics pour les charges de travail opérationnelles (OLTP) qui ont :
- Des lectures et écritures haute fréquence.
- Un grand nombre de sélections singleton.
- Des volumes élevés d’insertions à une seule ligne.
- Des besoins de traitement ligne par ligne.
- Des formats incompatibles (JSON, XML).

## <a name="prerequisites"></a>Prérequis
Pour pouvoir migrer votre serveur SQL Server vers Azure Synapse Analytics, vous devez disposer des prérequis suivants : 

- Une base de données de l'entrepôt de données ou charge de travail Analytics 
- Dernière version de l’outil [Azure Synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pour la migration des objets SQL Server vers des objets Azure Synapse.
- Un [pool SQL dédié](../get-started-create-workspace.md) dans l’espace de travail Azure Synapse. 

## <a name="pre-migration"></a>Prémigration
Une fois que vous avez décidé de migrer une solution existante vers Azure Synapse Analytics, il est important de planifier la migration avant de commencer. L’objectif principal de la planification est de s’assurer que vos données, vos schémas de table et votre code sont compatibles avec Azure Synapse Analytics. Il existe des différences de compatibilité à contourner entre votre système actuel et SQL Data Warehouse. De plus, migrer d’importantes quantités de données vers Azure prend du temps. Une planification minutieuse permet un transfert plus rapide de vos données vers Azure. Un autre objectif clé de la planification est d’ajuster votre conception pour s’assurer que votre solution tire pleinement parti des performances de requête élevées offertes par Azure Synapse Analytics. Le développement d’entrepôts de données prenant en charge la mise à l’échelle introduit des modèles uniques de conception, ce qui signifie que les approches traditionnelles ne sont pas toujours les mieux indiquées. Même si vous avez la possibilité d’apporter certaines modifications au niveau de la conception après la migration, apporter ces modifications plus tôt dans le processus vous fera par la suite gagner du temps.

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
L’un des bloqueurs critiques auxquels les clients sont confrontés quand ils migrent d’un système à l’autre est la traduction de leur code SQL. [Azure Synapse Pathway](/sql/tools/synapse-pathway/azure-synapse-pathway-overview) vous aide à effectuer une mise à niveau vers une plateforme d’entrepôt de données moderne en automatisant la traduction du code de votre entrepôt de données existant. Il s’agit d’un outil gratuit, intuitif et facile à utiliser, qui automatise la traduction du code, ce qui permet une migration plus rapide vers Azure Synapse Analytics.

## <a name="migrate"></a>Migrate
Pour une migration réussie, vous devez migrer vos schémas de table, votre code et vos données. Pour obtenir des instructions plus détaillées sur ces sujets, consultez :
- L’article [Migration de vos schémas](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- L’article [Migration de votre code](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- L’article [Migration de vos données](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).

## <a name="additional-resources"></a>Ressources supplémentaires 
- L’équipe de conseil clientèle (CAT, Customer Advisory Team) propose des conseils d’aide incontournables pour Azure Synapse Analytics (anciennement SQL Data Warehouse) publiés sous forme de billets de blog. Assurez-vous de consulter l’article [Migrating data to Azure SQL Data Warehouse in practice](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice) pour obtenir des conseils supplémentaires sur la migration.
- Consultez le livre blanc [Choix du chemin de migration de votre base de données vers Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) pour obtenir des informations supplémentaires et des recommandations.
- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez l’article [Services et outils disponibles pour la migration de données](https://docs.microsoft.com/azure/dms/dms-tools-matrix). 

## <a name="migration-assets-from-real-world-engagements"></a>Ressources de migration à partir d’engagements réels
Pour obtenir une aide supplémentaire concernant la réalisation de ce scénario de migration, consultez les ressources suivantes qui ont été développées dans le cadre d’un engagement de projet de migration réel.

| Titre/lien                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Outil et modèle d’évaluation d’une charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil fournit les plates-formes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une génération de rapports et des calculs simples en un clic qui permettent d’accélérer considérablement les évaluations d’un vaste domaine en fournissant un processus de décision de plate-forme cible automatisé et uniforme. |
| [Gestion des problèmes d’encodage des données lors du chargement de données dans Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ce blog est destiné à fournir des insights sur certains des problèmes d’encodage des données que vous pouvez rencontrer lors de l’utilisation de PolyBase pour charger des données dans SQL Data Warehouse. Cet article fournit également des options que vous pouvez utiliser pour surmonter de tels problèmes et charger les données avec succès. |
| [Obtention des tailles de table dans le pool SQL Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | L’une des tâches clés qu’un architecte doit effectuer consiste à obtenir des mesures concernant un nouvel environnement après la migration : collecte des temps de chargement du site local vers le cloud, collecte des temps de chargement de PolyBase, etc. Parmi ces tâches, l’une des plus importantes est de déterminer la taille de stockage dans SQL Data Warehouse par rapport à la plate-forme actuelle du client. |
| [Utilitaire permettant de déplacer des connexions SQL Server locales vers Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Script PowerShell qui crée un script de commande T-SQL visant à recréer les connexions et à sélectionner les utilisateurs de base de données d’un serveur SQL local vers un service PaaS Azure SQL. Cet outil permet de mapper automatiquement des comptes Windows AD à des comptes Azure AD ou d’effectuer des recherches de nom d'utilisateur principal (UPN) pour chaque connexion au niveau local de Windows Active Directory. L’outil peut également migrer les connexions natives SQL Server. Les rôles de serveur et de base de données personnalisés font l’objet d’un script, tout comme l’appartenance à un rôle, le rôle de base de données et les autorisations de l’utilisateur. Les bases de données autonomes ne sont pas encore prises en charge et seul un sous-ensemble d’autorisations SQL Server possibles fait l’objet d’un script. C’est-à-dire que l’octroi d’autorisations avec l’option Grant n’est pas pris en charge (arborescences d’autorisations complexes). Des informations supplémentaires sont disponibles dans le document de support et le script contient des commentaires pour faciliter la compréhension. |

> [!NOTE]
> Ces ressources ci-dessus ont été développées dans le cadre du programme de démarrage rapide de la migration des données (DM Jumpstart), sous la direction de l’équipe Azure Data Group engineering. La charte fondamentale du programme DM Jumpstart a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux opportunités de migration de plate-forme de données vers la plate-forme de données Azure de Microsoft. Si vous pensez que votre organisation aimerait participer au programme DM Jumpstart, contactez votre équipe en charge de votre compte et demandez-lui de soumettre une candidature.

## <a name="videos"></a>Vidéos
- Pour obtenir une vue d’ensemble du Guide de migration des bases de données Azure et des informations qu’il contient, consultez la vidéo [Guide pratique pour utiliser le Guide de migration des bases données](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/).
- Pour une vue d’ensemble des phases du processus de migration et des détails sur les outils et services spécifiques recommandés pour effectuer l’évaluation et la migration, consultez la vidéo [Vue d’ensemble du parcours de migration et des outils/services recommandés pour l’exécution de l’évaluation et de la migration](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).