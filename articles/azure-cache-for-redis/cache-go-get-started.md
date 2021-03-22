---
title: Utiliser Azure Cache pour Redis avec Go
description: Ce guide de démarrage rapide explique comment créer une application Go utilisant Azure Cache pour Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 04b582b5ef31e61039c5513ea2a4aa60f1c638e7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121335"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Démarrage rapide : Utiliser Azure Cache pour Redis avec Go

Dans cet article, vous allez apprendre à créer une API REST dans Go qui stockera et récupérera des informations utilisateur avec une structure de données [HASH](https://redis.io/topics/data-types-intro#redis-hashes) dans [Azure Cache pour Redis](./cache-overview.md). 

## <a name="skip-to-the-code-on-github"></a>Passer au code sur GitHub

Si vous souhaitez passer directement au code, consultez le [démarrage rapide Go](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/) sur GitHub.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (version 1.13 ou ultérieure recommandée)
- [Git](https://git-scm.com/downloads)
- Client HTTP tel que [curl](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Créer une instance Cache Redis Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Vérifier le code (facultatif)

Pour savoir comment le code fonctionne, vous pouvez examiner les extraits suivants. Sinon, vous pouvez passer à la section [Exécuter l’application](#run-the-application).

La bibliothèque open source [go-redis](https://github.com/go-redis/redis) permet d’interagir avec Azure Cache pour Redis.

La fonction `main` commence par lire le nom d’hôte et le mot de passe (clé d’accès) pour l’instance d’Azure Cache pour Redis.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Nous établissons ensuite la connexion à Azure Cache pour Redis. Notez que [tls.Config](https://golang.org/pkg/crypto/tls/#Config) est utilisé : Azure Cache pour Redis accepte uniquement les connexions sécurisées avec [au minimum TLS version 1.2](cache-remove-tls-10-11.md).

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Si la connexion réussit, les [gestionnaires HTTP](https://golang.org/pkg/net/http/#HandleFunc) sont configurés pour gérer les opérations `POST` et `GET`, et le serveur HTTP est démarré. 

> [!NOTE] 
> La [bibliothèque gorilla mux](https://github.com/gorilla/mux) est utilisée pour le routage (sachez toutefois qu’elle n’est pas strictement nécessaire et que nous pourrions très bien utiliser la bibliothèque standard pour cet exemple d’application).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

Le struct `userHandler` encapsule un [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client), qui est utilisé par les méthodes `createUser` et `getUser` (le code de ces méthodes n’est pas inclus ici pour rester bref). 

- `createUser`: accepte une charge utile JSON (contenant des informations utilisateur) et l’enregistre en tant que `HASH` dans Azure Cache pour Redis.
- `getUser` : récupère les informations utilisateur de `HASH` ou retourne une réponse HTTP `404` si elles sont introuvables.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Exécution de l'application

L’application accepte les informations de connectivité et d’identification sous la forme de variables d’environnement. 

1. Récupérez le **nom d’hôte** et les **clés d’accès** (disponibles par le biais de Clés d’accès) pour l’instance d’Azure Cache pour Redis dans le [portail Azure](https://portal.azure.com/).

1. Définissez-les avec les variables d’environnement respectives :

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Dans la fenêtre de terminal, accédez au dossier approprié. Par exemple :

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. Dans le terminal, exécutez la commande suivante pour démarrer l’application.

    ```shell
    go run main.go
    ```

Le serveur HTTP démarre sur le port `8080`.

## <a name="test-the-application"></a>Tester l’application

1. Créez quelques entrées utilisateur. L’exemple ci-dessous utilise curl :

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Récupérez un utilisateur existant avec son `id` :

    ```bash
    curl -i localhost:8080/users/1
    ```

    Vous devez obtenir une réponse JSON semblable à la suivante :
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Si vous essayez de récupérer un utilisateur qui n’existe pas, vous obtenez une erreur HTTP `404`. Par exemple :

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
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

Dans ce guide de démarrage rapide, vous avez appris à utiliser Go avec Azure Cache pour Redis. Vous avez configuré et exécuté une application simple basée sur l’API REST pour créer et obtenir des informations utilisateur avec une structure de données `HASH`.

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET simple qui utilise un cache Azure pour Redis.](./cache-web-app-howto.md)
