---
title: Connecter une application Rust à l’API Azure Cosmos DB pour MongoDB
description: Ce guide de démarrage rapide montre comment créer une application Rust avec l’API Azure Cosmos DB pour MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: a9a6c2b0a106b08d8aad880b0b5f18879ac5645b
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206256"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Démarrage rapide : Connecter une application Rust à l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB est un service de base de données multimodèle qui vous permet de créer et d’interroger rapidement des bases de données de documents, de tables, de paires clé/valeur et de graphes avec des capacités de distribution mondiale et de mise à l’échelle horizontale. L’exemple présenté dans cet article est une application simple basée sur la ligne de commande qui utilise le [pilote Rust pour MongoDB](https://github.com/mongodb/mongo-rust-driver). L’API Azure Cosmos DB pour MongoDB étant [compatible avec le protocole filaire MongoDB](./mongodb-introduction.md), n’importe quel pilote client MongoDB peut s’y connecter.

Vous allez apprendre à utiliser le pilote Rust MongoDB pour interagir avec l’API Azure Cosmos DB pour MongoDB en explorant les opérations CRUD (créer, lire, mettre à jour, supprimer) implémentées dans l’exemple de code. Pour finir, vous pourrez exécuter l’application localement pour la voir en action.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free). Vous pouvez également [essayez Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure. Vous pouvez aussi utiliser l’[émulateur Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) avec la chaîne de connexion `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- [Rust](https://www.rust-lang.org/tools/install) (version 1.39 ou ultérieure)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Configurer Azure Cosmos DB

Pour configurer un compte Azure Cosmos DB, suivez [ces instructions](create-mongodb-dotnet.md). L’application aura besoin de la chaîne de connexion MongoDB, que vous pouvez extraire à l’aide du portail Azure. Pour plus d’informations, consultez [Obtenir la chaîne de connexion MongoDB à personnaliser](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Exécution de l'application

### <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Exécutez les commandes suivantes pour cloner l’exemple de référentiel.

1. Ouvrez une invite de commandes, créez un nouveau dossier nommé `git-samples`, puis fermez l’invite de commandes.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application.

    ```bash
    cd "C:\git-samples"
    ```

1. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Créer l’application

Pour générer le binaire :

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Configurer l’application 

Exportez la chaîne de connexion, la base de données MongoDB et les noms de collections en tant que variables d’environnement. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> L’option `ssl=true` est importante, en raison des exigences de Cosmos DB. Pour plus d’informations, consultez [Exigences relatives à la chaîne de connexion](connect-mongodb-account.md#connection-string-requirements).
>

Pour la variable d’environnement `MONGODB_URL`, remplacez les espaces réservés pour `<COSMOSDB_ACCOUNT_NAME>` et `<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: nom du compte Azure Cosmos DB que vous avez créé
- `<COSMOSDB_PASSWORD>`: clé de base de données extraite à l’étape précédente

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Vous pouvez choisir vos valeurs préférées pour `MONGODB_DATABASE` et `MONGODB_COLLECTION` ou les conserver en l’état.

Pour exécuter l’application, accédez au dossier approprié (où se trouve le binaire d’application) :

```bash
cd target/release
```

Pour créer un élément `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

En cas de réussite, vous devriez voir une sortie avec l’élément MongoDB `_id` du document nouvellement créé :

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Créer un autre élément `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Répertorier tous les éléments `todo`

```bash
./todo list all
```

Vous devriez voir ceux que vous venez d’ajouter :

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Pour mettre à jour l’état d’un `todo` (par exemple le définir à l’état `completed`), utilisez l’ID `todo` comme suit :

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Répertorier uniquement les éléments `todo`terminés

```bash
./todo list completed
```

Vous devriez voir celui que vous venez de mettre à jour

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Supprimer un élément `todo` à l’aide de son ID

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Répertorier les éléments `todo` à confirmer

```bash
./todo list all
```

L’élément `todo` que vous venez de supprimer ne devrait pas apparaître.

### <a name="view-data-in-data-explorer"></a>Afficher les données dans l’Explorateur de données

Les données stockées dans Azure Cosmos DB peuvent être affichées et interrogées dans le portail Azure.

Pour afficher, interroger et manipuler les données utilisateur créées à l’étape précédente, connectez-vous au [portail Azure](https://portal.azure.com) dans votre navigateur web.

Dans la zone de recherche en haut de la page, entrez **Azure Cosmos DB**. Lorsque le panneau de votre compte Cosmos s’ouvre, sélectionnez votre compte Cosmos. Dans le volet de navigation gauche, sélectionnez **Explorateur de données**. Développez votre collection dans le volet Collections, pour pouvoir afficher les documents de la collection, interroger les données et même créer et exécuter des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur.

## <a name="review-the-code-optional"></a>Examiner le code (facultatif)

Pour savoir comment l’application fonctionne, vous pouvez examiner les extraits de code fournis dans cette section. Les extraits suivants proviennent du fichier `src/main.rs`.

La fonction `main` est le point d’entrée de l’application `todo`. Elle s’attend à ce que l’URL de connexion pour l’API Azure Cosmos DB pour MongoDB soit fournie par la variable d’environnement `MONGODB_URL`. Une nouvelle instance de `TodoManager` est créée, suivie d’une [`match` expression](https://doc.rust-lang.org/book/ch06-02-match.html) qui délègue à la méthode `TodoManager` appropriée en fonction de l’opération choisie par l’utilisateur : `create`, `update`, `list` ou `delete`.

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` est un `struct` qui encapsule un [mongodb::sync::Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html). Quand vous essayez d’instancier un `TodoManager` à l’aide de la fonction `new`, cela établit une connexion à l’API Azure Cosmos DB pour MongoDB.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

Plus important encore, `TodoManager` dispose de méthodes pour aider à gérer les `todo`. Décrivons-les un par un.

La méthode `add_todo` prend une description `todo` fournie par l’utilisateur, et crée une instance de struct `Todo` qui ressemble à ce qui suit. Le framework [serde](https://github.com/serde-rs/serde) est utilisé pour mapper (sérialiser/désérialiser) des données BSON en instances de structs `Todo`. Notez comment les attributs de champs `serde` sont utilisés pour personnaliser le processus de sérialisation/désérialisation. Par exemple, le champ `todo_id` dans le `struct` Todo est un `ObjectId` et il est stocké dans MongoDB en tant que `_id`.

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) accepte un [Document](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) représentant les détails de `todo` à ajouter. Notez que la conversion de `Todo` en `Document` est un processus en deux étapes, réalisé à l’aide d’une combinaison de [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) et [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document).

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Collection.find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) est utilisée pour récupérer *tous* les `todo`ou pour les filtrer en fonction de l’état fourni par l’utilisateur (`pending` ou `completed`). Notez comment, dans la boucle `while`, chaque `Document` obtenu suite à la recherche est converti en struct `Todo` à l’aide de [bson::from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html). C’est l’inverse de ce qui a été fait dans la méthode `add_todo`.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

L’état d’un `todo` peut être mis à jour (de `pending` à `completed` ou vice versa). Le `todo` est converti en [bson::oid::ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html), qui est ensuite utilisé par la méthode [Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) pour localiser le document qui doit être mis à jour.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

La suppression d’un `todo` est simple à l’aide de la méthode [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one).


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte d’API Azure Cosmos DB MongoDB à l’aide d’Azure Cloud Shell, et à créer puis exécuter une application de ligne de commande Rust pour gérer des éléments `todo`. Vous pouvez maintenant importer des données supplémentaires dans votre compte Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
