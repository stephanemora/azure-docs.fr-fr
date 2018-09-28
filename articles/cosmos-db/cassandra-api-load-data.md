---
title: Charger des exemples de données dans une table de l’API Cassandra d’Azure Cosmos DB avec une application Java | Microsoft Docs
description: Cet article montre comment charger des exemples de données utilisateur dans une table du compte de l’API Cassandra d’Azure Cosmos DB avec une application Java.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d659004e94c51f173bc1e9ae42fd9c62ae45912f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972536"
---
# <a name="load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>Charger des exemples de données dans une table de l’API Cassandra d’Azure Cosmos DB

Ce tutoriel montre comment charger des exemples de données utilisateur dans une table du compte de l’API Cassandra d’Azure Cosmos DB avec une application Java. L’application Java utilise le [pilote Java Datastax](https://github.com/datastax/java-driver) et charge des données utilisateur, comme l’ID d’utilisateur, le nom d’utilisateur et la localité de l’utilisateur. 

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Charger des données dans une table Cassandra
> * Exécuter l’application

## <a name="prerequisites"></a>Prérequis

* Cet article appartient à un tutoriel en plusieurs parties. Avant de commencer avec ce document, veillez à [créer le compte, l’espace de clés et la table de l’API Cassandra](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Charger des données dans la table

Ouvrez le fichier « UserRepository.java » sous le dossier « src\main\java\com\azure\cosmosdb\cassandra » et ajoutez le code pour insérer les champs user_id, user_name et user_bcity dans la table :

```java
/**
* Insert a row into user table
*
* @param id   user_id
* @param name user_name
* @param city user_bcity
*/
public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
}

/**
* Create a PrepareStatement to insert a row to user table
*
* @return PreparedStatement
*/
public PreparedStatement prepareInsertStatement() {
    final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
    return session.prepare(insertStatement);
}
```
 
Ouvrez le fichier « UserProfile.java » sous le dossier « src\main\java\com\azure\cosmosdb\cassandra ». Cette classe contient la méthode principale qui appelle les méthodes createKeyspace et createTable que vous avez définies précédemment. Ajoutez maintenant le code suivant pour insérer des exemples de données dans la table de l’API Cassandra.

```java
//Insert rows into user table
PreparedStatement preparedStatement = repository.prepareInsertStatement();
    repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
    repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
    repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
    repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
    repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
```

## <a name="run-the-app"></a>Exécution de l'application

Ouvrez une invite de commandes ou la fenêtre de terminal, et accédez au chemin du dossier où vous avez créé le projet. Exécutez la commande « mvn clean install » pour générer le fichier cosmosdb-cassandra-examples.jar dans le dossier cible et exécuter l’application. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Vous pouvez maintenant ouvrir l’Explorateur de données dans le portail Azure pour vérifier que les informations utilisateur sont ajoutées à la table.
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment charger des exemples de données dans le compte de l’API Cassandra d’Azure Cosmos DB. Vous pouvez maintenant passer à l’article suivant :

> [!div class="nextstepaction"]
> [Interroger des données à partir du compte de l’API Cassandra](cassandra-api-query-data.md)
