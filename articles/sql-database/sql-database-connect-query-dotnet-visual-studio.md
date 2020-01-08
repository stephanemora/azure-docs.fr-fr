---
title: Utiliser Visual Studio avec .NET et C# pour interroger
description: Utilisez Visual Studio pour créer une application en C# qui se connecte à une base de données Azure SQL et l’interroge avec des instructions Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/31/2019
ms.openlocfilehash: b37430ed7f23088c9bcacd555d68e484310de700
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562151"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de NET et C# dans Visual Studio pour se connecter à une base de données Azure SQL et l’interroger

Ce guide de démarrage rapide montre comment utiliser [l’infrastructure .NET](https://www.microsoft.com/net/) et le code C# dans Visual Studio pour interroger une base de données Azure SQL avec des instructions Transact-SQL.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- base de données Azure SQL. Utilisez l’un de ces guides de démarrage rapide pour créer et configurer une base de données dans Azure SQL Database :

  || Base de données unique | Instance gérée |
  |:--- |:--- |:---|
  | Créer| [Portail](sql-database-single-database-get-started.md) | [Portail](sql-database-managed-instance-get-started.md) |
  || [INTERFACE DE LIGNE DE COMMANDE](scripts/sql-database-create-and-configure-database-cli.md) | [INTERFACE DE LIGNE DE COMMANDE](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurer | [Règle de pare-feu IP au niveau du serveur](sql-database-server-level-firewall-rule.md)| [Connectivité à partir d’une machine virtuelle](sql-database-managed-instance-configure-vm.md)|
  |||[Connectivité à partir d’une machine locale](sql-database-managed-instance-configure-p2s.md)
  |Charger les données|Adventure Works chargé dans le cadre du guide de démarrage rapide|[Restaurer Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restaurer ou importer Adventure Works à partir du fichier [BACPAC](sql-database-import.md) disponible sur [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Les scripts fournis dans cet article utilisent la base de données Adventure Works. Avec une instance managée, vous devez importer la base de données Adventure Works dans une base de données d’instance, ou modifier les scripts fournis dans cet article pour utiliser la base de données Wide World Importers.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) édition Community, Professional ou Enterprise.

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à la page **Bases de données SQL** ou **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom complet du serveur à côté de **Nom du serveur** pour une base de données, ou le nom complet du serveur à côté de **Hôte** pour une instance managée. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**. 

## <a name="create-code-to-query-the-sql-database"></a>Créer du code pour interroger la base de données SQL

1. Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**. 
   
1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#** , puis **Application console (.NET Framework)** .
   
1. Entrez *sqltest* pour le nom du projet puis sélectionnez **OK**. Le nouveau projet est créé. 
   
1. Sélectionnez **Projet** > **Gérer les packages NuGet**. 
   
1. Dans **Gestionnaire de package NuGet**, sélectionnez l’onglet **Parcourir**, puis recherchez et sélectionnez **System.Data.SqlClient**.
   
1. Sur la page **System.Data.SqlClient**, sélectionnez **Installer**. 
   - Si vous y êtes invité, sélectionnez **OK** pour poursuivre l’installation. 
   - Si une fenêtre **Acceptation de la licence** apparaît, sélectionnez **J’accepte**.
   
1. Une fois l’installation terminée, vous pouvez fermer le **Gestionnaire de package NuGet**. 
   
1. Dans l’éditeur de code, remplacez le contenu de **Program.cs** par le code suivant. Remplacez `<server>`, `<username>`, `<password>` et `<database>` par vos valeurs.
   
   >[!IMPORTANT]
   >Dans cet exemple, le code utilise les exemples de données AdventureWorksLT que vous pouvez choisir comme source lors de la création de votre base de données. Si votre base de données comporte des données différentes, utilisez des tables de votre propre base de données dans la requête SELECT. 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Exécuter le code

1. Pour exécuter l’application, sélectionnez **Déboguer** > **Démarrer le débogage**, ou sélectionnez **Démarrer** dans la barre d’outils, ou appuyez sur **F5**.
1. Vérifiez que les 20 premières lignes catégorie/produit de votre base de données sont retournées, puis fermez la fenêtre de l’application.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [connecter et interroger une base de données Azure SQL à l’aide de .NET Core](sql-database-connect-query-dotnet-core.md) sur Windows/Linux/macOS.  
- En savoir plus sur la [prise en main de .NET Core sur Windows/Linux/macOS à l’aide de la ligne de commande](/dotnet/core/tutorials/using-with-xplat-cli).
- Découvrez comment [concevoir votre première base de données Azure SQL à l’aide de SSMS](sql-database-design-first-database.md) ou [concevoir votre première base de données Azure SQL à l’aide de .NET](sql-database-design-first-database-csharp.md).
- Pour plus d’informations sur .NET, consultez la [documentation .NET](https://docs.microsoft.com/dotnet/).
- Exemple de logique de nouvelle tentative : [Connexion résiliente à SQL avec ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

