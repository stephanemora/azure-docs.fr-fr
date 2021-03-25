---
title: Utiliser .NET Core pour se connecter à une base de données et l’interroger
description: Cette rubrique explique comment utiliser .NET Core pour créer un programme qui se connecte à une base de données dans Azure SQL Database ou Azure SQL Managed Instance et l’interroge à l’aide d’instructions Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705210"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Démarrage rapide : Utiliser .NET Core (C#) pour interroger une base de données
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Dans ce guide de démarrage rapide, vous allez utiliser [.NET Core](https://www.microsoft.com/net/) et du code C# pour vous connecter à une base de données. Vous allez ensuite exécuter une instruction Transact-SQL pour interroger des données.

> [!TIP]
> Le module Microsoft Learn suivant vous aide à apprendre gratuitement comment [Développer et configurer une application ASP.Net qui interroge une base de données dans Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/).

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Une version installée de [.NET Core pour votre système d’exploitation](https://www.microsoft.com/net/core).
- Base de données dans laquelle vous pouvez exécuter votre requête. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Créer un nouveau projet .NET Core

1. Ouvrez une invite de commandes et créez un dossier nommé **sqltest**. Accédez à ce dossier et exécutez cette commande.

    ```cmd
    dotnet new console
    ```

    Cette commande crée de nouveaux fichiers projet d’application, y compris un fichier de code C# initial (**Program.cs**), un fichier de configuration XML (**sqltest.csproj**) et les fichiers binaires nécessaires.

2. Dans un éditeur de texte, ouvrez **sqltest.csproj** et collez le code XML suivant entre les balises `<Project>`. Ce code XML ajoute `System.Data.SqlClient` en tant que dépendance.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Ajouter du code pour interroger la base de données dans Azure SQL Database

1. Ouvrez le fichier **Program.cs** dans un éditeur de texte.

2. Remplacez le contenu par le code suivant et ajoutez les valeurs appropriées pour votre serveur, base de données, nom d’utilisateur et mot de passe.

> [!NOTE]
> Pour utiliser une chaîne de connexion ADO.NET, remplacez les 4 lignes dans le code définissant le serveur, la base de données, le nom d’utilisateur et le mot de passe par la ligne ci-dessous. Dans la chaîne, définissez votre nom d’utilisateur et votre mot de passe.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Exécuter le code

1. À l’invite, exécutez les commandes suivantes.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Vérifiez que les lignes sont retournées.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Appuyez sur **Entrée** pour fermer la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main de .NET Core sur Windows/Linux/macOS à l’aide de la ligne de commande](/dotnet/core/tutorials/using-with-xplat-cli).
- Découvrez comment [vous connecter à Azure SQL Database ou Azure SQL Managed Instance et exécuter des requêtes à l’aide de .NET Framework et Visual Studio](connect-query-dotnet-visual-studio.md).  
- Découvrez comment [concevoir votre première base de données à l’aide de SSMS](design-first-database-tutorial.md) ou comment [concevoir une base de données et vous y connecter avec C# et ADO.NET](design-first-database-csharp-tutorial.md).
- Pour plus d’informations sur .NET, consultez la [documentation .NET](/dotnet/).
