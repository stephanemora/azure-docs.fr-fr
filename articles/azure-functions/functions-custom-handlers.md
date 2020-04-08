---
title: Gestionnaires personnalisés Azure Functions (préversion)
description: Apprenez à utiliser Azure Functions avec n’importe quel langage ou version de runtime.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475135"
---
# <a name="azure-functions-custom-handlers-preview"></a>Gestionnaires personnalisés Azure Functions (préversion)

Chaque application Functions est exécutée par un gestionnaire propre au langage. Même si Azure Functions prend en charge un grand nombre de [gestionnaires de langage](./supported-languages.md) par défaut, dans certains cas, vous tiendrez peut-être à avoir davantage de contrôle sur l’environnement d’exécution d’application. Les gestionnaires personnalisés vous offrent ce contrôle additionnel.

Les gestionnaires personnalisés sont des serveurs web légers qui reçoivent des événements de l’hôte Functions. Tout langage qui prend en charge les primitives HTTP peut implémenter un gestionnaire personnalisé.

Les gestionnaires personnalisés sont particulièrement adaptés dans les cas où vous voulez :

- Implémenter une application Functions dans un autre langage que ceux officiellement pris en charge
- Implémenter une application Functions dans une version de langage ou un runtime non pris en charge par défaut
- Bénéficier d’un contrôle précis sur l’environnement d’exécution d’application

Avec les gestionnaires personnalisés, tous les [déclencheurs et liaisons d’entrée et de sortie](./functions-triggers-bindings.md) sont pris en charge via des [bundles d’extension](./functions-bindings-register.md).

## <a name="overview"></a>Vue d’ensemble

Le diagramme suivant illustre la relation entre l’hôte Functions et un serveur web implémenté en tant que gestionnaire personnalisé.

