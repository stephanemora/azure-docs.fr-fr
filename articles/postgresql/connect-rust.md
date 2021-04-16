---
title: 'Démarrage rapide : Se connecter avec Rust - Azure Database pour PostgreSQL - Serveur unique'
description: Ce guide de démarrage rapide fournit des exemples de code Rust que vous pouvez utiliser pour vous connecter et interroger des données à partir d'Azure Database pour PostgreSQL - Serveur unique.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505551"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Démarrage rapide : Utiliser Rust afin de se connecter à Azure Database pour PostgreSQL et d'interroger les données - Serveur unique

Dans cet article, vous allez apprendre à utiliser le [pilote PostgreSQL pour Rust](https://github.com/sfackler/rust-postgres) afin d'interagir avec Azure Database pour PostgreSQL en explorant les opérations CRUD (créer, lire, mettre à jour, supprimer) implémentées dans l'exemple de code. Pour finir, vous pourrez exécuter l’application localement pour la voir en action.

## <a name="prerequisites"></a>Prérequis
Voici les prérequis pour ce guide de démarrage rapide :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
- Version récente de [Rust](https://www.rust-lang.org/tools/install) installée.
- Instance d'Azure Database pour PostgreSQL - Serveur unique à créer à l'aide du [portail Azure](./quickstart-create-server-database-portal.md) <br/> ou d'[Azure CLI](./quickstart-create-server-database-azure-cli.md).
- Selon que vous utilisez un accès public ou privé, effectuez **UNE** des actions ci-dessous pour activer la connectivité.

  |Action| Méthode de connexion|Guide pratique|
  |:--------- |:--------- |:--------- |
  | **Configurer les règles de pare-feu** | Public | [Portail](./howto-manage-firewall-using-portal.md) <br/> [INTERFACE DE LIGNE DE COMMANDE](./howto-manage-firewall-using-cli.md)|
  | **Configurer le point de terminaison de service** | Public | [Portail](./howto-manage-vnet-using-portal.md) <br/> [INTERFACE DE LIGNE DE COMMANDE](./howto-manage-vnet-using-cli.md)|
  | **Configurer une liaison privée** | Privées | [Portail](./howto-configure-privatelink-portal.md) <br/> [INTERFACE DE LIGNE DE COMMANDE](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) installé.

## <a name="get-database-connection-information"></a>Obtenir des informations de connexion à la base de données
La connexion d’une base de données Azure Database pour PostgreSQL nécessite un nom de serveur complet et des informations d’identification de connexion. Vous pouvez vous procurer ces informations à partir du portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), cherchez et sélectionnez le nom de votre serveur Azure Database pour PostgreSQL.
1. Sur la page **Vue d’ensemble** du serveur, copiez le **nom du serveur** complet et le **nom d’utilisateur administrateur**. Le **nom de serveur complet** a toujours la forme *\<my-server-name>.postgres.database.azure.com*, et le **nom d’utilisateur administrateur** a toujours la forme *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>Examiner le code (facultatif)

Pour savoir comment le code fonctionne, vous pouvez examiner les extraits suivants. Sinon, vous pouvez passer à la section [Exécuter l’application](#run-the-application).

### <a name="connect"></a>Se connecter

La fonction `main` commence par se connecter à Azure Database pour PostgreSQL et elle utilise les variables d'environnement suivantes pour les informations de connectivité : `POSTGRES_HOST`, `POSTGRES_USER`, `POSTGRES_PASSWORD` et `POSTGRES_DBNAME`. Par défaut, le service de base de données PostgreSQL est configuré de façon à exiger une connexion `TLS`. Vous pouvez désactiver l'exigence d'utilisation du protocole `TLS` si votre application cliente ne prend pas en charge la connectivité `TLS`. Pour plus d'informations, consultez [Configurer la connectivité TLS dans Azure Database pour PostgreSQL - Serveur unique](./concepts-ssl-connection-security.md).

L'exemple d'application proposé dans cet article utilise TLS avec [postgres-openssl crate](https://crates.io/crates/postgres-openssl/). La fonction [postgres::Client::connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) permet d'initier la connexion, et le programme se ferme en cas d'échec.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Supprimer et créer une table

L'exemple d'application utilise une table `inventory` simple pour illustrer les opérations CRUD (créer, lire, mettre à jour, supprimer).

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

La fonction `drop_create_table` tente d'abord de supprimer (`DROP`) la table `inventory` avant d'en créer une nouvelle. Cela facilite l'apprentissage/l'expérimentation, car vous commencez toujours avec un état connu (propre). La méthode [execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) est utilisée pour les opérations de création et de suppression. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Insertion des données

`insert_data` ajoute des entrées à la table `inventory`. Elle crée une [instruction préparée](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) avec la fonction [prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare). 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Notez également l'utilisation de la méthode [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed), qui permet de spécifier explicitement les types de paramètres d'interrogation.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Enfin, une boucle `for` est utilisée pour ajouter `item-3`, `item-4` et `item-5`, avec une quantité générée de façon aléatoire pour chacun.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Interroger des données

La fonction `query_data` montre comment récupérer des données à partir de la table `inventory`. La méthode [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) est utilisée pour récupérer un élément à partir de son `id`. 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Toutes les lignes de la table « inventory » sont extraites en utilisant une requête `select * from` avec la méthode [query](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query). Les lignes renvoyées sont itérées pour extraire la valeur de chaque colonne à l'aide de [get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get). 

> [!TIP]
> Notez que `get` permet de spécifier la colonne soit par son index numérique sur la ligne, soit par son nom.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Mettre à jour des données

La fonction `update_date` met à jour de façon aléatoire la quantité de l'ensemble des éléments. Étant donné que la fonction `insert_data` a ajouté `5` lignes, le même paramètre est pris en compte dans la boucle `for` - `for n in 1..=5`.

> [!TIP]
> Notez que nous utilisons `query` plutôt qu'`execute`, car nous prévoyons de récupérer l'`id` et la quantité (`quantity`) nouvellement générée (à l'aide de la [clause RETURNING](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Suppression de données

Enfin, la fonction `delete` montre comment supprimer un élément de la table `inventory` à partir de son `id`. L'`id` est choisi de façon aléatoire. Il s'agit d'un entier aléatoire compris entre `1` et `5` (`5` inclus), car la fonction `insert_data` a initialement ajouté `5` lignes. 

> [!TIP]
> Notez que nous utilisons `query` plutôt qu'`execute`, car nous prévoyons de récupérer les informations relatives à l'élément que nous venons de supprimer (à l'aide de la [clause RETURNING](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Exécution de l'application

1. Commencez par exécuter la commande suivante pour cloner l'exemple de référentiel :

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Définissez les variables d'environnement requises avec les valeurs que vous avez copiées à partir du portail Azure :

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Pour exécuter l'application, accédez au répertoire où vous l'avez clonée et exécutez `cargo run` :

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    Vous devez voir une sortie similaire à ce qui suit :

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. Pour confirmer, vous pouvez également vous connecter à Azure Database pour PostgreSQL [à l'aide de psql](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) et exécuter des requêtes dans la base de données, par exemple :

    ```sql
    select * from inventory;
    ```

[Vous rencontrez des problèmes ? Faites-le nous savoir](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour nettoyer toutes les ressources utilisées dans le cadre de ce guide de démarrage rapide, supprimez le groupe de ressources à l’aide de la commande suivante :

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Gérer un serveur Azure Database pour PostgreSQL à l'aide du portail](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gérer un serveur Azure Database pour PostgreSQL à l'aide de l'interface CLI](./how-to-manage-server-cli.md)<br/>

[Vous ne trouvez pas ce que vous cherchez ? Faites-le nous savoir.](https://aka.ms/postgres-doc-feedback)
