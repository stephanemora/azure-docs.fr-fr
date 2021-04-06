---
title: Utiliser Visual Studio avec .NET et C# pour interroger
description: Utilisez Visual Studio pour créer une application C# qui se connecte à une base de données dans Azure SQL Database ou Azure SQL Managed instance et qui exécute des requêtes.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705198"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Démarrage rapide : Utiliser .NET et C# dans Visual Studio pour se connecter à une base de données et l’interroger
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ce guide de démarrage rapide montre comment utiliser le [.NET Framework](https://www.microsoft.com/net/) et le code C# dans Visual Studio pour interroger une base de données dans Azure SQL ou Synapse SQL avec des instructions Transact-SQL.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) édition Community, Professional ou Enterprise.
- Base de données dans laquelle vous pouvez exécuter une requête.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Créer du code pour interroger la base de données dans Azure SQL Database

1. Dans Visual Studio, créez un projet. 
   
1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#** , **Application console (.NET Framework)** .
   
1. Entrez *sqltest* pour le nom du projet puis sélectionnez **OK**. Le nouveau projet est créé. 
   
1. Sélectionnez **Projet** > **Gérer les packages NuGet**. 
   
1. Dans **Gestionnaire de package NuGet**, sélectionnez l'onglet **Parcourir**, puis recherchez et sélectionnez **Microsoft.Data.SqlClient**.
   
1. Sur la page **Microsoft.Data.SqlClient**, sélectionnez **Installer**. 
   - Si vous y êtes invité, sélectionnez **OK** pour poursuivre l’installation. 
   - Si une fenêtre **Acceptation de la licence** apparaît, sélectionnez **J’accepte**.
   
1. Une fois l’installation terminée, vous pouvez fermer le **Gestionnaire de package NuGet**. 
   
1. Dans l’éditeur de code, remplacez le contenu de **Program.cs** par le code suivant. Remplacez `<your_server>`, `<your_username>`, `<your_password>` et `<your_database>` par vos valeurs.
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
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
1. Vérifiez que les noms et les classements des bases de données sont retournés, puis fermez la fenêtre de l’application.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [vous connecter à une base de données dans Azure SQL Database et l’interroger en utilisant .NET Core](connect-query-dotnet-core.md) sur Windows/Linux/macOS.  
- En savoir plus sur la [prise en main de .NET Core sur Windows/Linux/macOS à l’aide de la ligne de commande](/dotnet/core/tutorials/using-with-xplat-cli).
- Découvrez comment [concevoir votre première base de données dans Azure SQL Database en utilisant SSMS](design-first-database-tutorial.md) ou comment [concevoir votre première base de données dans Azure SQL Database en utilisant .NET](design-first-database-csharp-tutorial.md).
- Pour plus d’informations sur .NET, consultez la [documentation .NET](/dotnet/).
- Exemple de logique de nouvelle tentative : [Connexion résiliente à Azure SQL avec ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
