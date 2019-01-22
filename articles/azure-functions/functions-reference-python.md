---
title: Informations de référence pour Azure Functions à destination des développeurs Python
description: Développer des fonctions avec Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, fonctions, traitement des événements, calcul dynamique, architecture serverless, Python
ms.service: functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: dadb645067a0f6cac436d638a829ac4c0937bd60
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304371"
---
# <a name="azure-functions-python-developer-guide"></a>Guide des développeurs Python sur Azure Functions

Cet article est une introduction au développement d’Azure Functions avec Python. Le contenu ci-dessous suppose d’avoir lu le [Guide de développement Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Modèle de programmation

Une fonction Azure doit être une méthode sans état qui traite une entrée et produit une sortie dans un script Python. Par défaut, le runtime exige qu’elle soit implémentée sous forme de méthode globale nommée `main()` dans le fichier `__init__.py`.

Vous pouvez modifier la configuration par défaut en spécifiant les propriétés `scriptFile` et `entryPoint` dans le fichier `function.json`. Par exemple, _function.json_ (ci-dessous) indique au runtime d’utiliser la méthode _customentry()_ du fichier _main.py_ comme point d’entrée de la fonction Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Les données issues des déclencheurs et des liaisons sont liées à la fonction par des attributs de méthode avec la propriété `name` définie dans le fichier de configuration `function.json`. Par exemple, _function.json_ (ci-dessous) décrit une fonction simple déclenchée par une requête HTTP nommée `req` :

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

Si vous le souhaitez, vous pouvez également déclarer les types de paramètres et le type de retour dans la fonction à l’aide des annotations de type Python. Par exemple, il est possible d’écrire la même fonction avec des annotations :

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
 | - extensions.csproj
 | - bin
```

Il existe un fichier [host.json](functions-host-json.md) partagé que vous pouvez utiliser pour configurer l’application de fonction. Chaque fonction a son propre fichier de code et son propre fichier de configuration de liaison (function.json). 

Le code partagé doit être conservé dans un dossier distinct. Pour faire référence à des modules dans le dossier SharedCode, vous pouvez utiliser la syntaxe suivante :

```
from ..SharedCode import myFirstHelperFunction
```

Les extensions de liaison utilisées par le runtime de Functions sont définies dans le fichier `extensions.csproj`, et les fichiers bibliothèque proprement dits dans le dossier `bin`. Si vous développez en local, vous devez [inscrire les extensions de liaison](functions-triggers-bindings.md#local-development-azure-functions-core-tools) avec Azure Functions Core Tools. 

Lorsque vous déployez un projet Functions sur votre application de fonction dans Azure, l’ensemble du contenu du dossier FunctionApp (et non le dossier proprement dit) doit être inclus dans le package.

## <a name="inputs"></a>Entrées

Les entrées sont réparties en deux catégories dans Azure Functions : l’entrée du déclencheur et l’entrée supplémentaire. Bien qu’elles soient différentes dans `function.json`, leur utilisation est identique dans le code Python. L’extrait de code suivant en est un exemple :

```json
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

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Lorsque la fonction est appelée, la requête HTTP est transmise à la fonction dans `req`. Une entrée est récupérée dans le Stockage Blob Azure à partir de _l’ID_ dans l’URL d’itinéraire et mise à disposition comme `obj` dans le corps de fonction.

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

## <a name="importing-shared-code-into-a-function-module"></a>Importer du code partagé dans un module de fonction

Les modules Python publiés avec des modules de fonction doivent être importés suivant la syntaxe d’importation relative :

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Vous pouvez également placer le code partagé dans un package autonome, le publier dans une instance PyPI publique ou privée et le définir comme dépendance standard.

## <a name="async"></a>Async

Dans la mesure où il ne peut y avoir qu’un seul processus Python par application de fonction, il est recommandé d’implémenter une fonction Azure sous forme de coroutine asynchrone avec l’instruction `async def`.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Si la fonction main() est synchrone (pas de qualificateur `async`), nous l’exécutons automatiquement dans un pool de threads `asyncio`.

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

## <a name="python-version-and-package-management"></a>Version de Python et gestion des packages

Actuellement, Azure Functions ne prend en charge que Python 3.6.x (distribution officielle de CPython).

Si vous développez en local avec Azure Functions Core Tools ou Visual Studio Code, ajoutez les noms et les versions des packages requis au fichier `requirements.txt` et installez-les avec `pip`.

Par exemple, le fichier de configuration requise suivant et la commande pip permettent d’installer le package `requests` à partir de PyPI.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Avant de procéder à la publication, vérifiez que toutes les dépendances sont répertoriées dans le fichier `requirements.txt`, situé à la racine de votre répertoire de projet. Pour qu’Azure Functions s’exécute correctement, le fichier de configuration requise doit comporter au moins les packages suivants :

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Publier sur Azure

Si un compilateur est nécessaire pour le package utilisé, et que celui-ci ne prend pas en charge l’installation de wheels compatibles manylinux de PyPI, la publication sur Azure échoue avec l’erreur suivante : 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Pour générer et configurer automatiquement les binaires nécessaires, [installez Docker](https://docs.docker.com/install/) sur votre ordinateur local et exécutez la commande suivante afin d’utiliser [Azure Functions Core Tools](functions-run-local.md#v2) (func) pour la publication. Veillez à remplacer `<app name>` par le nom de votre application de fonction dans Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

En coulisses, Core Tools utilise Docker pour exécuter l’image [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) comme conteneur sur l’ordinateur local. Avec cet environnement, il génère alors les modules requis et les installe à partir de la distribution source, avant de les packager pour le déploiement final sur Azure.

> [!NOTE]
> Core Tools (func) utilise le programme de PyInstaller pour figer le code de l’utilisateur et les dépendances en un seul exécutable autonome dans Azure. Cette fonctionnalité, actuellement en préversion, ne couvre pas forcément tous les types de packages Python. Si vous ne parvenez pas à importer vos modules, essayez de les republier avec l’option `--no-bundler`. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Si les problèmes persistent, faites-le nous savoir en [ouvrant un problème](https://github.com/Azure/azure-functions-core-tools/issues/new) et en ajoutant sa description. 


Pour générer vos dépendances et effectuer la publication avec un système d’intégration continue (CI) et de livraison continue (CD), vous pouvez utiliser [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) ou un [script personnalisé d’intégration continue Travis](https://docs.travis-ci.com/user/deployment/script/). 

Voici un exemple de script `azure-pipelines.yml` pour le processus de build et de publication.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Voici un exemple de script `.travis.yaml` pour le processus de build et de publication.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

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
