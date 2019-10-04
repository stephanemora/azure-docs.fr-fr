---
title: Ajouter une liaison de file d’attente Stockage Azure à votre fonction Python
description: Découvrez comment ajouter une liaison de sortie de file d’attente Stockage Azure à votre fonction Python en utilisant Azure CLI et Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 9fdbf3466256c5e24de17541770fa2095fcf38a4
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171083"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Ajouter une liaison de file d’attente Stockage Azure à votre fonction Python

Azure Functions vous permet de connecter des services Azure et d’autres ressources à des fonctions sans avoir à écrire votre propre code d’intégration. Ces *liaisons*, qui représentent l’entrée et la sortie, sont déclarées dans la définition de la fonction. Les données issues des liaisons sont fournies à la fonction en tant que paramètres. Un *déclencheur* est un type spécial de liaison d’entrée. Si une fonction ne peut avoir qu’un seul déclencheur, elle peut avoir plusieurs liaisons d’entrée et de sortie. Pour en savoir plus, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

Cet article vous montre comment intégrer la fonction que vous avez créée dans l’[article du guide de démarrage rapide précédent](functions-create-first-function-python.md) à une file d’attente Stockage Azure. La liaison de sortie que vous ajoutez à cette fonction écrit des données d’une requête HTTP dans un message en file d’attente.

