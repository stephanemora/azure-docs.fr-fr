---
title: Utiliser Java pour interroger une base de données
description: Montre comment utiliser Java pour créer un programme qui se connecte à une base de données dans Azure SQL Database ou Azure SQL Managed Instance et pour l’interroger à l’aide d’instructions T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-java-july2019. seo-java-august2019, sqldbrb=2 
ms.openlocfilehash: 6be52d2d3472888607bbd6276b4794184bb11273
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267390"
---
# <a name="quickstart-use-java-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Démarrage rapide : Utiliser Java pour interroger une base de données dans Azure SQL Database ou Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dans ce guide de démarrage rapide, vous utilisez Java pour vous connecter à une base de données dans Azure SQL Database ou Azure SQL Managed Instance, et vous utilisez des instructions T-SQL pour interroger les données.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  || SQL Database | Instance managée SQL | SQL Server sur une machine virtuelle Azure |
  |:--- |:--- |:---|:---|
  | Créer| [Portail](single-database-create-quickstart.md) | [Portail](../managed-instance/instance-create-quickstart.md) | [Portail](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [INTERFACE DE LIGNE DE COMMANDE](scripts/create-and-configure-database-cli.md) | [INTERFACE DE LIGNE DE COMMANDE](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurer | [Règle de pare-feu IP au niveau du serveur](firewall-create-server-level-portal-quickstart.md)| [Connectivité à partir d’une machine virtuelle](../managed-instance/connect-vm-instance-configure.md)|
  |||[Connectivité locale](../managed-instance/point-to-site-p2s-configure.md) | [Se connecter à une instance SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Charger les données|Adventure Works chargé dans le cadre du guide de démarrage rapide|[Restaurer Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Restaurer Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurer ou importer Adventure Works à partir d’un fichier [BACPAC](database-import.md) disponible sur [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurer ou importer Adventure Works à partir d’un fichier [BACPAC](database-import.md) disponible sur [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- Logiciels associés à [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macos"></a>[macOS](#tab/macos)

  Installez Homebrew et Java, puis Maven en suivant les étapes **1.2** et **1.3** dans [Créer des applications Java à l’aide de SQL Server sur macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installez Java, le kit de développement Java, puis Maven en suivant les étapes **1.2**, **1.3** et **1.4** dans [Créer des applications Java à l’aide de SQL Server sur Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installez Java, puis Maven en suivant les étapes **1.2** et **1.3** dans [Créer des applications Java à l’aide de SQL Server sur Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Les scripts fournis dans cet article utilisent la base de données **Adventure Works**.

> [!NOTE]
> Vous pouvez éventuellement choisir d’utiliser une instance managée SQL Azure.
>
> Pour la création et la configuration, utilisez le [portail Azure](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) ou l’[interface CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), puis configurez une connectivité [locale](../managed-instance/point-to-site-p2s-configure.md) ou de [machine virtuelle](../managed-instance/connect-vm-instance-configure.md).
>
> Pour charger des données, consultez [Restaurer avec BACPAC](database-import.md) avec le fichier [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works), ou consultez [Restaurer la base de données Wide World Importers](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-server-connection-information"></a>Obtenir les informations de connexion du serveur

Obtenez les informations de connexion dont vous avez besoin pour vous connecter à la base de données dans Azure SQL Database. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Bases de données SQL** ou ouvrez la page **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom de serveur complet à côté de **Nom de serveur** pour une base de données dans Azure SQL Database ou le nom de serveur complet (ou l’adresse IP) à côté de **Hôte** pour une instance managée SQL Azure ou SQL Server sur une machine virtuelle Azure. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**.

> [!NOTE]
> Pour obtenir les informations de connexion de SQL Server sur une machine virtuelle Azure, consultez [Se connecter à SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

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

## <a name="add-code-to-query-the-database"></a>Ajouter du code pour interroger la base de données

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
   > Cet exemple de code utilise l’exemple de base de données **AdventureWorksLT** dans Azure SQL Database.

## <a name="run-the-code"></a>Exécuter le code

1. À l’invite de commande, exécutez l’application.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Vérifiez que les 20 premières lignes ont été retournées et fermez la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données dans Azure SQL Database](design-first-database-tutorial.md)  
- [Pilote JDBC Microsoft pour SQL Server](https://github.com/microsoft/mssql-jdbc)  
- [Report issues/ask questions](https://github.com/microsoft/mssql-jdbc/issues) (Signaler des problèmes/poser des questions)  
