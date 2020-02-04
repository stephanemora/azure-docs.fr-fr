---
title: Utiliser Java pour interroger une base de données
description: Explique comment utiliser Java pour créer un programme qui se connecte à une base de données Azure SQL et pour l’interroger à l’aide d’instructions T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768642"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de Java pour interroger une base de données Azure SQL

Dans ce guide de démarrage rapide, vous utilisez Java pour vous connecter à une base de données Azure SQL et vous utilisez des instructions T-SQL pour interroger les données.

## <a name="prerequisites"></a>Conditions préalables requises

- Un compte Azure avec un abonnement actif. [Créez gratuitement un compte](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Une [base de données Azure SQL](sql-database-single-database-get-started.md)
- Logiciels associés à [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Installez Homebrew et Java, puis Maven en suivant les étapes **1.2** et **1.3** dans [Créer des applications Java à l’aide de SQL Server sur macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installez Java, le kit de développement Java, puis Maven en suivant les étapes **1.2**, **1.3** et **1.4** dans [Créer des applications Java à l’aide de SQL Server sur Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Installez Java, puis Maven en suivant les étapes **1.2** et **1.3** dans [Créer des applications Java à l’aide de SQL Server sur Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Les scripts fournis dans cet article utilisent la base de données **Adventure Works**.

> [!NOTE]
> Vous pouvez éventuellement choisir d’utiliser une instance gérée d’Azure SQL.
>
> Pour créer et configurer, utilisez le [portail Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) ou [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), puis configurez [sur site](sql-database-managed-instance-configure-p2s.md) ou par le biais d’une connexion aux [machines virtuelles](sql-database-managed-instance-configure-vm.md).
>
> Pour charger des données, consultez [Restaurer avec BACPAC](sql-database-import.md) avec le fichier [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works), ou consultez [Restaurer la base de données Wide World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Bases de données SQL** ou ouvrez la page **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom complet du serveur à côté de **Nom du serveur** pour une base de données, ou le nom complet du serveur à côté de **Hôte** pour une instance managée. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**. 

## <a name="create-the-project"></a>Créer le projet

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

- [Concevoir votre première base de données Azure SQL](sql-database-design-first-database.md)  

- [Pilote JDBC Microsoft pour SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Report issues/ask questions](https://github.com/microsoft/mssql-jdbc/issues) (Signaler des problèmes/poser des questions)  
