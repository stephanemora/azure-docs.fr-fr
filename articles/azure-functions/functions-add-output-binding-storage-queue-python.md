---
title: Ajouter une liaison de file d’attente Stockage Azure à votre fonction Python
description: Intégrez une file d’attente Stockage Azure à une fonction Python à l’aide d’une liaison de sortie.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: f5527e0e636c3f8c9ee3723570ed9811f0df3641
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198477"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Ajouter une liaison de file d’attente Stockage Azure à votre fonction Python

Dans cet article, vous intégrez une file d’attente Stockage Azure à la fonction et au compte de stockage que vous avez créés dans [Créer une fonction Python déclenchée par HTTP](functions-create-first-function-python.md). Vous effectuez cette intégration à l’aide d’une *liaison de sortie* qui écrit les données d’une requête HTTP dans un message en file d’attente. Le fait de suivre les instructions de cet article n’entraîne aucun coût supplémentaire au-delà des quelques cents USD du guide de démarrage rapide précédent.

## <a name="prerequisites"></a>Prérequis

- Suivez le guide de démarrage rapide, [Créer une fonction Python déclenchée par HTTP](functions-create-first-function-python.md). Si vous avez déjà nettoyé les ressources à la fin de cet article, suivez à nouveau les étapes pour recréer l’application Functions dans Azure, mais laissez les ressources en place.

## <a name="retrieve-the-azure-storage-connection-string"></a>Récupérer la chaîne de connexion de Stockage Azure

Quand vous avez créé une application de fonction au sein d’Azure dans le guide de démarrage rapide précédent, vous avez également créé un compte de stockage. La chaîne de connexion pour ce compte est stockée de manière sécurisée dans les paramètres d’application au sein d’Azure. En téléchargeant le paramètre dans le fichier *local.settings.json*, vous pouvez utiliser cette connexion pour écrire dans une file d’attente de stockage du même compte au moment de l’exécution locale de la fonction. 

1. À partir de la racine du projet, exécutez la commande suivante, en remplaçant `<app_name>` par le nom de votre application de fonction créée dans le guide de démarrage rapide précédent. Cette commande remplace toutes les valeurs existantes dans le fichier.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Ouvrez *local.settings.json* et recherchez la valeur nommée `AzureWebJobsStorage`, qui est la chaîne de connexion du compte de stockage. Vous utilisez le nom `AzureWebJobsStorage` et la chaîne de connexion dans d’autres sections de cet article.

> [!IMPORTANT]
> Dans la mesure où *local.settings.json* contient des secrets téléchargés à partir d’Azure, excluez toujours ce fichier du contrôle de code source. Le fichier *.gitignore* créé avec un projet Functions local exclut le fichier par défaut.

## <a name="add-an-output-binding-to-functionjson"></a>Ajoutez une liaison de sortie à function.json

Bien qu’une fonction ne puisse avoir qu’un seul déclencheur, elle peut avoir plusieurs liaisons d’entrée et de sortie. Ainsi, vous pouvez vous connecter à d’autres services et ressources Azure sans avoir à écrire du code d’intégration personnalisé. Vous déclarez ces liaisons dans le fichier *function.json* de votre dossier de fonction selon le langage que vous utilisez pour la fonction.

