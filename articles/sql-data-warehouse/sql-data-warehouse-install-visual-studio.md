---
title: Installer Visual Studio 2019 pour SQL Data Warehouse | Microsoft Docs
description: Installer les outils de développement Visual Studio et SSDT pour Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 02caa7154ef9cb8419b533f0cb2d0fd57e86bf9f
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563336"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Bien démarrer avec Visual Studio 2019 pour SQL Data Warehouse
Visual Studio **2019** SQL Server Data Tools (SSDT) est un outil unique qui vous permet d’effectuer les opérations suivantes :

- Connecter, interroger et développer des applications pour SQL Data Warehouse 
- Utiliser un explorateur d’objets pour explorer visuellement tous les objets de votre modèle de données, notamment les tables, les vues, les procédures stockées, etc.
- Générer des scripts DDL (Data Definition Language) T-SQL pour vos objets
- Développer votre entrepôt de données à l’aide d’une approche basée sur l’état avec des projets de base de données SSDT
- Intégrer votre projet de base de données à des systèmes de contrôle de code source comme Git avec les référentiels Azure DevOps
- Configurer des pipelines d’intégration et de déploiement continus avec des serveurs d’automatisation comme Azure DevOps [bientôt disponible]

> [!NOTE]
> Les projets de base de données Visual Studio SSDT sont actuellement en préversion. Pour recevoir des mises à jour périodiques sur cette fonctionnalité, votez sur [UserVoice].

## <a name="install-visual-studio-2019"></a>Installer Visual Studio 2019
Consultez [Télécharger Visual Studio 2019][] pour télécharger et installer Visual Studio. Lors de l’installation, sélectionnez la charge de travail de stockage et de traitement des données. L’installation SSDT autonome n’est plus obligatoire dans Visual Studio 2019.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Signalement de problèmes avec SSDT Visual Studio 2019 (préversion)

Pour signaler des problèmes lors de l’utilisation de SSDT avec SQL Data Warehouse, envoyez un e-mail à la liste de distribution suivante : <sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez de la dernière version de SSDT, vous êtes prêt à vous [connecter][connect] à SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Télécharger Visual Studio 2019]: https://visualstudio.microsoft.com/vs/preview/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