![Vue d’ensemble d’un gestionnaire personnalisé Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Un événement déclenche une demande qui est envoyée à l’hôte Functions. L’événement comporte soit une charge utile HTTP brute (pour les fonctions déclenchées par HTTP sans liaisons), soit une charge utile qui détient les données de liaison d’entrée de la fonction.
- L’hôte Functions proxyse ensuite la demande au serveur web en émettant une [charge utile de demande](#request-payload).
- Le serveur web exécute la fonction individuelle, puis retourne une [charge utile de réponse](#response-payload) à l’hôte Functions.
- L’hôte Functions proxyse la réponse en tant que charge utile de liaison de sortie à destination de la cible.

Une application Azure Functions implémentée en tant que gestionnaire personnalisé doit configurer les fichiers *host.json* et *function.json* en respectant quelques conventions.

## <a name="application-structure"></a>Structure d'application

Pour implémenter un gestionnaire personnalisé, votre application a besoin des éléments suivants :

- Un fichier *host.json* à la racine de l’application
- Un fichier *function.json* pour chaque fonction (à l’intérieur d’un dossier dont le nom correspond à celui de la fonction)
- Une commande, un script ou un exécutable qui exécute un serveur web

Le diagramme suivant montre comment ces fichiers se présentent dans le système de fichiers pour une fonction nommée « order ».

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuration

L’application est configurée via le fichier *host.json*. Ce fichier indique à l’hôte Functions où envoyer les demandes en pointant vers un serveur web capable de traiter les événements HTTP.

Un gestionnaire personnalisé est configuré dans le fichier *host.json* avec des détails sur la façon d’exécuter le serveur web dans la section `httpWorker`.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

La section `httpWorker` pointe vers une cible définie par `defaultExecutablePath`. La cible d’exécution peut être une commande, un exécutable ou un fichier où le serveur web est implémenté.

Pour les applications scriptées, `defaultExecutablePath` pointe vers le runtime du langage de script et `defaultWorkerPath` pointe vers l’emplacement du fichier de script. L’exemple suivant montre comment une application JavaScript est configurée dans Node.js en tant que gestionnaire personnalisé.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Vous pouvez aussi transmettre des arguments en utilisant le tableau `arguments` :

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Les arguments sont nécessaires pour bon nombre de configurations de débogage. Pour plus d’informations, consultez la section [Débogage](#debugging).

> [!NOTE]
> Le fichier *host.json* doit figurer dans la structure de répertoires au même niveau que le serveur web en cours d’exécution. Certains langages et chaînes d’outils ne placent pas par défaut ce fichier à la racine de l’application.

#### <a name="bindings-support"></a>Prise en charge des liaisons

Vous pouvez rendre disponibles des déclencheurs standard, de même que des liaisons d’entrée et de sortie, en référençant des [bundles d’extension](./functions-bindings-register.md) dans le fichier *host.json*.

### <a name="function-metadata"></a>Métadonnées de fonction

Quand il est utilisé avec un gestionnaire personnalisé, le contenu de *function.json* ne se définit pas différemment qu’une fonction dans un autre contexte. La seule exigence est que les fichiers *function.json* doivent se trouver dans un dossier dont le nom correspond à celui de la fonction.

### <a name="request-payload"></a>Charge utile de demande

La charge utile de demande pour les fonctions HTTP pures est la charge utile de demande HTTP brute. Les fonctions HTTP pures sont définies en tant que fonctions, sans aucune liaison d’entrée ou de sortie, qui retournent une réponse HTTP.

Tout autre type de fonction qui comprend des liaisons d’entrée, de sortie ou qui est déclenchée via une source d’événement autre que HTTP possède une charge utile de demande personnalisée.

Le code suivant représente un exemple de charge utile de demande. La charge utile comprend une structure JSON à deux membres : `Data` et `Metadata`.

Le membre `Data` comprend des clés dont le nom correspond aux noms d’entrée et de déclencheur définis dans le tableau de liaisons du fichier *function.json*.

Le membre `Metadata` comprend les [métadonnées générées à partir de la source d’événement](./functions-bindings-expressions-patterns.md#trigger-metadata).

Compte tenu des liaisons définies dans le fichier *function.json* suivant :

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Une charge utile de demande similaire à cet exemple est retournée:

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>Charge utile de réponse

Par convention, les réponses de fonction se présentent sous la forme de paires clé/valeur. Les clés prises en charge sont les suivantes :

| <nobr>Clé de charge utile</nobr>   | Type de données | Notes                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Contient les valeurs de réponse définies par le tableau `bindings` du fichier *function.json*.<br /><br />Par exemple, si une fonction est configurée avec une liaison de sortie de stockage d’objet blob nommée « blob », `Outputs` contient une clé nommée `blob`, qui prend la valeur de l’objet blob. |
| `Logs`        | tableau     | Les messages figurent dans les journaux d’invocations Functions.<br /><br />Quand l’exécution se produit dans Azure, les messages s’affichent dans Application Insights. |
| `ReturnValue` | string    | Utilisé pour fournir une réponse quand une sortie est configurée avec la valeur `$return` dans le fichier *function.json*. |

Consultez l’[exemple de charge utile](#bindings-implementation).

## <a name="examples"></a>Exemples

Les gestionnaires personnalisés peuvent être implémentés dans n’importe quel langage prenant en charge les événements HTTP. Bien que Azure Functions [prenne entièrement en charge JavaScript et Node.js](./functions-reference-node.md), les exemples suivants montrent comment implémenter un gestionnaire personnalisé en utilisant JavaScript dans Node.js à des fins didactiques.

> [!TIP]
> En plus de donner des indications sur la façon d’implémenter un gestionnaire personnalisé dans d’autres langages, les exemples Node.js présentés ici peuvent aussi vous être utiles si vous souhaitez exécuter une application Functions dans une version non prise en charge de Node.js.

## <a name="http-only-function"></a>Fonction HTTP uniquement

L’exemple suivant montre comment configurer une fonction déclenchée par HTTP sans aucune autre liaison ni sortie. Le scénario implémenté dans cet exemple présente une fonction nommée `http` qui accepte un `GET` ou un `POST`.

L’extrait de code suivant montre comment se compose une demande à la fonction.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implémentation

Dans un dossier nommé *http*, le fichier *function.json* configure la fonction déclenchée par HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

La fonction est configurée pour accepter à la fois les demandes `GET` et `POST`, et la valeur de résultat est fournie via un argument nommé `res`.

À la racine de l’application, le fichier *host.json* est configuré pour exécuter Node.js et indique le fichier `server.js`.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Le fichier *server.js* implémente un serveur web et une fonction HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

Dans cet exemple, un serveur web est créé à l’aide d’Express en vue de gérer les événements HTTP et est configuré pour écouter les demandes via `FUNCTIONS_HTTPWORKER_PORT`.

La fonction est définie sur le chemin `/hello`. Les demandes `GET` sont gérées en retournant un objet JSON simple, et les demandes `POST` ont accès au corps de la demande via `req.body`.

La route de la fonction « order » est ici `/hello` et non `/api/hello`, car l’hôte Functions proxyse la demande au gestionnaire personnalisé.

>[!NOTE]
>`FUNCTIONS_HTTPWORKER_PORT` n’est pas le port public utilisé pour appeler la fonction. Ce port est utilisé par l’hôte Functions pour appeler le gestionnaire personnalisé.

## <a name="function-with-bindings"></a>Fonction avec liaisons

Le scénario implémenté dans cet exemple présente une fonction nommée `order` qui accepte un `POST` avec une charge utile représentant une commande de produit. Quand une commande est transmise à la fonction, un message du service Stockage File d’attente est créé et une réponse HTTP est retournée.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Implémentation

Dans un dossier nommé *order*, le fichier *function.json* configure la fonction déclenchée par HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

Cette fonction est définie comme étant une [fonction déclenchée par HTTP](./functions-bindings-http-webhook-trigger.md) qui retourne une [réponse HTTP](./functions-bindings-http-webhook-output.md) et génère un message de [Stockage File d’attente](./functions-bindings-storage-queue-output.md).

À la racine de l’application, le fichier *host.json* est configuré pour exécuter Node.js et indique le fichier `server.js`.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Le fichier *server.js* implémente un serveur web et une fonction HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

Dans cet exemple, un serveur web est créé à l’aide d’Express en vue de gérer les événements HTTP et est configuré pour écouter les demandes via `FUNCTIONS_HTTPWORKER_PORT`.

La fonction est définie sur le chemin `/order`.  La route de la fonction « order » est ici `/order` et non `/api/order`, car l’hôte Functions proxyse la demande au gestionnaire personnalisé.

À mesure que les demandes `POST` sont envoyées à cette fonction, les données sont exposées par les moyens suivants :

- Le corps de la demande est disponible via `req.body`
- Les données transmises à la fonction sont disponibles via `req.body.Data.req.Body`

La réponse de la fonction se présente sous la forme d’une paire clé/valeur où le membre `Outputs` contient une valeur JSON et où les clés correspondent aux sorties définies dans le fichier *function.json*.

En définissant `message` comme étant égal au message issu de la demande et `res` égal à la réponse HTTP attendue, cette fonction génère un message dans Stockage File d’attente et retourne une réponse HTTP.

## <a name="debugging"></a>Débogage

Pour déboguer votre application de gestionnaire personnalisé Functions, vous devez ajouter les arguments appropriés en fonction du langage et du runtime utilisés pour activer le débogage.

Par exemple, pour déboguer une application Node.js, l’indicateur `--inspect` est passé en tant qu’argument dans le fichier *host.json*.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> La configuration du débogage est intégrée à votre fichier *host.json*, ce qui signifie que vous devrez peut-être supprimer certains arguments avant le déploiement en production.

Avec cette configuration, vous pouvez lancer le processus hôte Functions à l’aide de la commande suivante :

```bash
func host start
```

Une fois le processus lancé, vous pouvez attacher un débogueur et atteindre des points d’arrêt.

### <a name="visual-studio-code"></a>Visual Studio Code

L’exemple de configuration ci-dessous montre comment configurer votre fichier *launch.json* de façon à connecter votre application au déboguer de Visual Studio Code.

Sachant que cet exemple repose sur Node.js, vous devrez peut-être le modifier pour d’autres langages ou runtimes.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Déploiement

Il est possible de déployer un gestionnaire personnalisé pour pratiquement chaque option d’hébergement Azure Functions (voir les [restrictions](#restrictions)). Si votre gestionnaire nécessite des dépendances personnalisées (comme le runtime d’un langage), vous devrez peut-être utiliser un [conteneur personnalisé](./functions-create-function-linux-custom-image.md).

## <a name="restrictions"></a>Restrictions

- Les gestionnaires personnalisés ne sont pas pris en charge dans les plans de consommation Linux.
- Le serveur web doit démarrer dans un délai de 60 secondes.

## <a name="samples"></a>Exemples

Vous trouverez sur [GitHub un dépôt d’exemples de gestionnaires personnalisés](https://github.com/Azure-Samples/functions-custom-handlers) qui montrent comment implémenter des fonctions dans différents langages.