Compte tenu du guide de démarrage rapide précédent, votre fichier *function.json* situé dans le dossier *HttpExample* contient deux liaisons dans la collection `bindings` :

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
    }
  ]
}
```

Chaque liaison a au moins un type, une direction et un nom. Dans l’exemple ci-dessus, la première liaison est de type `httpTrigger`, et sa direction est `in`. Pour la direction `in`, `name` spécifie le nom d’un paramètre d’entrée qui est envoyé à la fonction quand il est appelé par le déclencheur.

La deuxième liaison est de type `http`, et sa direction est `out`. Dans ce cas, le `name` spécial de `$return` indique que cette liaison utilise la valeur retournée par la fonction au lieu de fournir un paramètre d’entrée.

Pour écrire dans une file d’attente Stockage Azure à partir de cette fonction, ajoutez une liaison `out` de type `queue` nommée `msg`, comme indiqué dans le code ci-dessous :

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

Dans ce cas, `msg` est fourni à la fonction en tant qu’argument de sortie. Pour un type `queue`, vous devez également spécifier le nom de la file d’attente dans `queueName` et fournir le *nom* de la connexion Stockage Azure (à partir de *local.settings.json*) dans `connection`.

Pour plus d’informations sur les détails des liaisons, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md) et [Configuration de la sortie de la file d’attente](functions-bindings-storage-queue.md#output---configuration).

## <a name="add-code-to-use-the-output-binding"></a>Ajouter du code pour utiliser la liaison de sortie

Avec la liaison de file d’attente spécifiée dans *function.json*, vous pouvez à présent mettre à jour votre fonction pour recevoir le paramètre de sortie `msg` et écrire des messages dans la file d’attente.

Mettez à jour *HttpExample\\\_\_init\_\_.py* pour correspondre au code suivant, en ajoutant le paramètre `msg` à la définition de fonction et `msg.set(name)` sous l’instruction `if name:`.

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

Le paramètre `msg` est une instance de la [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Sa méthode `set` écrit un message de type chaîne dans la file d’attente. Dans le cas présent, il s’agit du nom passé à la fonction dans la chaîne de requête de l’URL.

Notez que vous *n’avez* pas besoin d’écrire du code pour l’authentification, l’obtention d’une référence de file d’attente ou l’écriture de données. Toutes ces tâches d’intégration sont prises en charge de manière pratique dans le runtime d’Azure Functions et la liaison de sortie de file d’attente.

## <a name="run-and-test-the-function-locally"></a>Exécuter et tester la fonction localement

1. À partir d’un terminal ou d’une invite de commandes, accédez au dossier de votre projet de fonction, *LocalFunctionProj*.

1. Démarrez l’hôte du runtime d’Azure Functions à l’aide de la commande suivante.

    ```
    func host start
    ```

1. Une fois que le démarrage est effectué et que vous voyez l’URL du point de terminaison `HttpExample`, copiez-la dans un navigateur, puis ajoutez la chaîne de requête `?name=<your-name>` pour obtenir une URL complète semblable à `http://localhost:7071/api/HttpExample?name=Guido`. Le navigateur doit afficher un message similaire à `Hello Guido`, comme dans l’article précédent.

    Si vous ne voyez pas le point de terminaison `HttpExample` s’afficher, arrêtez l’hôte avec **Ctrl**+**C**, puis recherchez la présence d’erreurs dans la sortie. Par exemple, l’hôte n’active pas le point de terminaison en cas d’erreurs dans *function.json*. Vérifiez également que vous exécutez `func host start` à partir du dossier de projet Functions et non du dossier *HttpExample*.

1. Une fois que vous avez fini, arrêtez l’hôte avec **Ctrl**+**C**.

