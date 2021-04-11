---
title: 'Azure Synapse Analytics : Guide de migration'
description: Suivez ce guide pour migrer vos bases de données vers un pool SQL dédié à Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: aa32083d48c868435ce77114f3a036b8bec47201
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608498"
---
# <a name="migrating-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrer un entrepôt de données vers un pool SQL dédié dans Azure Synapse Analytics 
Les sections suivantes fournissent une vue d’ensemble des éléments impliqués dans la migration d’une solution d'entrepôt de données existante vers un pool SQL dédié à Azure Synapse Analytics.

## <a name="overview"></a>Vue d’ensemble
Avant la migration, vous devez vérifier qu’Azure Synapse Analytics est la meilleure solution pour votre charge de travail. Azure Synapse Analytics est un système distribué conçu pour réaliser des tâches d’analytique sur de grandes quantités de données. La migration vers Azure Synapse Analytics exige d’apporter quelques modifications à la conception. Ces modifications ne sont pas complexes, mais leur implémentation peut prendre du temps. Si votre entreprise requiert un entrepôt de données de niveau entreprise, les avantages obtenus en valent la peine. Toutefois, si vous n’avez pas besoin de la puissance d’Azure Synapse Analytics, il est plus rentable d’utiliser [SQL Server](https://docs.microsoft.com/sql/sql-server/) ou [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

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
- Formats incompatibles (JSON, XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
L’un des obstacles critiques auxquels les clients sont confrontés quand ils migrent d’un système à l’autre est la traduction de leurs objets de base de données. [Azure Synapse Pathway](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) vous aide à effectuer une mise à niveau vers une plateforme d’entrepôt de données moderne en automatisant la traduction de l’objet de votre entrepôt de données existant. Il s’agit d’un outil gratuit, intuitif et facile à utiliser, qui automatise la traduction du code, ce qui permet une migration plus rapide vers Azure Synapse Analytics.

## <a name="prerequisites"></a>Prérequis
# <a name="migrate-from-sql-server"></a>[Migrer à partir de SQL Server](#tab/migratefromSQLServer)
Pour migrer votre entrepôt de données SQL Server vers Azure Synapse Analytics, vérifiez que vous disposez des prérequis suivants : 

- Entrepôt de données ou charge de travail Analytics 
- Télécharger la version la plus récente de l’outil [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pour la migration des objets SQL Server vers des objets Azure Synapse.
- Un [pool SQL dédié](../get-started-create-workspace.md) dans l’espace de travail Azure Synapse. 

# <a name="migrate-from-netezza"></a>[Migrer à partir de Netezza](#tab/migratefromNetezza)
Pour migrer votre entrepôt de données Netezza vers Azure Synapse Analytics, vérifiez que vous disposez des prérequis suivants : 

- Télécharger la version la plus récente de l’outil [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pour la migration des objets SQL Server vers des objets Azure Synapse.
- Un [pool SQL dédié](../get-started-create-workspace.md) dans l’espace de travail Azure Synapse.

Pour plus d’informations, consultez [Solutions Azure Synapse Analytics et migration pour Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrer à partir de Snowflake](#tab/migratefromSnowflake)
Pour migrer votre entrepôt de données Snowflake vers Azure Synapse Analytics, vérifiez que vous disposez des prérequis suivants : 

- Télécharger la version la plus récente de l’outil [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pour la migration des objets Snowflake vers des objets Azure Synapse.
- Un [pool SQL dédié](../get-started-create-workspace.md) dans l’espace de travail Azure Synapse. 

# <a name="migrate-from-oracle"></a>[Migrer à partir d’Oracle](#tab/migratefromOracle)
Pour migrer votre entrepôt de données Oracle vers Azure Synapse Analytics, vérifiez que vous disposez des prérequis suivants : 

- Entrepôt de données ou charge de travail Analytics 
- Télécharger SSMA pour Oracle afin de convertir les objets Oracle vers SQL Server. Pour plus d’informations, consultez [Migration de bases de données Oracle vers SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql). 
- Télécharger la version la plus récente de l’outil [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=102787) pour la migration des objets SQL Server vers des objets Azure Synapse.
- Un [pool SQL dédié](../get-started-create-workspace.md) dans l’espace de travail Azure Synapse. 

Pour plus d’informations, consultez [Solutions Azure Synapse Analytics et migration pour un entrepôt de données Oracle](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Prémigration
Une fois que vous avez décidé de migrer une solution existante vers Azure Synapse Analytics, il est important de planifier la migration avant de commencer. L’objectif principal de la planification est de s’assurer que vos données, vos schémas de table et votre code sont compatibles avec Azure Synapse Analytics. Il existe des différences de compatibilité à contourner entre votre système actuel et Azure Synapse Analytics. De plus, la migration de quantités importantes de données vers Azure prend du temps. Une planification minutieuse permet une migration plus rapide de vos données vers Azure. Un autre objectif clé de la planification est d’ajuster votre conception pour s’assurer que votre solution tire pleinement parti des performances de requêtes élevées offertes par Azure Synapse Analytics. Le développement d’entrepôts de données prenant en charge la mise à l’échelle introduit des modèles uniques de conception, ce qui signifie que les approches traditionnelles ne sont pas toujours les mieux indiquées. Même si vous avez la possibilité d’apporter certains ajustements au niveau de la conception après la migration, effectuer ces modifications plus tôt dans le processus vous permet de gagner du temps par la suite.

## <a name="migrate"></a>Migrate
Pour une migration réussie, vous devez migrer vos schémas de table, votre code et vos données. Pour obtenir des instructions plus détaillées sur ces sujets, consultez :
- L’article [Envisager la conception de tables](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview).
- L’article [Envisager la modification du code](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques).
- L’article [Migration de vos données](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading).
- L’article [Envisager la gestion des charges de travail](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management).

## <a name="additional-resources"></a>Ressources supplémentaires 
- L’équipe de conseil clientèle (CAT, Customer Advisory Team) propose des conseils particulièrement utiles pour Azure Synapse Analytics (anciennement SQL DW) publiés sous forme de billets de blog. Assurez-vous de consulter l’article [Migrating data to Azure SQL Data Warehouse in practice](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice) pour obtenir des conseils supplémentaires sur la migration.

## <a name="migration-assets-from-real-world-engagements"></a>Ressources de migration à partir d’engagements réels
Pour obtenir une aide supplémentaire concernant ce scénario de migration, consultez les ressources suivantes qui ont été développées dans le cadre d’un engagement de projet de migration réel.

| Titre/lien                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Outil et modèle d’évaluation de charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil suggère les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une génération de rapports et des calculs simples en un clic, qui permettent d’accélérer considérablement les évaluations d’un vaste domaine en fournissant un processus de décision automatisé et uniforme concernant le choix de la plateforme cible. |
| [Gestion des problèmes de codage des données lors du chargement de données dans Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ce blog est destiné à fournir des informations sur certains des problèmes de codage des données que vous pouvez rencontrer lors de l’utilisation de PolyBase pour charger des données dans SQL Data Warehouse. Cet article fournit également des options que vous pouvez utiliser pour surmonter ce type de problèmes et charger les données avec succès. |
| [Obtention des tailles de table dans le pool SQL Azure dédié à Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | L’une des tâches clés qu’un architecte doit effectuer consiste à obtenir des métriques concernant un nouvel environnement après la migration : collecte des temps de chargement du site local vers le cloud, collecte des temps de chargement de PolyBase, etc. Parmi ces tâches, l’une des plus importantes est de déterminer la taille de stockage dans SQL Data Warehouse par rapport à la plateforme actuelle du client. |
| [Utilitaire permettant de déplacer des connexions SQL Server locales vers Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Script PowerShell qui crée un script de commande T-SQL visant à recréer les connexions et à sélectionner les utilisateurs de base de données d’un SQL Server local vers un service Azure SQL PaaS. Cet outil permet de mapper automatiquement des comptes Windows AD à des comptes Azure AD ou d’effectuer des recherches de nom d’utilisateur principal (UPN) pour chaque connexion par rapport au Windows Active Directory local. L’outil peut également migrer les connexions natives SQL Server. Les rôles personnalisés de serveur et de base de données font l’objet d’un script, tout comme l’appartenance à un rôle, le rôle de base de données et les autorisations de l’utilisateur. Les bases de données autonomes ne sont pas encore prises en charge et seul un sous-ensemble d’autorisations SQL Server possibles fait l’objet d’un script. Cela signifie que l’octroi d’autorisations avec l’option Grant n’est pas pris en charge (arborescences d’autorisations complexes). Des informations supplémentaires sont disponibles dans le document de support et le script contient des commentaires pour faciliter la compréhension. |

> [!NOTE]
> Les ressources ci-dessus ont été développées dans le cadre du programme de démarrage rapide de la migration des données (DM Jumpstart), commandité par l’équipe d’ingénierie Azure Data Group. La charte fondamentale du programme DM Jumpstart a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux opportunités de migration de plateforme de données vers la plateforme Azure Data de Microsoft. Si vous pensez que votre organisation aimerait participer au programme DM Jumpstart, contactez l’équipe des comptes et demandez-lui de soumettre votre candidature.

## <a name="videos"></a>Vidéos
- Regardez comment [Walgreens a migré son système d’inventaire de détail](https://www.youtube.com/watch?v=86dhd8N1lH4) avec environ 100 To de données de Netezza vers Azure Synapse Analytics (anciennement SQL DW) en un temps record. 