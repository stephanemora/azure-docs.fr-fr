---
title: Application console en utilisant l’API d’Azure Cosmos DB pour MongoDB et le SDK Golang
description: Cet article présente un exemple de code Golang que vous pouvez utiliser pour vous connecter à l’API Azure Cosmos DB pour MongoDB et pour l’interroger.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 94be0ec16aedc317f1be41998356bc52b66f7e86
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619210"
---
# <a name="quickstart-build-a-console-app-using-azure-cosmos-dbs-api-for-mongodb-and-golang-sdk"></a>Démarrage rapide : Générer une application console en utilisant l’API Azure Cosmos DB pour MongoDB et le kit de développement logiciel (SDK) Golang

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous avez la possibilité de créer et d’interroger rapidement des documents, des paires clé/valeur et des bases de données de graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur de Cosmos DB.

Ce démarrage rapide montre comment utiliser une application MongoDB existante écrite en [Golang](https://golang.org/) et comment la connecter à votre base de données Cosmos à l’aide de l’API Azure Cosmos DB pour MongoDB.

En d’autres termes, votre application Golang sait uniquement qu’elle se connecte à l’aide d’un client MongoDB. L’application sait que les données sont stockées dans une base de données Cosmos.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Go](https://golang.org/dl/) et une connaissance de base du langage [Go](https://golang.org/).
- Un IDE : [GoLand](https://www.jetbrains.com/go/) de Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) de Microsoft ou [Atom](https://atom.io/). Dans ce tutoriel, j’utilise GoLand.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Cloner l’exemple d’application et installer les packages de composants requis.

1. Créez un dossier nommé CosmosDBSample dans le dossier GOROOT\src, C:\Go\ par défaut.
2. Exécutez la commande suivante à l’aide d’une fenêtre de terminal git comme git bash afin de cloner le répertoire d’exemple dans le dossier CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Exécutez la commande suivante pour obtenir le package mgo. 

    ```
    go get gopkg.in/mgo.v2
    ```

Le pilote [mgo](https://labix.org/mgo) est un pilote de type [MongoDB](https://www.mongodb.com/) destiné au [langage Go](https://golang.org/) qui implémente un ensemble de fonctionnalités riche et éprouvé sous une API très simple suivant des idiomes Go standard.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Cliquez sur **Démarrage rapide** dans le menu de navigation de gauche, puis cliquez sur **Autres** pour afficher les informations sur les chaînes de connexion requises par l’application Go.

2. Dans Goglang, ouvrez le fichier main.go dans le répertoire GOROOT\CosmosDBSample et mettez à jour les lignes de code suivantes à l’aide des informations de chaîne de connexion à depuis le portail Azure comme indiqué dans la capture d’écran suivante. 

    Le nom de la base de données correspond au préfixe de la valeur **Hôte** valeur dans le volet de chaîne de connexion du+ portail Azure. Pour le compte indiqué dans l’image ci-dessous, le nom de la base de données est golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Volet de démarrage rapide, onglet Autre dans le portail Azure affichant les informations de chaîne de connexion](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Enregistrez le fichier main.go.

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez directement passer à la section [Exécution de l’application](#run-the-app). 

Tous les extraits de code suivants proviennent du fichier main.go.

### <a name="connecting-the-go-app-to-cosmos-db"></a>Connecter l’application Go à Cosmos DB

L’API d’Azure Cosmos DB pour MongoDB prend en charge la connexion TLS. Pour vous connecter, vous devez configurer la fonction **DialServer** dans [mgo. DialInfo](https://godoc.org/gopkg.in/mgo.v2#DialInfo) et vérifier que la fonction [tls.*Dial*](https://golang.org/pkg/crypto/tls#Dial) assure la connexion.

L’extrait de code Golang suivant connecte l’application Go à l’API Azure Cosmos DB pour MongoDB. La classe *DialInfo* contient des options permettant d’établir une session.

```go
// DialInfo holds options for establishing a session.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to Cosmos database (using Azure Cosmos DB's API for MongoDB).
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

La méthode **mgo.Dial()** est utilisée lorsqu’il n’existe aucune connexion TLS. Pour établir une connexion TLS, la méthode **mgo.DialWithInfo()** est nécessaire.

Une instance de l’objet **DialWIthInfo{}** est utilisée pour créer l’objet de session. Une fois la session établie, vous pouvez accéder à la collection à l’aide de l’extrait de code suivant :

```go
collection := session.DB("database").C("package")
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Créer un document

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Interroger ou lire un document

Cosmos DB prend en charge les requêtes enrichies sur des données stockées dans chaque collection. L’exemple de code suivant illustre une requête que vous pouvez exécuter sur les documents dans votre collection.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Mettre à jour un document

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Supprimer un document

Cosmos DB prend en charge la suppression de documents.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Exécuter l’application

1. Dans Golang, vérifiez que votre GOPATH (disponible sous **Fichier**, **Paramètres**, **Go**, **GOPATH**) inclue l’emplacement dans lequel le gopkg a été installé, USERPROFILE\go par défaut. 
2. Commentez les lignes supprimant le document (lignes 103 à 107) afin que vous puissiez voir le document après l’exécution de l’application.
3. Dans Golang, cliquez sur **Exécuter**, puis cliquez sur **Exécuter "Générer Main.go et exécuter"** .

    L’application se termine et affiche la description du document créé dans [Créer un document](#create-document).
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Golang affichant la sortie de l’application](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Réviser votre document dans l’Explorateur de données

Revenez au portail Azure pour afficher votre document dans l’Explorateur de données.

1. Cliquez sur **Explorateur de données (version préliminaire)** dans le menu de navigation de gauche, développez **golang-coach**, **package**, puis cliquez sur **Documents**. Dans l’onglet **Documents**, cliquez sur l’id \_ pour afficher le document dans le volet de droite. 

    ![Explorateur de données affichant le document créé](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Vous pouvez ensuite travailler avec le document en ligne et cliquer sur **Mettre à jour** pour l’enregistrer. Vous pouvez également supprimer le document ou créer de des documents ou des requêtes.

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Cosmos et à exécuter une application Golang. Vous pouvez maintenant importer des données supplémentaires dans votre base de données Cosmos. 

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](mongodb-migrate.md)
