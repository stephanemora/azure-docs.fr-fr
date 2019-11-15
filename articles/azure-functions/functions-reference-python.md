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
ms.openlocfilehash: e0e649045e3efe488804fd37c030fe01991ad232
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803625"
---
# <a name="azure-functions-python-developer-guide"></a>Guide des développeurs Python sur Azure Functions

Cet article est une introduction au développement d’Azure Functions avec Python. Le contenu ci-dessous suppose d’avoir lu le [Guide de développement Azure Functions](functions-reference.md). 

Pour obtenir des exemples de projets Functions autonomes en Python, consultez les [exemples Functions en Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modèle de programmation

Azure Functions s’attend à ce qu’une fonction soit une méthode sans état qui traite une entrée et produit une sortie dans un script Python. Par défaut, le runtime s’attend à ce que la méthode soit implémentée en tant que méthode globale nommée `main()` dans le fichier `__init__.py`. Vous pouvez également [spécifier un autre point d’entrée](#alternate-entry-point).

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

Vous pouvez également déclarer explicitement les types d’attributs et le type de retour dans la fonction à l’aide des annotations de type Python. L’utilisation des fonctionnalités IntelliSense et de saisie semi-automatique fournies par de nombreux éditeurs de code Python s’en trouve facilitée.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Utilisez les annotations Python incluses dans le package [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) pour lier des entrées et des sorties à vos méthodes.

## <a name="alternate-entry-point"></a>Autre point d’entrée

Vous pouvez changer le comportement par défaut d’une fonction en spécifiant éventuellement les propriétés `scriptFile` et `entryPoint` dans le fichier *function.json*. Par exemple, le fichier _function.json_ (voir ci-dessous) indique au runtime d’utiliser la méthode `customentry()` dans le fichier _main.py_ comme point d’entrée de la fonction Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Structure de dossiers

La structure de dossiers d’un projet Python Functions se présente comme l’exemple suivant :

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
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

Si vous voulez faire référence à des modules locaux pour une fonction, vous pouvez utiliser la syntaxe d’importation relative de la façon suivante :

```
from . import example
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

Quand la fonction est appelée, la requête HTTP est passée à la fonction dans `req`. Une entrée est récupérée du Stockage Blob Azure sur la base de l’_ID_ présent dans l’URL de la route, puis elle est mise à disposition comme `obj` dans le corps de la fonction.  Ici, le compte de stockage spécifié est la chaîne de connexion trouvée, qui est le même compte de stockage utilisé par l’application de fonction.


## <a name="outputs"></a>Outputs

Les sorties peuvent être exprimées aussi bien dans une valeur de retour que dans des paramètres de sortie. S’il n’y a qu’une seule sortie, nous recommandons d’utiliser la valeur de retour. Lorsqu’il y en a plusieurs, il est nécessaire d’utiliser les paramètres de sortie.

Pour utiliser la valeur de retour d’une fonction comme valeur d’une liaison de sortie, la propriété `name` de la liaison doit être définie sur `$return` dans `function.json`.

Pour produire plusieurs sorties, utilisez la méthode `set()` fournie par l’interface [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) afin d’affecter une valeur à la liaison. Par exemple, la fonction suivante peut placer un message dans une file d’attente tout en renvoyant une réponse HTTP.

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
| **`critical(_message_)`**   | Écrit un message de niveau CRITIQUE sur l’enregistreur racine.  |
| **`error(_message_)`**   | Écrit un message de niveau ERREUR sur l’enregistreur racine.    |
| **`warning(_message_)`**    | Écrit un message de niveau AVERTISSEMENT sur l’enregistreur racine.  |
| **`info(_message_)`**    | Écrit un message de niveau INFO sur l’enregistreur racine.  |
| **`debug(_message_)`** | Écrit un message de niveau DÉBOGAGE sur l’enregistreur racine.  |

Pour en savoir plus sur la journalisation, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Déclencheur et liaisons HTTP

Le déclencheur HTTP est défini dans le fichier function.jon. Le `name` de la liaison doit correspondre au paramètre nommé dans la fonction. Dans les exemples précédents, un nom de liaison `req` est utilisé. Ce paramètre est un objet [HttpRequest], et un objet [HttpResponse] est retourné.

À partir de l’objet [HttpRequest], vous pouvez obtenir des en-têtes de demande, des paramètres de requête, des paramètres de route et le corps du message. 

L’exemple suivant provient du [modèle de déclencheur HTTP pour Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Dans cette fonction, la valeur du paramètre de requête `name` est obtenue à partir du paramètre `params` de l’objet [HttpRequest]. Le corps du message encodé JSON est lu à l’aide de la méthode `get_json`. 

De même, vous pouvez définir les `status_code` et `headers` pour le message de réponse dans l’objet [HttpResponse] retourné.

## <a name="concurrency"></a>Accès concurrentiel

Par défaut, le runtime Functions Python ne peut traiter qu’un appel de fonction à la fois. Ce niveau de concurrence peut ne pas suffire dans une ou plusieurs des conditions suivantes :

+ Vous essayez de gérer plusieurs appels effectués en même temps.
+ Vous traitez un grand nombre d’événements d’E/S.
+ Votre application est liée aux E/S.

Dans ces cas de figure, vous pouvez améliorer les performances par une exécution asynchrone et l’utilisation de plusieurs processus Worker de langage.  

### <a name="async"></a>Async

Nous vous recommandons d’utiliser l’instruction `async def` pour que votre fonction s’exécute en tant que coroutine asynchrone.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

Quand la fonction `main()` est synchrone (sans le qualificateur `async`), elle s’exécute automatiquement dans un pool de threads `asyncio`.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Utiliser plusieurs processus Worker de langage

Par défaut, chaque instance d’hôte Functions a un seul processus Worker de langage. Toutefois, il est possible d’utiliser plusieurs processus Worker de langage par instance d’hôte. Les appels de fonction peuvent alors être répartis uniformément entre ces processus Worker de langage. Pour modifier cette valeur, utilisez le paramètre d’application [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). 

## <a name="context"></a>Context

Pour obtenir le contexte d’appel d’une fonction pendant l’exécution, ajoutez l’argument [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) à sa signature. 

Par exemple :

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe [**Contexte**](/python/api/azure-functions/azure.functions.context?view=azure-python) contient les attributs de chaîne suivants :

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

## <a name="environment-variables"></a>Variables d’environnement

Dans Functions, les [paramètres de l’application](functions-app-settings.md), par exemple, les chaînes de connexion de service, sont exposés en tant que variables d’environnement pendant l’exécution. Vous pouvez accéder à ces paramètres en déclarant `import os` puis en utilisant `setting = os.environ["setting-name"]`.

L’exemple suivant obtient le [paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings), avec la clé nommée `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Pour le développement local, les paramètres d’application sont [conservés dans le fichier local.settings.json](functions-run-local.md#local-settings-file).  

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

Avant de procéder à la publication, vérifiez que toutes les dépendances sont listées dans le fichier *requirements.txt*, situé à la racine de votre répertoire de projet. Azure Functions peut [créer à distance](functions-deployment-technologies.md#remote-build) ces dépendances.

Les dossiers et fichiers projet qui sont exclus de la publication, y compris le dossier d’environnement virtuel, sont listés dans le fichier .funcignore. 

[Azure Functions Core Tools](functions-run-local.md#v2) et l’[extension Azure Functions pour VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) effectueront une build distante par défaut. Par exemple, utilisez la commande suivante :

```bash
func azure functionapp publish <app name>
```

Si vous souhaitez générer votre application localement au lieu de le faire sur Azure, [installez Docker](https://docs.docker.com/install/) sur votre machine locale et exécutez la commande suivante afin d’utiliser [Azure Functions Core Tools](functions-run-local.md#v2) (func) pour la publication. Veillez à remplacer `<app name>` par le nom de votre application de fonction dans Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

En coulisses, Core Tools utilise Docker pour exécuter l’image [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) comme conteneur sur l’ordinateur local. Avec cet environnement, il génère alors les modules requis et les installe à partir de la distribution source, avant de les packager pour le déploiement final sur Azure.

Pour générer les dépendances et effectuer la publication au moyen d’un système de livraison continue (CD), [utilisez Azure Pipelines](functions-how-to-azure-devops.md). 

## <a name="unit-testing"></a>Tests unitaires

Les fonctions écrites en Python peuvent être testées comme tout autre code Python à l’aide des frameworks de test standard. Pour la plupart des liaisons, il est possible de créer un objet d’entrée factice en créant une instance d’une classe appropriée à partir du package `azure.functions`. Comme le package [`azure.functions`](https://pypi.org/project/azure-functions/) n’est pas immédiatement disponible, assurez-vous de l’installer via votre fichier `requirements.txt`, comme décrit dans la section [Version de Python et gestion des packages](#python-version-and-package-management) ci-dessus.

Voici un exemple de test factice d’une fonction déclenchée via HTTP :

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
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
## <a name="temporary-files"></a>Fichiers temporaires

La méthode `tempfile.gettempdir()` retourne un dossier temporaire, `/tmp` sous Linux. Votre application peut utiliser ce répertoire pour stocker les fichiers temporaires générés et utilisés par vos fonctions lors de l’exécution. 

> [!IMPORTANT]
> Il n’est pas garanti que les fichiers écrits dans le répertoire temporaire persistent d’un appel à l’autre. Lors de la montée en charge (scale out), les fichiers temporaires ne sont pas partagés entre les instances. 

L’exemple suivant crée un fichier temporaire nommé dans le répertoire temporaire (`/tmp`) :

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

## <a name="known-issues-and-faq"></a>Problèmes connus et FAQ

Tous les problèmes connus et les demandes de fonctionnalités sont listés dans [Problèmes GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Si vous rencontrez un problème qui n’apparaît pas dans GitHub, soumettez un nouveau problème en incluant une description détaillée.

### <a name="cross-origin-resource-sharing"></a>Partage de ressources cross-origin

Azure Functions prend en charge le partage des ressources cross-origin (CORS). CORS est configuré [dans le portail](functions-how-to-use-azure-function-app-settings.md#cors) et par le biais d’[Azure CLI](/cli/azure/functionapp/cors). La liste des origines autorisées CORS s’applique au niveau de l’application de fonction. Quand CORS est activé, les réponses incluent l’en-tête `Access-Control-Allow-Origin`. Pour plus d'informations, consultez la page [Partage des ressources cross-origin](functions-how-to-use-azure-function-app-settings.md#cors).

La liste des origines autorisées [n’est pas prise en charge](https://github.com/Azure/azure-functions-python-worker/issues/444) pour les applications de fonction Python. En raison de cette limitation, vous devez définir expressément l’en-tête `Access-Control-Allow-Origin` dans vos fonctions HTTP, comme indiqué dans l’exemple suivant :

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Veillez également à mettre à jour votre fichier function.json pour prendre en charge la méthode HTTP :

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Le navigateur Chrome utilise cette méthode pour négocier la liste des origines autorisées. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Documentation sur l’API du package Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* [Liaisons de Stockage Blob](functions-bindings-storage-blob.md)
* [Liaisons HTTP et Webhook](functions-bindings-http-webhook.md)
* [Liaisons de Stockage File d’attente](functions-bindings-storage-queue.md)
* [Déclencheur de minuteur](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
