---
title: 'Azure Synapse Analytics : Guide de migration'
description: Suivez ce guide pour migrer vos bases de données vers un pool SQL dédié à Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 5d2dc316d5f58374792e04460f1abd8e63002243
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524202"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrer un entrepôt de données vers un pool SQL dédié dans Azure Synapse Analytics

Les sections suivantes fournissent une vue d’ensemble des éléments impliqués dans la migration d’une solution d’entrepôt de données existante vers un pool SQL dédié à Azure Synapse Analytics.

## <a name="overview"></a>Vue d’ensemble

Avant la migration, vous devez vérifier qu’Azure Synapse Analytics est la meilleure solution pour votre charge de travail. Azure Synapse Analytics est un système distribué conçu pour réaliser des tâches d’analytique sur de grandes quantités de données. La migration vers Azure Synapse Analytics exige d’apporter quelques modifications à la conception. Ces modifications ne sont pas complexes, mais leur implémentation peut prendre du temps. Si votre entreprise requiert un entrepôt de données de niveau entreprise, les avantages obtenus en valent la peine. Toutefois, si vous n’avez pas besoin de la puissance d’Azure Synapse Analytics, il est plus rentable d’utiliser [SQL Server](/sql/sql-server/) ou [Azure SQL Database](../../azure-sql/index.yml).

Envisagez d’utiliser Azure Synapse Analytics dans les cas suivants :

- Vous avez au moins un téraoctet (To) de données.
- Vous prévoyez d’effectuer des tâches d’analytique sur de grandes quantités de données.
- Vous avez besoin de pouvoir mettre à l’échelle les ressources de calcul et de stockage.
- Vous souhaitez réduire les coûts en interrompant les ressources de calcul quand vous n’en avez pas besoin.

À la place d’Azure Synapse Analytics, envisagez d’autres options pour les charges de travail opérationnelles (OLTP) qui présentent ces caractéristiques :

