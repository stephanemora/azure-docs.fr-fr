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
ms.date: 05/29/2019
ms.openlocfilehash: 1d4b17cf1e0349bf877c676cb4e591fc20ad4113
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416367"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Démarrage rapide : Utiliser R pour interroger une base de données SQL Azure (préversion)

 Ce démarrage rapide explique comment utiliser [R](https://www.r-project.org/) avec Machine Learning Services pour se connecter à une base de données SQL Azure et recourir à des instructions Transact-SQL pour interroger des données. Machine Learning Services est une fonctionnalité Azure SQL Database pour l’exécution de scripts R dans les bases de données. Pour en savoir plus, consultez [Azure SQL Database Machine Learning Services avec R (préversion)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vérifiez que vous avez :

- base de données Azure SQL. Utilisez l’un de ces guides de démarrage rapide pour créer et configurer une base de données dans Azure SQL Database :

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Base de données unique |
  |:--- |:--- |
  | Créer| [Portal](sql-database-single-database-get-started.md) |
  || [INTERFACE DE LIGNE DE COMMANDE](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Configuration | [Règle de pare-feu IP au niveau du serveur](sql-database-server-level-firewall-rule.md) |
  | Charger des données | Adventure Works chargé dans le cadre du guide de démarrage rapide |
  |||

  > [!NOTE]
  > Dans la préversion d’Azure SQL Database Machine Learning Services avec R, l’option de déploiement d’instance gérée n’est pas prise en charge.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

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
