---
title: Utiliser Azure Cache pour Redis avec Rust
description: Dans ce guide de démarrage rapide, vous allez apprendre à interagir avec Azure Cache pour Redis en utilisant Rust.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: b55a706aa25b21620226690c172c996fe10a84fa
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203490"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Démarrage rapide : Utiliser Azure Cache pour Redis avec Rust

Dans cet article, vous allez apprendre à utiliser le [langage de programmation Rust](https://www.rust-lang.org/) pour l’interaction avec [Azure Cache pour Redis](./cache-overview.md). Il montre des exemples de structures de données Redis couramment utilisées (telles que [String](https://redis.io/topics/data-types-intro#redis-strings), [Hash](https://redis.io/topics/data-types-intro#redis-hashes), [List](https://redis.io/topics/data-types-intro#redis-lists), etc.) qui utilisent la bibliothèque [redis-rs](https://github.com/mitsuhiko/redis-rs) pour Redis. Ce client expose à la fois les API de haut et de bas niveau, et vous verrez ces deux styles en action avec l’aide de l’exemple de code présenté dans cet article.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install) (version 1.39 ou ultérieure)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Créer une instance Cache Redis Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Examiner le code (facultatif)

Pour savoir comment le code fonctionne, vous pouvez examiner les extraits suivants. Sinon, vous pouvez passer à la section [Exécuter l’application](#run-the-application).

La fonction `connect` est utilisée pour établir une connexion à Azure Cache pour Redis. Elle attend la transmission du nom d’hôte et du mot de passe (clé d’accès) via les variables d’environnement `REDIS_HOSTNAME` et `REDIS_PASSWORD` respectivement. Le format de l’URL de connexion est `rediss://<username>:<password>@<hostname>` : Azure Cache pour Redis accepte uniquement les connexions sécurisées avec [au minimum TLS version 1.2](cache-remove-tls-10-11.md).

L’appel à [redis::Client::open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) effectue une validation de base pendant que [get_connection()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) lance réellement la connexion. Le programme s’arrête en cas d’échec de la connectivité quelle qu’en soit la raison, telle qu’un mot de passe incorrect.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

La fonction `basics` couvre les commandes [SET](https://redis.io/commands/set), [GET](https://redis.io/commands/get) et [INCR](https://redis.io/commands/incr). L’API de bas niveau est utilisée pour `SET` et `GET`, qui définit et récupère la valeur d’une clé nommée `foo`. La commande `INCRBY` est exécutée à l’aide d’une API de haut niveau, autrement dit [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) incrémente la valeur d’une clé (nommée `counter`) de `2` avant un appel à [get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) pour la récupérer.

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

L’extrait de code ci-dessous illustre les fonctionnalités d’une structure de données Redis `HASH`. [HSET](https://redis.io/commands/hset) est appelée à l’aide de l’API de bas niveau pour stocker des informations (`name`, `version`, `repo`) sur les pilotes Redis (clients). Par exemple, les détails du pilote Rust (utilisé dans cet exemple de code) sont capturés sous la forme d’un [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html), puis transmis à l’API de bas niveau. Ils sont ensuite récupérés avec [HGETALL](https://redis.io/commands/hgetall).

`HSET` peut également être exécutée à l’aide d’une API de haut niveau avec [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) qui accepte un tableau de tuples. [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) est ensuite exécutée pour extraire la valeur d’un attribut unique (`repo` dans ce cas).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

Dans la fonction ci-dessous, vous pouvez voir comment utiliser une structure de données `LIST`. [LPUSH](https://redis.io/commands/lpush) est exécutée (avec l’API de bas niveau) pour ajouter une entrée à la liste et la méthode [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) de haut niveau permet de la récupérer dans la liste. Ensuite, la méthode [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) est utilisée pour ajouter deux entrées à la liste, qui sont ensuite extraites avec la méthode [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) de bas niveau.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Vous pouvez voir ici quelques-unes des opérations `SET`. La méthode [sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (API de haut niveau) est utilisée pour ajouter deux entrées à un `SET` nommé `users`. [SISMEMBER](https://redis.io/commands/hset) est ensuite exécutée (API de bas niveau) pour vérifier l’existence de `user1`. Enfin, [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) permet d’extraire et de parcourir toutes les entrées définies sous la forme d’un vecteur ([Vec<String>](https://doc.rust-lang.org/std/vec/struct.Vec.html)).

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

La fonction `sorted_set` ci-dessous illustre la structure de données Sorted Set. [ZADD](https://redis.io/commands/zadd) est appelée (avec l’API de bas niveau) pour ajouter un score d’entier aléatoire pour un joueur (`player-1`). Ensuite, la méthode [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) (API de haut niveau) est utilisée pour ajouter d’autres joueurs (`player-2` à `player-5`) et leurs scores respectifs (générés de manière aléatoire). Le nombre d’entrées dans l’ensemble trié est déterminé à l’aide de [ZCARD](https://redis.io/commands/zcard) et utilisé comme limite de la commande [ZRANGE](https://redis.io/commands/zrange) (appelée avec l’API de bas niveau) pour lister les joueurs avec leurs scores dans l’ordre croissant.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Commencez par cloner l’application à partir de GitHub.

1. Ouvrez une invite de commandes, puis créez un dossier nommé `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

1. Ouvrez une fenêtre de terminal git, comme git bash. Utilisez la commande `cd` pour passer au nouveau dossier dans lequel vous allez cloner l’exemple d’application.

    ```bash
    cd "C:\git-samples"
    ```

1. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Exécution de l'application

L’application accepte les informations de connectivité et d’identification sous la forme de variables d’environnement. 

1. Extrayez le **nom d’hôte** et les **clés d’accès** (disponibles par le biais de Clés d’accès) pour l’instance Azure Cache pour Redis dans le [portail Azure](https://portal.azure.com/).

1. Définissez-les avec les variables d’environnement respectives :

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Dans la fenêtre de terminal, accédez au dossier approprié. Par exemple :

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. Dans le terminal, exécutez la commande suivante pour démarrer l’application.

    ```shell
    cargo run
    ```
    
    Vous verrez un résultat similaire à ceci :
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Si vous souhaitez exécuter une fonction spécifique, commentez d’autres fonctions dans la fonction `main` :
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous en avez terminé avec le groupe de ressources et les ressources Azure que vous avez créés dans ce démarrage rapide, vous pouvez les supprimer pour éviter des frais.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et l’ensemble des ressources qu’il contient sont supprimés définitivement. Si vous avez créé votre instance Azure Cache pour Redis dans un groupe de ressources existant que vous souhaitez conserver, vous pouvez supprimer uniquement le cache en sélectionnant **Supprimer** dans la page **Vue d’ensemble** du cache. 

Pour supprimer le groupe de ressources et son instance Azure Cache pour Redis :

1. Dans le **portail Azure**, recherchez et sélectionnez [Groupes de ressources](https://portal.azure.com).
1. Dans la zone de texte **Filtrer par nom**, entrez le nom du groupe de ressources contenant votre instance de cache, puis sélectionnez-le dans les résultats de la recherche. 
1. Dans la page de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Tapez le nom du groupe de ressources, puis sélectionnez **Supprimer**.
   
   ![Supprimer votre groupe de ressources pour Azure Cache pour Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser le pilote Rust pour Redis afin de vous connecter et d’exécuter des opérations dans Azure Cache pour Redis.

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET simple qui utilise un cache Azure pour Redis.](./cache-web-app-howto.md)