- Lectures et écritures haute fréquence.
- Grand nombre de sélections singleton.
- Volumes élevés d’insertions d’une seule ligne.
- Besoins de traitement ligne par ligne.
- Formats incompatibles (par exemple, JSON et XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

L’un des obstacles critiques auxquels les clients sont confrontés lors d’une migration d’un système à l’autre est la traduction de leurs objets de base de données. [Azure Synapse Pathway](/sql/tools/synapse-pathway/azure-synapse-pathway-overview) vous aide à effectuer une mise à niveau vers une plateforme d’entrepôt de données moderne en automatisant la traduction de l’objet de votre entrepôt de données existant. Il s’agit d’un outil gratuit, intuitif et facile à utiliser, qui automatise la traduction du code afin d’accélérer la migration vers Azure Synapse Analytics.

## <a name="prerequisites"></a>Prérequis

# <a name="migrate-from-sql-server"></a>[Migrer à partir de SQL Server](#tab/migratefromSQLServer)

Pour migrer votre entrepôt de données SQL Server vers Azure Synapse Analytics, vérifiez que vous disposez des prérequis suivants :

- Entrepôt de données ou charge de travail analytique
- Téléchargez la version la plus récente de l’outil [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061) pour la migration des objets SQL Server vers des objets Azure Synapse.
- [Pool SQL dédié](../get-started-create-workspace.md) dans un espace de travail Azure Synapse.

# <a name="migrate-from-netezza"></a>[Migrer à partir de Netezza](#tab/migratefromNetezza)

Pour migrer votre entrepôt de données Netezza vers Azure Synapse Analytics, vérifiez que vous disposez des prérequis suivants :

- Téléchargez la version la plus récente de l’outil [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061) pour la migration des objets SQL Server vers des objets Azure Synapse.
- [Pool SQL dédié](../get-started-create-workspace.md) dans un espace de travail Azure Synapse.

Pour plus d’informations, consultez [Solutions Azure Synapse Analytics et migration pour Netezza](/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrer à partir de Snowflake](#tab/migratefromSnowflake)

Pour migrer votre entrepôt de données Snowflake vers Azure Synapse Analytics, vérifiez que vous disposez des prérequis suivants :

- Téléchargez la version la plus récente de l’outil [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061) pour la migration des objets Snowflake vers des objets Azure Synapse.
- [Pool SQL dédié](../get-started-create-workspace.md) dans un espace de travail Azure Synapse.

# <a name="migrate-from-oracle"></a>[Migrer à partir d’Oracle](#tab/migratefromOracle)

Pour migrer votre entrepôt de données Oracle vers Azure Synapse Analytics, vérifiez que vous disposez des prérequis suivants :

- Entrepôt de données ou charge de travail analytique
- Téléchargez l’Assistant Migration SQL Server pour Oracle afin de convertir les objets Oracle vers SQL Server. Pour plus d’informations, consultez [Migration de bases de données Oracle vers SQL Server (OracleToSQL)](/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Téléchargez la version la plus récente de l’outil [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=103061) pour la migration des objets SQL Server vers des objets Azure Synapse.
- [Pool SQL dédié](../get-started-create-workspace.md) dans un espace de travail Azure Synapse.

Pour plus d’informations, consultez [Solutions Azure Synapse Analytics et migration pour un entrepôt de données Oracle](/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Prémigration

Une fois que vous avez décidé de migrer une solution existante vers Azure Synapse Analytics, vous devez planifier votre migration avant de commencer. L’objectif principal de la planification est de s’assurer que vos données, vos schémas de table et votre code sont compatibles avec Azure Synapse Analytics. Il existe des différences de compatibilité à contourner entre votre système actuel et Azure Synapse Analytics. De plus, la migration de quantités importantes de données vers Azure prend du temps. Une planification minutieuse permet une migration plus rapide de vos données vers Azure.

Un autre objectif clé de la planification est d’ajuster votre conception pour s’assurer que votre solution tire pleinement parti des performances de requêtes élevées offertes par Azure Synapse Analytics. Le développement d’entrepôts de données prenant en charge la mise à l’échelle introduit des modèles uniques de conception, ce qui signifie que les approches traditionnelles ne sont pas toujours les mieux indiquées. Même si vous avez la possibilité d’apporter certains ajustements au niveau de la conception après la migration, effectuer ces modifications plus tôt dans le processus vous permet de gagner du temps par la suite.

## <a name="migrate"></a>Migrate

Pour une migration réussie, vous devez migrer vos schémas de table, votre code et vos données. Pour obtenir des instructions plus détaillées sur ces sujets, consultez les articles suivants :

- [Envisager la conception de tables](../sql-data-warehouse/sql-data-warehouse-tables-overview.md)
- [Envisager la modification du code](../sql-data-warehouse/sql-data-warehouse-overview-develop.md#development-recommendations-and-coding-techniques)
- [Migrer vos données](../sql-data-warehouse/design-elt-data-loading.md)
- [Envisager la gestion des charges de travail](../sql-data-warehouse/sql-data-warehouse-workload-management.md)

## <a name="more-resources"></a>Plus de ressources

L’équipe de conseil clientèle (Customer Advisory Team) propose des conseils particulièrement utiles pour Azure Synapse Analytics (anciennement Azure SQL Data Warehouse) publiés sous forme de billets de blog. Pour plus d’informations sur la migration, consultez [Migration de données vers Azure SQL Data Warehouse](/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Ressources de migration à partir d’engagements réels

Pour plus d’informations sur l’exécution de ce scénario de migration, consultez les ressources suivantes. Elles ont été développées pour soutenir un engagement de projet de migration réel.

| Titre/lien                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Outil et modèle d’évaluation de charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil fournit des suggestions pour les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données qui sont les mieux adaptés pour une charge de travail donnée. Il propose des fonctionnalités de génération de rapports et de calculs simples en un clic qui permettent d’accélérer les évaluations d’un vaste domaine en fournissant un processus de décision de plateforme cible automatisé et uniforme. |
| [Gestion des problèmes de codage des données lors du chargement de données dans Azure Synapse Analytics](https://azure.microsoft.com/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ce billet de blog fournit des informations sur certains des problèmes de codage des données que vous pouvez rencontrer lors de l’utilisation de PolyBase pour charger des données dans SQL Data Warehouse. Cet article fournit également des options que vous pouvez utiliser pour surmonter ce type de problèmes et charger les données avec succès. |
| [Obtention des tailles de table dans le pool SQL Azure dédié à Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | L’une des tâches clés qu’un architecte doit effectuer consiste à obtenir des mesures sur un nouvel environnement après la migration. Les exemples incluent la collecte des temps de chargement depuis un site local vers le cloud et la collecte des temps de chargement Polybase. L’une des tâches les plus importantes est de déterminer la taille de stockage dans SQL Data Warehouse par rapport à la plateforme actuelle du client. |
| [Utilitaire permettant de déplacer des connexions SQL Server locales vers Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Un script PowerShell crée un script de commande T-SQL visant à recréer les connexions et certains utilisateurs de base de données d’une instance SQL Server locale vers un service Azure SQL PaaS. Cet outil permet de mapper automatiquement des comptes Windows Server Active Directory à des comptes Azure Active Directory ou d’effectuer des recherches de nom d’utilisateur principal (UPN) pour chaque connexion par rapport au Windows Server Active Directory local. L’outil peut également migrer les connexions natives SQL Server. Les rôles personnalisés de serveur et de base de données font l’objet d’un script, tout comme l’appartenance à un rôle, le rôle de base de données et les autorisations de l’utilisateur. Les bases de données autonomes ne sont pas prises en charge et seul un sous-ensemble des autorisations SQL Server possibles est scripté. Des informations supplémentaires sont disponibles dans le document de support et le script contient des commentaires pour faciliter la compréhension. |

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme Azure Data de Microsoft.

## <a name="videos"></a>Vidéos

Regardez comment [Walgreens a migré son système d’inventaire](https://www.youtube.com/watch?v=86dhd8N1lH4) avec environ 100 To de données de Netezza vers Azure Synapse Analytics en un temps record.
