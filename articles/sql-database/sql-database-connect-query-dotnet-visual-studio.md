---
title: Utilisation de Visual Studio avec .NET et C# pour interroger Azure SQL Database | Microsoft Docs
description: Utilisez Visual Studio pour créer une application en C# qui se connecte à une base de données SQL Azure et l’interroge avec des instructions Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/11/2018
ms.openlocfilehash: 93249b7d274ce9d7928dfa46eb339da68c92b785
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163294"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de NET et C# dans Visual Studio pour se connecter à une base de données SQL Azure et l’interroger

Ce guide de démarrage rapide montre comment utiliser [l’infrastructure .NET](https://www.microsoft.com/net/) et le code C# dans Visual Studio pour interroger une base de données SQL Azure avec des instructions Transact-SQL.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) édition Communauté, Professionnel ou Entreprise.

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>Créer du code pour interroger la base de données SQL

1. Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**. 
   
1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#**, puis **Application console (.NET Framework)**.
   
1. Entrez *sqltest* pour le nom du projet puis sélectionnez **OK**. Le nouveau projet est créé. 
   
1. Sélectionnez **Projet** > **Gérer les packages NuGet**. 
   
1. Dans **Gestionnaire de package NuGet**, sélectionnez l’onglet **Parcourir**, puis recherchez et sélectionnez **System.Data.SqlClient**.
   
1. Sur la page **System.Data.SqlClient**, sélectionnez **Installer**. 
   - Si vous y êtes invité, sélectionnez **OK** pour poursuivre l’installation. 
   - Si une fenêtre **Acceptation de la licence** apparaît, sélectionnez **J’accepte**.
   
1. Une fois l’installation terminée, vous pouvez fermer le **Gestionnaire de package NuGet**. 
   
1. Dans l’éditeur de code, remplacez le contenu de **Program.cs** par le code suivant. Remplacez `<server>`, `<username>`, `<password>`, et `<database>` par vos valeurs.
   
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
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
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

- Découvrez comment [connecter et interroger une base de données SQL Azure à l’aide de .NET Core](sql-database-connect-query-dotnet-core.md) sur Windows/Linux/macOS.  
- En savoir plus sur la [prise en main de .NET Core sur Windows/Linux/macOS à l’aide de la ligne de commande](/dotnet/core/tutorials/using-with-xplat-cli).
- Découvrez comment [concevoir votre première base de données SQL Azure à l’aide de SSMS](sql-database-design-first-database.md) ou [concevoir votre première base de données SQL Azure à l’aide de .NET](sql-database-design-first-database-csharp.md).
- Pour plus d’informations sur .NET, consultez la [documentation .NET](https://docs.microsoft.com/dotnet/).
- Exemple de logique de nouvelle tentative : [Connexion résiliente à SQL avec ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