> [!TIP]
> Au démarrage, l’hôte télécharge et installe l’[extension de liaison de stockage](functions-bindings-storage-blob.md#add-to-your-functions-app) et d’autres extensions de liaison Microsoft. Cette installation a lieu, car les extensions de liaison sont activées par défaut dans le fichier *host.json* avec les propriétés suivantes :
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Si vous rencontrez des erreurs liées aux extensions de liaison, vérifiez que les propriétés ci-dessus sont présentes dans *host.json*.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Voir le message dans la file d’attente Stockage Azure

Quand votre fonction génère une réponse HTTP pour le navigateur web, elle appelle également `msg.set(name)`, qui écrit un message dans une file d’attente Stockage Azure nommée `outqueue`, comme cela est spécifié dans la liaison de file d’attente. Vous pouvez voir la file d’attente dans le [portail Azure](../storage/queues/storage-quickstart-queues-portal.md) ou dans l’[Explorateur Stockage Microsoft Azure](https://storageexplorer.com/). Vous pouvez également voir la file d’attente dans Azure CLI, comme indiqué dans les étapes suivantes :

1. Ouvrez le fichier *local.setting.json* du projet de fonction, et copiez la valeur de la chaîne de connexion. Dans une fenêtre de terminal ou une fenêtre Commande, exécutez la commande suivante pour créer une variable d’environnement nommée `AZURE_STORAGE_CONNECTION_STRING`, en collant votre chaîne de connexion spécifique à la place de `<connection_string>`. (Cette variable d’environnement signifie que vous n’avez pas besoin de fournir la chaîne de connexion pour les prochaines commandes à l’aide de l’argument `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Facultatif) Utilisez la commande [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) pour voir les files d’attente de stockage dans votre compte. La sortie de cette commande doit inclure une file d’attente nommée `outqueue`, qui a été créée au moment où la fonction a écrit son premier message dans cette file d’attente.
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. Utilisez la commande [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) pour voir les messages de cette file d’attente, laquelle doit correspondre au premier nom que vous avez utilisé quand vous avez testé la fonction. La commande récupère le premier message de la file d’attente au format d’[encodage base64](functions-bindings-storage-queue.md#encoding). Vous devez donc également décoder le message pour le voir sous forme de texte.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Dans la mesure où vous devez déréférencer la collecte du message et son décodage à partir du format base64, exécutez PowerShell et utilisez la commande PowerShell.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Redéployer le projet sur Azure

Une fois que vous avez testé la fonction localement et vérifié qu’elle a écrit un message dans la file d’attente Stockage Azure, vous pouvez redéployer votre projet pour mettre à jour le point de terminaison en cours d’exécution sur Azure.

1. Dans le dossier *LocalFunctionsProj*, utilisez la commande [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) pour redéployer le projet, en remplaçant `<app_name>` par le nom de votre application.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Comme dans le guide de démarrage rapide précédent, utilisez un navigateur ou CURL pour tester la fonction redéployée.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Copiez l’**URL d’appel** complète affichée au sein de la sortie de la commande de publication dans la barre d’adresse d’un navigateur, en ajoutant le paramètre de requête `&name=Azure`. Le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

    ![Sortie de la fonction exécutée sur Azure dans un navigateur](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Exécutez [curl](https://curl.haxx.se/) avec l’**URL d’appel**, en ajoutant le paramètre `&name=Azure`. La sortie de la commande doit correspondre au texte « Hello Azure ».
    
    ![Sortie de la fonction exécutée sur Azure à l’aide de curl](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Examinez à nouveau la file d’attente de stockage, comme indiqué dans la section précédente, pour vérifier qu’elle contient le nouveau message écrit.

    Si vous utilisez Azure CLI pour examiner la file d’attente, la commande `az storage message peek` affiche uniquement le premier message qui s’y trouve. Pour simuler le traitement des messages, utilisez `az storage message get` à la place avec les mêmes arguments. La commande `get` retourne le message et le supprime de la file d’attente. Vous pouvez ensuite répéter la même commande jusqu’à ce que la file d’attente soit vide (et que la commande génère une erreur).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez à l’étape suivante, [Activer l’intégration à Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource), gardez toutes vos ressources en place, car vous allez tirer parti de ce que vous avez déjà fait.

Sinon, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Activer l’intégration à Application Insights avec une application Azure Functions](functions-monitoring.md#manually-connect-an-app-insights-resource)

Autres ressources :

- [Exemples de projets Functions complets en Python](/samples/browse/?products=azure-functions&languages=python)
- [Guide du développeur Python sur Azure Functions](functions-reference-python.md)
- [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)
- [Page de tarification de Functions](https://azure.microsoft.com/pricing/details/functions/)
- Article [Estimation des coûts d’un plan Consommation](functions-consumption-costs.md)
