---
title: Interroger les données d’un compte de l’API Cassandra Azure Cosmos DB
description: Cet article montre comment interroger les données utilisateur d’un compte de l’API Cassandra Azure Cosmos DB à l’aide d’une application Java.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: a06e7e6159953bfeffa966759d29b91bbcbafd37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739207"
---
# <a name="tutorial-query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Tutoriel : Interroger les données d’un compte d’API Cassandra Azure Cosmos DB

Ce didacticiel montre comment interroger les données utilisateur provenant d’un compte de l’API Cassandra Azure Cosmos DB à l’aide d’une application Java. L’application Java utilise le [pilote Java](https://github.com/datastax/java-driver) et interroge des données utilisateur, comme l’ID de l’utilisateur, son nom et sa localité. 

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Interroger les données d’une table Cassandra
> * Exécution de l'application

## <a name="prerequisites"></a>Prérequis

* Cet article appartient à un tutoriel en plusieurs parties. Avant de commencer, veillez à compléter les étapes précédentes afin de créer un compte, un espace de clés et une table d’API Cassandra et de [charger des exemples de données dans la table](cassandra-api-load-data.md). 

## <a name="query-data"></a>Données de requête

Utilisez les étapes suivantes pour interroger des données de votre table d’API Cassandra :

1. Ouvrez le fichier `UserRepository.java` qui se trouve dans le dossier `src\main\java\com\azure\cosmosdb\cassandra`. Ajoutez-y le bloc de code suivant. Ce code fournit trois méthodes : 

   * Pour interroger tous les utilisateurs de la base de données
   * Pour interroger un utilisateur spécifique, filtré par ID d’utilisateur
   * Pour supprimer une table.

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Ouvrez le fichier `UserProfile.java` qui se trouve dans le dossier `src\main\java\com\azure\cosmosdb\cassandra`. Cette classe contient la méthode principale qui appelle les méthodes d’insertion des données createKeyspace et createTable que vous avez définies précédemment. Ajoutez maintenant le code suivant pour interroger tous les utilisateurs ou un utilisateur spécifique :

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Exécuter l’application Java
1. Ouvrez une invite de commandes ou une fenêtre de terminal. Collez le bloc de code suivant. 

   Ce code change de répertoire (cd) pour le chemin du dossier où vous avez créé le projet. Ensuite, il exécute la commande `mvn clean install` pour générer le fichier `cosmosdb-cassandra-examples.jar` dans le dossier cible. Enfin, il exécute l’application Java.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Maintenant, dans le Portail Azure, ouvrez l’**Explorateur de données** et confirmez la suppression de la table utilisateur.

## <a name="next-steps"></a>Étapes suivantes

* Dans ce tutoriel, vous avez appris à interroger les données d’un compte de l’API Cassandra Azure Cosmos DB. Vous pouvez maintenant passer à l’article suivant :

> [!div class="nextstepaction"]
> [Migrer des données vers un compte d’API Cassandra](cassandra-import-data.md)