La plupart des liaisons requièrent une chaîne de connexion stockée que Functions utilise pour accéder au service lié. Pour faciliter cette connexion, vous utilisez le compte de stockage que vous avez créé avec votre application de fonction. La connexion à ce compte est déjà stockée dans un paramètre d’application nommé `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Prérequis

Avant de commencer cet article, suivez les étapes de la [partie 1 du guide de démarrage rapide Python](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Télécharger les paramètres de l’application de fonction

Dans l’article du guide de démarrage rapide précédent, vous avez créé une application de fonction dans Azure ainsi que le compte de stockage nécessaire. La chaîne de connexion pour ce compte est stockée de manière sécurisée dans les paramètres d’application au sein d’Azure. Dans cet article, vous allez écrire des messages dans une file d’attente de stockage au sein du même compte. Pour vous connecter à votre compte de stockage lors de l’exécution de la fonction localement, vous devez télécharger les paramètres de l’application dans le fichier local.settings.json. Exécutez la commande Azure Functions Core Tools suivante pour télécharger les paramètres dans le fichier local.settings.json, en remplaçant `<APP_NAME>` par le nom de votre application de fonction décrite dans l’article précédent :

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Il se peut que vous deviez vous connecter à votre compte Azure.

> [!IMPORTANT]  
> Comme il contient des secrets, le fichier local.settings.json n’est jamais publié et doit être exclu du contrôle de code source.

Vous avez besoin de la valeur `AzureWebJobsStorage`, qui est la chaîne de connexion de compte de stockage. Cette connexion vous permet de vérifier que la liaison de sortie fonctionne comme prévu.

## <a name="enable-extension-bundles"></a>Activer les offres groupées d’extension

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Vous pouvez maintenant ajouter la liaison de sortie de stockage à votre projet.

## <a name="add-an-output-binding"></a>Ajouter une liaison de sortie

Dans Functions, chaque type de liaison requiert la définition d’une `direction`, d’un `type` et d’un `name` unique dans le fichier function.json. Selon le type de liaison, des propriétés supplémentaires peuvent être requises. La [configuration de la sortie de la file d’attente](functions-bindings-storage-queue.md#output---configuration) décrit les champs requis pour une liaison de file d’attente Stockage Azure.

Pour créer une liaison, vous ajoutez un objet de configuration de liaison au fichier function.json. Modifiez le fichier function.json dans votre dossier HttpTrigger pour ajouter au tableau `bindings` un objet ayant les propriétés suivantes :

| Propriété | Valeur | Description |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Nom identifiant le paramètre de liaison référencé dans votre code. |
| **`type`** | `queue` | La liaison est une liaison de file d’attente Stockage Azure. |
| **`direction`** | `out` | La liaison est une liaison de sortie. |
| **`queueName`** | `outqueue` | Nom de la file d’attente dans laquelle écrit la liaison. Si la propriété `queueName` n’existe pas, la liaison la crée lors de la première utilisation. |
| **`connection`** | `AzureWebJobsStorage` | Nom d’un paramètre d’application qui contient la chaîne de connexion du compte de stockage. Le paramètre `AzureWebJobsStorage` contient la chaîne de connexion du compte de stockage que vous avez créé avec l’application de fonction. |

Votre fichier function.json doit maintenant ressembler à l’exemple suivant :

```json
{
  "scriptFile": "__init__.py",
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
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Ajouter le code qui utilise la liaison de sortie

Une fois la propriété `name` configurée, vous pouvez commencer à l’utiliser pour accéder à la liaison en tant qu’attribut de méthode dans la signature de la fonction. Dans l’exemple suivant, `msg` est une instance de la [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest).

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

Lorsque vous utilisez une liaison de sortie, vous n’avez pas besoin de recourir au code du Kit de développement logiciel (SDK) Stockage Azure pour l’authentification, l’obtention d’une référence de file d’attente ou l’écriture de données. La liaison de sortie de file d’attente et le runtime Functions effectuent ces tâches.

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Comme auparavant, utilisez la commande suivante pour démarrer le runtime Functions localement :

```bash
func host start
```

> [!NOTE]  
> Comme vous avez activé des offres groupées d’extension dans le fichier host.json dans le cadre du guide de démarrage rapide précédent, l’[extension de liaison de stockage](functions-bindings-storage-blob.md#packages---functions-2x) a été téléchargée et installée lors du démarrage, avec les autres extensions de liaison Microsoft.

Copiez l’URL de votre fonction `HttpTrigger` à partir de la sortie du runtime et collez-la dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. Vous devez voir la même réponse dans le navigateur que dans l’article précédent.

Cette fois, la liaison de sortie crée également une file d’attente nommée `outqueue` dans votre compte de stockage et ajoute un message avec cette même chaîne.

Ensuite, vous utilisez l’interface de ligne de commande Azure pour afficher la nouvelle file d’attente et vérifier qu’un message a été ajouté. Vous pouvez également afficher votre file d’attente en utilisant l’[Explorateur Stockage Azure][Azure Storage Explorer] ou sur le [portail Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Définir la connexion de compte de stockage

Ouvrez le fichier local.settings.json et copiez la valeur d’`AzureWebJobsStorage`, qui est la chaîne de connexion de compte de stockage. Définissez la variable d’environnement `AZURE_STORAGE_CONNECTION_STRING` sur la chaîne de connexion à l’aide de la commande Bash suivante :

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Lorsque vous définissez la chaîne de connexion dans la variable d’environnement `AZURE_STORAGE_CONNECTION_STRING`, vous pouvez accéder à votre compte de stockage sans devoir vous authentifier à chaque fois.

### <a name="query-the-storage-queue"></a>Interroger la file d’attente de stockage

Vous pouvez utiliser la commande [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) pour afficher les files d’attente de stockage dans votre compte, comme dans l’exemple suivant :

```azurecli-interactive
az storage queue list --output tsv
```

La sortie de cette commande comprend une file d’attente nommée `outqueue`, qui a été créée lors de l’exécution de la fonction.

Ensuite, utilisez la commande [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) pour afficher les messages dans cette file d’attente, comme dans l’exemple suivant :

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

La chaîne retournée doit être la même que le message que vous avez envoyé pour tester la fonction.

> [!NOTE]  
> L’exemple précédent décode la chaîne retournée à partir de base64. En effet, les liaisons Stockage File d’attente écrivent dans Stockage Azure et lisent à partir de ce dernier sous forme de [chaînes base64](functions-bindings-storage-queue.md#encoding).

À présent, il est temps de republier sur Azure l’application de fonction mise à jour.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Là encore, vous pouvez utiliser cURL ou un navigateur pour tester la fonction déployée. Comme précédemment, ajoutez la chaîne de requête `&name=<yourname>` à l’URL, comme dans l’exemple suivant :

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Vous pouvez [examiner le message de file d’attente de stockage](#query-the-storage-queue) pour vérifier que la liaison de sortie regénère un nouveau message dans la file d’attente.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez mis à jour votre fonction déclenchée via HTTP pour écrire des données dans une file d’attente de stockage. Pour en savoir plus sur le développement de fonctions Azure Functions avec Python, voir le [Guide des développeurs Python sur Azure Functions](functions-reference-python.md) et [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md). Pour obtenir des exemples de projets Functions complets en Python, consultez les [exemples Functions en Python](/samples/browse/?products=azure-functions&languages=python). 

Ensuite, vous devez activer la supervision Application Insights pour votre application de fonction :

> [!div class="nextstepaction"]
> [Activer l’intégration à Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/