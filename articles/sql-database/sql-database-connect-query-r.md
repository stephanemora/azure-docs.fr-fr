---
title: Utiliser R pour interroger Azure SQL Database
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Cet article vous explique comment utiliser un script R pour vous connecter à une base de données Azure SQL et l’interroger à l’aide d’instructions Transact-SQL.
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
ms.date: 04/11/2019
ms.openlocfilehash: 2b1206e3087b0573736174d4eed502653d76f7a5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001183"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Démarrage rapide : Utiliser R pour interroger une base de données SQL Azure (préversion)

 Ce démarrage rapide explique comment utiliser [R](https://www.r-project.org/) avec Machine Learning Services pour se connecter à une base de données SQL Azure et recourir à des instructions Transact-SQL pour interroger des données. Machine Learning Services est une fonctionnalité Azure SQL Database pour l’exécution de scripts R dans les bases de données. Pour en savoir plus, consultez [Azure SQL Database Machine Learning Services avec R (préversion)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vérifiez que vous avez :

- base de données Azure SQL. Utilisez l’un de ces guides de démarrage rapide pour créer et configurer une base de données dans Azure SQL Database :

  || Base de données unique | Instance gérée |
  |:--- |:--- |:---|
  | Créer| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [INTERFACE DE LIGNE DE COMMANDE](scripts/sql-database-create-and-configure-database-cli.md) | [INTERFACE DE LIGNE DE COMMANDE](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configuration | [Règle de pare-feu IP au niveau du serveur](sql-database-server-level-firewall-rule.md)| [Connectivité à partir d’une machine virtuelle](sql-database-managed-instance-configure-vm.md)|
  |||[Connectivité à partir d’une machine locale](sql-database-managed-instance-configure-p2s.md)
  |||

- Machine Learning Services (avec R) activé. Pendant la préversion publique, Microsoft vous intégrera et activera le machine learning sur votre base de données nouvelle ou existante. Suivez les étapes pour vous [inscrire à la préversion](sql-database-machine-learning-services-overview.md#signup).

- La dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Vous pouvez exécuter des scripts R à l’aide d’autres outils de gestion de base de données ou de requête, mais dans ce démarrage rapide, vous allez utiliser SSMS.

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Accédez à la page **Bases de données SQL** ou **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom complet du serveur à côté de **Nom du serveur** pour une base de données, ou le nom complet du serveur à côté de **Hôte** pour une instance managée. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**.

## <a name="create-code-to-query-your-sql-database"></a>Créer du code pour interroger votre base de données SQL

1. Ouvrez **SQL Server Management Studio**, puis connectez-vous à votre base de données SQL.

   Si vous avez besoin d’aide pour la connexion, consultez [Démarrage rapide : utiliser SQL Server Management Studio pour se connecter à une base de données Azure SQL et l’interroger](sql-database-connect-query-ssms.md).

1. Transmettez la totalité du script R à la procédure stockée [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Le script est transmis par le biais de l’`@script`argument. Tout le contenu de l’argument `@script` doit être du code R valide.

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

- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Machine Learning Services (avec R) dans Azure SQL Database](sql-database-machine-learning-services-overview.md)
- [Créer et exécuter des scripts R simples dans Azure SQL Database Machine Learning Services (préversion)](sql-database-quickstart-r-create-script.md)
- [Écrire des fonctions R avancées dans Azure SQL Database avec Machine Learning Services (préversion)](sql-database-machine-learning-services-functions.md)
