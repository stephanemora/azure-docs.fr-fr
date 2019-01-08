---
title: Concevoir sa première base de données SQL Azure - C# | Microsoft Docs
description: Apprenez à concevoir votre première base de données SQL Azure et vous y connecter à l’aide d’un programme C# utilisant ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 12/10/2018
ms.openlocfilehash: cf180f6e2970ac4435602f1cceeb98a4dd9e8724
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727163"
---
# <a name="tutorial-design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Didacticiel : Concevoir une base de données SQL Azure et se connecter avec C&#x23; et ADO.NET

Azure SQL Database est une solution DBaaS relationnelle dans Microsoft Cloud (Azure). Ce didacticiel vous montre comment utiliser le portail Azure et ADO.NET avec Visual Studio pour :

> [!div class="checklist"]
> * Créer une base de données dans le portail Azure
> * Configurer une règle de pare-feu au niveau du serveur dans le portail Azure
> * Se connecter à la base de données avec ADO.NET et Visual Studio
> * Créer des tables avec ADO.NET
> * Insérer, mettre à jour et supprimer des données avec ADO.NET
> * Interroger les données avec ADO.NET

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Une installation de [Visual Studio 2017](https://www.visualstudio.com/downloads/)

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]

<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer certaines tâches de base sur les bases de données, comme créer une base de données et des tables,vous connecter à la base de données, charger des données et exécuter des requêtes. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une base de données
> * Configurer une règle de pare-feu
> * Se connecter à la base de données avec [Visual Studio et C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Créer des tables
> * Insérer, mettre à jour, supprimer et interroger des données

Passez au tutoriel suivant pour en savoir plus sur la migration des données.

> [!div class="nextstepaction"]
> [Migrer SQL Server vers Azure SQL Database hors connexion à l’aide de DMS](../dms/tutorial-sql-server-to-azure-sql.md)
