---
title: Utilisation de .NET Core pour interroger Azure SQL Database | Microsoft Docs
description: Cette rubrique vous explique comment utiliser .NET Core pour créer un programme qui se connecte à une base de données SQL Azure et l’interroge à l’aide d’instructions Transact-SQL.
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
ms.date: 12/10/2018
ms.openlocfilehash: 4d2acf64f7798f845c2993b108b7074283c40be6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160013"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de .NET Core (C#) pour interroger une base de données SQL Azure

Dans ce démarrage rapide, vous allez utiliser [.NET Core](https://www.microsoft.com/net/) et du code C# pour vous connecter à une base de données SQL Azure. Vous allez ensuite exécuter une instruction Transact-SQL pour interroger des données.

## <a name="prerequisites"></a>Prérequis

Pour ce didacticiel, vous avez besoin des éléments suivants :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Une version installée de [.NET Core pour votre système d’exploitation](https://www.microsoft.com/net/core). 

> [!NOTE]
> Ce guide de démarrage rapide utilise la base de données *mySampleDatabase*. Si vous souhaitez utiliser une autre base de données, vous devrez modifier les références de base de données et modifier la requête `SELECT` dans le code C#.


## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="get-adonet-connection-information-optional"></a>Obtenir des informations de connexion ADO.NET (facultatif)

1. Accédez à la page **mySampleDatabase**, puis, sous **Paramètres**, sélectionnez **Chaînes de connexion**.

2. Passez en revue la chaîne de connexion **ADO.NET** complète.

    ![Chaîne de connexion ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Copiez la chaîne de connexion **ADO.NET** si vous voulez l’utiliser.
  
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

## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL

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

2. Vérifiez que les 20 premières lignes sont retournées.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. Appuyez sur **Entrée** pour fermer la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main de .NET Core sur Windows/Linux/macOS à l’aide de la ligne de commande](/dotnet/core/tutorials/using-with-xplat-cli).
- Découvrez comment [connecter et interroger une base de données SQL Azure à l’aide de .NET Framework et Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Découvrez comment [concevoir votre première base de données SQL Azure à l’aide de SSMS](sql-database-design-first-database.md) ou [concevoir une base de données SQL Azure et se connecter avec C# et ADO.NET](sql-database-design-first-database-csharp.md).
- Pour plus d’informations sur .NET, consultez la [documentation .NET](https://docs.microsoft.com/dotnet/).
