---
title: Créer une fonction Python déclenchée par HTTP dans Azure
description: Créez et déployez du code Python serverless dans le cloud à l’aide d’Azure Functions.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 452c5aeab5d2a1092cb7d338d37e26a82d92396e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845496"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Démarrage rapide : Créer une fonction Python déclenchée par HTTP dans Azure

Dans cet article, vous utilisez des outils en ligne de commande pour créer une fonction Python qui répond à des requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. Le fait de suivre ce guide de démarrage rapide entraîne une faible dépense de quelques cents USD tout au plus dans votre compte Azure.

Si vous souhaitez consulter une version de cet article adaptée à Visual Studio Code, [cliquez ici](/azure/python/tutorial-vs-code-serverless-python-01).

## <a name="prerequisites"></a>Conditions préalables requises

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1846 ou ultérieure.
- [Azure CLI](/cli/azure/install-azure-cli) version 2.0.76 ou ultérieure. 
- [Python 3.7.4 - 64 bits](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 est pris en charge par Azure Functions ; Python 3.8 et les versions ultérieures ne sont pas encore prises en charge.)

### <a name="prerequisite-check"></a>Vérification du prérequis

1. Dans une fenêtre de terminal ou une fenêtre Commande, exécutez `func --version` pour vérifier que vous disposez d’Azure Functions Core Tools version 2.7.1846 ou ultérieure.
1. Exécutez `az --version` pour vérifier que vous disposez d’Azure CLI version 2.0.76 ou ultérieure.
1. Exécutez `az login` pour vous connecter à Azure et vérifier que l’abonnement est actif.
1. Exécutez `python --version` (Linux/macOS) ou `py --version` (Windows) pour vérifier que vous disposez de Python version 3.7.x.

## <a name="create-and-activate-a-virtual-environment"></a>Créer et activer un environnement virtuel

Dans le dossier approprié, exécutez les commandes suivantes pour créer et activer un environnement virtuel nommé `.venv`. Veillez à utiliser Python 3.7, qui est pris en charge par Azure Functions.


# <a name="bashtabbash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Si Python n’a pas installé le package venv sur votre distribution Linux, exécutez la commande suivante :

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Vous devez exécuter toutes les commandes suivantes dans cet environnement virtuel activé. (Pour quitter l’environnement virtuel, exécutez `deactivate`.)

## <a name="create-a-local-function-project"></a>Créer un projet de fonction local

Dans Azure Functions, un projet de fonction est un conteneur pour une ou plusieurs fonctions qui répondent chacune à un déclencheur spécifique. Toutes les fonctions d’un projet partagent les mêmes configurations locale et d’hébergement. Dans cette section, vous créez un projet de fonction qui contient une seule fonction.

