---
title: Utilisation de Java pour interroger Azure SQL Database | Microsoft Docs
description: Explique comment utiliser Java pour créer un programme qui se connecte à une base de données SQL Azure et pour l’interroger à l’aide d’instructions T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/20/2018
ms.openlocfilehash: 5c12bd54c0ea96ac915fedab94f03cf044330dcf
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723293"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Démarrage rapide : Utiliser Java pour interroger une base de données Azure SQL

Cet article explique comment utiliser [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) pour se connecter à une base de données SQL Azure. Vous pouvez ensuite utiliser les instructions T-SQL pour interroger des données.

## <a name="prerequisites"></a>Prérequis

Pour suivre cet exemple, vérifiez que les conditions préalables ci-dessous sont bien remplies :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal-firewall.md) pour l’adresse IP publique de l’ordinateur que vous utilisez

- Le logiciel associé à Java installé pour votre système d’exploitation :

  - **MacOS**, installez Homebrew et Java, puis installez Maven. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu**, installez Java, le kit de développement Java, puis installez Maven. Consultez les [étapes 1.2, 1.3 et 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows**, installez Java puis Maven. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-database-connection"></a>Obtenir la connexion de base de données

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-the-project"></a>Création du projet

1. À partir de l’invite de commandes, créez un projet Maven nommé *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Basculez vers le dossier *sqltest* et ouvrez *pom.xml* avec votre éditeur de texte favori. Ajoutez le **pilote Microsoft JDBC pour SQL Server** aux dépendances de votre projet en utilisant le code ci-dessous.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. Toujours dans *pom.xml*, ajoutez les propriétés suivantes à votre projet. S’il n’y a pas de section Propriétés, vous pouvez les ajouter après les dépendances.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Enregistrez et fermez le fichier *pom.xml*.

## <a name="add-code-to-query-database"></a>Ajouter du code pour interroger la base de données

1. Vous devez déjà disposer d’un fichier appelé *App.java* dans votre projet Maven, situé à l’emplacement :

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Ouvrez le fichier et remplacez son contenu par le code suivant. Ensuite, ajoutez les valeurs appropriées pour vos serveur, base de données, utilisateur et mot de passe.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > Cet exemple de code utilise l’exemple de base de données **AdventureWorksLT** pour SQL Azure.

## <a name="run-the-code"></a>Exécuter le code

1. À l’invite de commande, exécutez l’application.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Vérifiez que les 20 premières lignes ont été retournées et fermez la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)  

- [Pilote JDBC Microsoft pour SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Report issues/ask questions](https://github.com/microsoft/mssql-jdbc/issues) (Signaler des problèmes/poser des questions)  
