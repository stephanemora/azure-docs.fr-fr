---
title: Installer Visual Studio et SSDT pour SQL Data Warehouse | Microsoft Docs
description: Installer les outils de développement Visual Studio et SSDT pour Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: e27a3d4f2fe4b8421b637e485d77d6376d15bf9a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461489"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installer Visual Studio et SSDT pour SQL Data Warehouse
Pour développer des applications pour SQL Data Warehouse, nous recommandons l’utilisation de la version la plus récente de Visual Studio avec la dernière version de SQL Server Data Tools (SSDT).  Visual Studio 2013 Update 5 avec SSDT est également pris en charge pour la compatibilité descendante.  

L’utilisation de Visual Studio avec SSDT vous permet d’utiliser l’Explorateur d’objets SQL Server pour explorer visuellement les tables, les vues, les procédures stockées et un plus grand nombre d’objets dans SQL Data Warehouse, ainsi que pour exécuter des requêtes.

> [!NOTE]
> SQL Data Warehouse ne prend pas actuellement en charge les projets de base de données Visual Studio. Pour recevoir des mises à jour périodiques sur cette fonctionnalité, votez sur [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Étape 1 : Installation de Visual Studio
Suivez ces liens pour télécharger et installer Visual Studio. Si Visual Studio 2013 ou ultérieur est déjà installé sur votre machine, passez à l’étape 2 pour installer SSDT.

1. [Téléchargez Visual Studio][].
2. Suivez le guide [d’installation de Visual Studio][Installing Visual Studio] sur MSDN et sélectionnez les configurations par défaut.

## <a name="step-2-install-ssdt"></a>Étape 2 : Installer SSDT
Pour installer SSDT pour Visual Studio, commencez par vérifier la présence d’une mise à jour SSDT dans Visual Studio en procédant comme suit.

1. Dans Visual Studio, cliquez sur **Outils** / **Extensions et mises à jour...** / **Mises à jour**
2. Sélectionnez **Mises à jour du produit** puis recherchez **Mise à jour Microsoft SQL Server pour les outils de base de données**.

Si aucune mise à jour n’est trouvée, vous devez avoir installé la version la plus récente.  Pour vérifier que SSDT est installé, cliquez sur **Aide** / **À propos de Microsoft Visual Studio** et recherchez SQL Server Data Tools dans la liste. La dernière version de SSDT est 14.0.60525.0. Si l’option d’installation n’est pas disponible à partir de Visual Studio, vous pouvez visiter la page [Téléchargement SSDT][SSDT Download] pour télécharger et installer SSDT manuellement.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous disposez de la dernière version de SSDT, vous êtes prêt à vous [connecter][connect] à SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Téléchargez Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