1. Dans l’environnement virtuel, exécutez la commande `func init` pour créer un projet Functions dans un dossier nommé *LocalFunctionProj* avec le runtime spécifié :

    ```
    func init LocalFunctionProj --python
    ```
    
    Ce dossier contient divers fichiers pour le projet, notamment des fichiers config nommés [local.settings.json](functions-run-local.md#local-settings-file) et [host.json](functions-host-json.md). Dans la mesure où *local.settings.json* peut contenir des secrets téléchargés à partir d’Azure, le fichier est exclu du contrôle de code source par défaut dans le fichier *.gitignore*.

    > [!TIP]
    > Dans la mesure où un projet de fonction est lié à un runtime spécifique, toutes les fonctions du projet doivent être écrites dans le même langage.

1. Accédez au dossier du projet :

    ```
    cd LocalFunctionProj
    ```
    
1. Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction, et l’argument `--template` spécifie le déclencheur de la fonction. `func new` crée un sous-dossier correspondant au nom de la fonction qui contient le fichier de code approprié au langage choisi pour le projet, et un fichier config nommé *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>(Facultatif) Examiner le contenu du fichier

Si vous le souhaitez, vous pouvez passer à [Exécuter la fonction localement](#run-the-function-locally) et examiner le contenu du fichier plus tard.

### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* contient une fonction Python `main()`, qui se déclenche selon la configuration définie dans *function.json*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
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
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Pour le déclencheur HTTP, la fonction reçoit les données de requête dans la variable `req`, tel que cela est défini dans *function.json*. `req` est une instance de la [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). L’objet retourné, défini comme `$return` dans *function.json*, est une instance de la [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](functions-bindings-http-webhook.md).

### <a name="functionjson"></a>function.json

*function.json* est un fichier config qui définit le `bindings` d’entrée et de sortie de la fonction, notamment le type du déclencheur. Vous pouvez changer `scriptFile` pour appeler un autre fichier Python, si vous le souhaitez.

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

Chaque liaison nécessite une direction, un type et un nom unique. Le déclencheur HTTP comporte une liaison d’entrée de type [`httpTrigger`](functions-bindings-http-webhook.md#trigger) et une liaison de sortie de type [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Démarrez la fonction en démarrant l’hôte du runtime d’Azure Functions local dans le dossier *LocalFunctionProj* :

```
func start
```

La sortie suivante doit s’afficher. (Si HttpExample n’apparaît pas comme indiqué ci-dessous, cela signifie probablement que vous avez démarré l’hôte à partir du dossier *HttpExample*. Dans ce cas, utilisez **Ctrl**+**C** pour arrêter l’hôte, accédez au dossier *LocalFunctionProj* parent, puis réexécutez `func start`.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Copiez dans un navigateur l’URL de votre fonction `HttpExample` à partir de cette sortie, puis ajoutez la chaîne de requête `?name=<your-name>` pour obtenir une URL complète semblable à `http://localhost:7071/api/HttpExample?name=Functions`. Le navigateur doit afficher un message similaire à `Hello Functions` :

![Résultat de la fonction exécutée localement dans le navigateur](./media/functions-create-first-function-python/function-test-local-browser.png)

Le terminal dans lequel vous avez exécuté `func start` affiche également une sortie de journal quand vous effectuez des requêtes.

Quand vous êtes prêt, appuyez sur **Ctrl**+**C** pour arrêter l’hôte Functions.

## <a name="create-supporting-azure-resources-for-your-function"></a>Créer des ressources Azure de prise en charge pour votre fonction

Pour déployer votre code de fonction sur Azure, vous devez créer trois ressources :

- Un groupe de ressources, qui est un conteneur logique pour les ressources associées.
- Un compte de stockage Azure, qui conserve l’état et d’autres informations spécifiques à vos projets.
- Une application Azure Functions, qui fournit l’environnement d’exécution de votre code de fonction. Une application de fonction est mappée à votre projet de fonction local. Elle vous permet de regrouper les fonctions en tant qu’unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

Vous utilisez les commandes Azure CLI pour créer ces éléments. Chaque commande fournit une sortie JSON à la fin de son exécution.

1. Si vous ne l’avez pas déjà fait, connectez-vous à Azure avec la commande [az login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources nommé `AzureFunctionsQuickstart-rg` dans la région `westeurope`. (Vous créez généralement votre groupe de ressources et vos ressources dans une région près de chez vous, en utilisant une région disponible à partir de la commande `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Vous ne pouvez pas héberger des applications Linux et Windows dans le même groupe de ressources. Si vous disposez d’un groupe de ressources existant nommé `AzureFunctionsQuickstart-rg` avec une application de fonction Windows ou une application web, vous devez utiliser un autre groupe de ressources.
    
1. Créez un compte de stockage universel dans votre groupe de ressources et votre région à l’aide de la commande [az storage account create](/cli/azure/storage/account#az-storage-account-create). Dans l’exemple suivant, remplacez `<storage_name>` par le nom global unique qui vous convient. Les noms doivent contenir entre 3 et 24 caractères, et comporter uniquement des lettres minuscules. `Standard_LRS` spécifie un compte universel classique.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Le compte de stockage n’engendre que quelques cents USD de dépense dans ce guide de démarrage rapide.
    
1. Créez l’application Functions à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Dans l’exemple suivant, remplacez `<storage_name>` par le nom du compte que vous avez utilisé à l’étape précédente, puis remplacez `<app_name>` par le nom global unique qui vous convient. `<app_name>` représente également le domaine DNS par défaut pour l’application de fonction.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Cette commande crée une application de fonction exécutant le runtime de langage spécifié dans le [Plan Consommation Azure Functions](functions-scale.md#consumption-plan), qui est gratuit pour l’utilisation faite ici. La commande provisionne également une instance d’Azure Application Insights associée dans le même groupe de ressources, avec laquelle vous pouvez superviser votre application de fonction et visualiser les journaux. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). L’instance n’entraîne aucun coût tant que vous ne l’activez pas.
    
## <a name="deploy-the-function-project-to-azure"></a>Déployer le projet de fonction sur Azure

Une fois les ressources nécessaires en place, vous êtes prêt à déployer votre projet Functions local sur l’application de fonction dans Azure à l’aide de la commande [func azure functionapp publish](functions-run-local.md#project-file-deployment). Dans l’exemple suivant, remplacez `<app_name>` par le nom de votre application.

```
func azure functionapp publish <app_name>
```

Si vous voyez l’erreur « Impossible de localiser l’application nommée... », attendez quelques secondes, puis réessayez, car Azure n’a peut-être pas complètement initialisé l’application après la commande `az functionapp create` précédente.

La commande de publication affiche des résultats similaires à la sortie suivante (tronquée par souci de simplicité) :

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>Appeler la fonction sur Azure

Dans la mesure où votre fonction utilise un déclencheur HTTP, vous l’appelez en adressant une requête HTTP à son URL dans le navigateur ou à l’aide d’un outil semblable à curl. Dans les deux cas, le paramètre d’URL `code` est votre clé de fonction unique qui autorise l’appel avec votre point de terminaison de fonction.

# <a name="browsertabbrowser"></a>[Browser](#tab/browser)

Copiez l’**URL d’appel** complète affichée au sein de la sortie de la commande de publication dans la barre d’adresse d’un navigateur, en ajoutant le paramètre de requête `&name=Azure`. Le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

![Sortie de la fonction exécutée sur Azure dans un navigateur](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[curl](#tab/curl)

Exécutez [curl](https://curl.haxx.se/) avec l’**URL d’appel**, en ajoutant le paramètre `&name=Azure`. La sortie de la commande doit correspondre au texte « Hello Azure ».

![Sortie de la fonction exécutée sur Azure à l’aide de curl](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Pour voir les journaux en quasi temps réel d’une application Python publiée, utilisez le [flux de métriques temps réel Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez à l’étape suivante, [Ajouter une liaison de sortie de file d’attente Stockage Azure](functions-add-output-binding-storage-queue-python.md), gardez toutes vos ressources en place, car vous allez tirer parti de ce que vous avez déjà fait.

Sinon, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une liaison de sortie de file d’attente Stockage Azure](functions-add-output-binding-storage-queue-python.md)
