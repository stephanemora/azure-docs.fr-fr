---
title: Installer Visual Studio 2019
description: Installer les outils de développement Visual Studio et SQL Server (SSDT) pour SQL Analytics
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d0a1772706fa838f51322d5f5d5bd1b46eb9144a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351627"
---
# <a name="getting-started-with-visual-studio-2019"></a>Prise en main de Visual Studio 2019
Visual Studio **2019** SQL Server Data Tools (SSDT) est un outil unique qui vous permet d’effectuer les opérations suivantes :

- Connecter, interroger et développer des applications
- Utiliser un explorateur d’objets pour explorer visuellement tous les objets de votre modèle de données, notamment les tables, les vues, les procédures stockées, etc.
- Générer des scripts DDL (Data Definition Language) T-SQL pour vos objets
- Développer votre entrepôt de données à l’aide d’une approche basée sur l’état avec des projets de base de données SSDT
- Intégrer votre projet de base de données à des systèmes de contrôle de code source comme Git avec Azure Repos
- Configurer des pipelines d’intégration et de déploiement continus avec des serveurs d’automatisation comme Azure DevOps

## <a name="install-visual-studio-2019"></a>Installer Visual Studio 2019
Consultez [Télécharger Visual Studio 2019][] pour télécharger et installer Visual Studio **16.3 ou version ultérieure**. Lors de l’installation, sélectionnez la charge de travail de stockage et de traitement des données. L’installation SSDT autonome n’est plus obligatoire dans Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Fonctionnalités non prises en charge sur SSDT

Certaines mises en production de fonctionnalités SQL Analytics peuvent ne pas prendre en charge SSDT. Actuellement, les fonctionnalités suivantes ne sont pas prises en charge :

- [Affichages matérialisés](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (en cours)
- [Index columnstore en cluster ordonné](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (en cours)
- [Instruction COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (en cours)
- [Gestion des charges de travail](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) : classifieurs et groupes de charge de travail (en cours)
- [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Envoyez un ticket de support ou votez [ici](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) pour obtenir la fonctionnalité prise en charge.
- [Masquage des données dynamiques](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Envoyez un ticket de support ou votez [ici](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) pour obtenir la fonctionnalité prise en charge. 
- Fonction [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- Les [tables avec contraintes](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) ne sont pas prises en charge. Pour ces objets de table, définissez l’action de génération sur « Aucune ».

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez de la dernière version de SSDT, vous êtes prêt à vous [connecter](sql-data-warehouse-query-visual-studio.md) à votre pool SQL.




<!--Other-->

[Télécharger Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
