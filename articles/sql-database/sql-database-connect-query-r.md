---
title: Utiliser R avec Machine Learning Services pour interroger une base de données (préversion)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Cet article explique comment utiliser un script R avec le service Machine Learning Services dans Azure SQL Database pour vous connecter à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768510"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Démarrage rapide : Utiliser R avec Machine Learning Services pour interroger Azure SQL Database (préversion)

Dans ce démarrage rapide, vous utilisez R avec Machine Learning Services pour vous connecter à une base de données Azure SQL et vous utilisez des instructions T-SQL pour interroger les données.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez gratuitement un compte](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Une [base de données Azure SQL](sql-database-single-database-get-started.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) avec R activé. [Inscrivez-vous à la préversion](sql-database-machine-learning-services-overview.md#signup).
- [SSMS](/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio)

> [!IMPORTANT]
> Les scripts fournis dans cet article utilisent la base de données **Adventure Works**.

> [!NOTE]
> Pendant la préversion publique, Microsoft va vous intégrer et activer l’apprentissage automatique pour votre base de données existante ou nouvelle, mais l’option de déploiement d’instance gérée n’est pas prise en charge pour le moment.

Machine Learning Services avec R est une fonctionnalité de base de données Azure SQL utilisée pour exécuter des scripts R dans les bases de données. Pour plus d’informations, consultez le [projet R](https://www.r-project.org/).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à la page **Bases de données SQL** ou **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom complet du serveur à côté de **Nom du serveur** pour une base de données, ou le nom complet du serveur à côté de **Hôte** pour une instance managée. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**.

## <a name="create-code-to-query-your-sql-database"></a>Créer du code pour interroger votre base de données SQL

1. Ouvrez **SQL Server Management Studio**, puis connectez-vous à votre base de données SQL.

   Si vous avez besoin d’aide pour la connexion, consultez [Démarrage rapide : utiliser SQL Server Management Studio pour se connecter à une base de données Azure SQL et l’interroger](sql-database-connect-query-ssms.md).

1. Transmettez la totalité du script R à la procédure stockée [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Le script est transmis via l’argument `@script`. Tout ce qui se trouve dans l’argument `@script` doit être du code R valide.
   
   >[!IMPORTANT]
   >Dans cet exemple, le code utilise les exemples de données AdventureWorksLT que vous pouvez choisir comme source lors de la création de votre base de données. Si votre base de données comporte des données différentes, utilisez des tables de votre propre base de données dans la requête SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Si vous rencontrez des erreurs, cela peut être dû au fait que la préversion publique de Machine Learning Services (avec R) n’est pas activée pour votre base de données SQL. Consultez [Composants requis](#prerequisites) ci-dessus.

## <a name="run-the-code"></a>Exécuter le code

1. Exécutez la procédure stockée [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

1. Vérifiez que les 20 premières lignes catégorie/produit sont retournées dans la fenêtre **Messages**.

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données Azure SQL](sql-database-design-first-database.md)
- [Machine Learning Services (avec R) dans Azure SQL Database](sql-database-machine-learning-services-overview.md)
- [Créer et exécuter des scripts R simples dans Azure SQL Database Machine Learning Services (préversion)](sql-database-quickstart-r-create-script.md)
- [Écrire des fonctions R avancées dans Azure SQL Database avec Machine Learning Services (préversion)](sql-database-machine-learning-services-functions.md)
