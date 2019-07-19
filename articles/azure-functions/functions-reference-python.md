---
title: Informations de référence pour Azure Functions à destination des développeurs Python
description: Développer des fonctions avec Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, fonctions, traitement des événements, calcul dynamique, architecture serverless, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 249e5ac33b1420ada2cda45ea729471351f21adf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341986"
---
# <a name="azure-functions-python-developer-guide"></a>Guide des développeurs Python sur Azure Functions

Cet article est une introduction au développement d’Azure Functions avec Python. Le contenu ci-dessous suppose d’avoir lu le [Guide de développement Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Modèle de programmation

Une fonction Azure doit être une méthode sans état qui traite une entrée et produit une sortie dans un script Python. Par défaut, le runtime s’attend à ce que la méthode soit implémentée en tant que méthode globale nommée `main()` dans le fichier `__init__.py`.

Vous pouvez modifier la configuration par défaut en spécifiant les propriétés `scriptFile` et `entryPoint` dans le fichier *function.json*. Par exemple, le fichier _function.json_ (voir ci-dessous) indique au runtime d’utiliser la méthode `customentry()` dans le fichier _main.py_ comme point d’entrée de la fonction Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Les données issues des déclencheurs et des liaisons sont liées à la fonction par des attributs de méthode avec la propriété `name` qui est définie dans le fichier *function.json*. L’exemple de fichier _function.json_ ci-dessous décrit une fonction simple déclenchée par une requête HTTP nommée `req` :

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

Le fichier `__init__.py` contient le code de fonction suivant :

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Si vous souhaitez utiliser les fonctionnalités IntelliSense et de saisie semi-automatique fournies par votre éditeur de code, vous pouvez également déclarer les types d’attributs et le type de retour dans la fonction à l’aide d’annotations Python. 

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Utilisez les annotations Python incluses dans le package [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) pour lier des entrées et des sorties à vos méthodes.

## <a name="folder-structure"></a>Structure de dossiers

La structure de dossiers d’un projet Python Functions se présente ainsi :

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

Il existe un fichier [host.json](functions-host-json.md) partagé que vous pouvez utiliser pour configurer l’application de fonction. Chaque fonction a son propre fichier de code et son propre fichier de configuration de liaison (function.json). 

Le code partagé doit être conservé dans un dossier distinct. Pour faire référence à des modules dans le dossier SharedCode, vous pouvez utiliser la syntaxe suivante :

```
from __app__.SharedCode import myFirstHelperFunction
```

Quand vous déployez un projet Function dans votre application de fonction sur Azure, tout le contenu du dossier *FunctionApp* (et non le dossier proprement dit) doit être inclus dans le package.

## <a name="triggers-and-inputs"></a>Déclencheurs et entrées

Les entrées sont réparties en deux catégories dans Azure Functions : l’entrée du déclencheur et l’entrée supplémentaire. Ces entrées sont différentes dans le fichier `function.json`, mais leur utilisation est identique dans le code Python.  Les chaînes de connexion ou les secrets pour les sources de déclencheur et d’entrée sont mappés aux valeurs dans le fichier `local.settings.json` lors d’une exécution locale, et aux paramètres d’application lors d’une exécution dans Azure. 

L’exemple de code suivant illustre la différence entre les deux :

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quand la fonction est appelée, la requête HTTP est passée à la fonction dans `req`. Une entrée est récupérée du Stockage Blob Azure sur la base de l’_ID_ présent dans l’URL de la route, puis elle est mise à disposition comme `obj` dans le corps de la fonction.  Ici, le compte de stockage spécifié est la chaîne de connexion trouvée dans `AzureWebJobsStorage`, qui est le même compte de stockage utilisé par l’application de fonction.


## <a name="outputs"></a>Outputs

Les sorties peuvent être exprimées aussi bien dans une valeur de retour que dans des paramètres de sortie. S’il n’y a qu’une seule sortie, nous recommandons d’utiliser la valeur de retour. Lorsqu’il y en a plusieurs, il est nécessaire d’utiliser les paramètres de sortie.

Pour utiliser la valeur de retour d’une fonction comme valeur d’une liaison de sortie, la propriété `name` de la liaison doit être définie sur `$return` dans `function.json`.

Pour produire plusieurs sorties, utilisez la méthode `set()` fournie par l’interface `azure.functions.Out` afin d’affecter une valeur à la liaison. Par exemple, la fonction suivante peut placer un message dans une file d’attente tout en renvoyant une réponse HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Journalisation

L’accès à l’enregistreur d’événements du runtime d’Azure Functions se fait par l’intermédiaire du gestionnaire [`logging`](https://docs.python.org/3/library/logging.html#module-logging) racine dans l’application de fonction. Cet enregistreur, lié à Application Insights, permet de signaler les avertissements et les erreurs générés lors de l’exécution de la fonction.

L’exemple suivant enregistre un message d’informations lorsque la fonction est appelée avec un déclencheur HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Des méthodes d’enregistrement supplémentaires permettent d’écrire dans la console à différents niveaux de trace :

| Méthode                 | Description                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_message_)**   | Écrit un message de niveau CRITIQUE sur l’enregistreur racine.  |
| logging.**error(_message_)**   | Écrit un message de niveau ERREUR sur l’enregistreur racine.    |
| logging.**warning(_message_)**    | Écrit un message de niveau AVERTISSEMENT sur l’enregistreur racine.  |
| logging.**info(_message_)**    | Écrit un message de niveau INFO sur l’enregistreur racine.  |
| logging.**debug(_message_)** | Écrit un message de niveau DÉBOGAGE sur l’enregistreur racine.  |

## <a name="async"></a>Async

Nous vous recommandons d’écrire votre fonction Azure dans une coroutine asynchrone à l’aide de l’instruction `async def`.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Si la fonction main() est synchrone (sans qualificateur `async`), elle sera automatiquement exécutée dans un pool de threads `asyncio`.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

Pour obtenir le contexte d’appel d’une fonction pendant l’exécution, ajoutez l’argument `context` à sa signature. 

Par exemple :

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe **Context** comporte les méthodes suivantes :

`function_directory`  
Répertoire dans lequel s’exécute la fonction.

`function_name`  
Nom de la fonction.

`invocation_id`  
ID de l’appel de fonction en cours.

## <a name="global-variables"></a>Variables globales

L’état de votre application n’est pas systématiquement conservé en vue des exécutions ultérieures. Toutefois, le runtime Azure Functions réutilise souvent le même processus pour plusieurs exécutions de la même application. Pour mettre en cache les résultats d’un calcul coûteux, vous devez le déclarer en tant que variable globale. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Version de Python et gestion des packages

Actuellement, Azure Functions ne prend en charge que Python 3.6.x (distribution officielle de CPython).

Si vous développez en local avec Azure Functions Core Tools ou Visual Studio Code, ajoutez les noms et les versions des packages requis au fichier `requirements.txt` et installez-les avec `pip`.

Par exemple, le fichier de configuration requise suivant et la commande pip permettent d’installer le package `requests` à partir de PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publier sur Azure

Avant de procéder à la publication, vérifiez que toutes les dépendances sont listées dans le fichier *requirements.txt*, situé à la racine de votre répertoire de projet. Si un compilateur est nécessaire pour le package utilisé, et que celui-ci ne prend pas en charge l’installation de wheels compatibles manylinux de PyPI, la publication sur Azure échoue avec l’erreur suivante : 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Pour générer et configurer automatiquement les binaires nécessaires, [installez Docker](https://docs.docker.com/install/) sur votre ordinateur local et exécutez la commande suivante afin d’utiliser [Azure Functions Core Tools](functions-run-local.md#v2) (func) pour la publication. Veillez à remplacer `<app name>` par le nom de votre application de fonction dans Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

En coulisses, Core Tools utilise Docker pour exécuter l’image [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) comme conteneur sur l’ordinateur local. Avec cet environnement, il génère alors les modules requis et les installe à partir de la distribution source, avant de les packager pour le déploiement final sur Azure.

Pour générer les dépendances et effectuer la publication au moyen d’un système de livraison continue (CD), [utilisez Azure DevOps Pipelines](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops). 

## <a name="unit-testing"></a>Tests unitaires

Les fonctions écrites en Python peuvent être testées comme tout autre code Python à l’aide des frameworks de test standard. Pour la plupart des liaisons, il est possible de créer un objet d’entrée factice en créant une instance d’une classe appropriée à partir du package `azure.functions`.

Voici un exemple de test factice d’une fonction déclenchée via HTTP :

```python
# myapp/__init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/my_function',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            'Hello, Test!',
        )
```

Voici un autre exemple, avec une fonction déclenchée d’une file d’attente :

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>Problèmes connus et FAQ

Tous les problèmes connus et les demandes de fonctionnalités sont listés dans [Problèmes GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Si vous rencontrez un problème qui n’apparaît pas dans GitHub, soumettez un nouveau problème en incluant une description détaillée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* [Liaisons de Stockage Blob](functions-bindings-storage-blob.md)
* [Liaisons HTTP et Webhook](functions-bindings-http-webhook.md)
* [Liaisons de Stockage File d’attente](functions-bindings-storage-queue.md)
* [Déclencheur de minuteur](functions-bindings-timer.md)
