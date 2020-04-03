---
title: Créer une fonction dans Azure qui répond à des requêtes HTTP
description: Découvrez comment créer une fonction à partir de la ligne de commande, puis comment publier le projet local sur un hébergement serverless dans Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 89b6a9f31414cbaa9cc92c1a0d881a1354180990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282730"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Démarrage rapide : Créer une fonction dans Azure qui répond à des requêtes HTTP

Dans cet article, vous utilisez des outils en ligne de commande pour créer une fonction qui répond à des requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. Le fait de suivre ce guide de démarrage rapide entraîne une faible dépense de quelques cents USD tout au plus dans votre compte Azure.

Si vous souhaitez consulter une version de cet article adaptée à Visual Studio Code, [cliquez ici](functions-create-first-function-vs-code.md).

## <a name="configure-your-local-environment"></a>Configurer votre environnement local

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1846 ou version 2.x ultérieure.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 et 3.7 nécessitent [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1846 (ou une version 2.x ultérieure). Python 3.8 nécessite une [version 3.x](./functions-run-local.md#v2) des outils Core Tools.
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.0.76 ou ultérieure. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS et Maintenance LTS (versions 8.11.1 et 10.14.1 recommandées).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-382/), [Python 3.7](https://www.python.org/downloads/release/python-375/) et [Python 3.6](https://www.python.org/downloads/release/python-368/), qui sont pris en charge par Azure Functions (x64).
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Kit SDK .NET Core 2.2+](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Vérifier votre environnement

+ Dans une fenêtre de terminal ou une fenêtre Commande, exécutez `func --version` pour vérifier que vous disposez d’Azure Functions Core Tools version 2.7.1846 ou version 2.x ultérieure.

+ Exécutez `az --version` pour vérifier que vous disposez d’Azure CLI version 2.0.76 ou ultérieure.

+ Exécutez `az login` pour vous connecter à Azure et vérifier que l’abonnement est actif.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Exécutez `node --version` pour vérifier que la version de Node.js est 8. x ou 10.x.
::: zone-end
::: zone pivot="programming-language-python"
+ Exécutez `python --version` (Linux/macOS) ou `py --version` (Windows) pour vérifier que vous disposez de Python version 3.8.x, 3.7.x ou 3.6.x.

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Créer et activer un environnement virtuel

Dans le dossier approprié, exécutez les commandes suivantes pour créer et activer un environnement virtuel nommé `.venv`. Veillez à utiliser Python 3.8, 3.7 ou 3.6, qui sont pris en charge par Azure Functions.


# <a name="bash"></a>[bash](#tab/bash)

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Vous devez exécuter toutes les commandes suivantes dans cet environnement virtuel activé. (Pour quitter l’environnement virtuel, exécutez `deactivate`.)

::: zone-end

## <a name="create-a-local-function-project"></a>Créer un projet de fonction local

Dans Azure Functions, un projet de fonction est un conteneur pour une ou plusieurs fonctions qui répondent chacune à un déclencheur spécifique. Toutes les fonctions d’un projet partagent les mêmes configurations locale et d’hébergement. Dans cette section, vous créez un projet de fonction qui contient une seule fonction.

1. Dans l’environnement virtuel, exécutez la commande `func init` pour créer un projet Functions dans un dossier nommé *LocalFunctionProj* avec le runtime spécifié :

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionProj --python
    ```
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionProj --dotnet
    ```
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionProj --javascript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionProj --typescript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionProj --powershell
    ```
    ::: zone-end


    Ce dossier contient divers fichiers pour le projet, notamment des fichiers config nommés [local.settings.json](functions-run-local.md#local-settings-file) et [host.json](functions-host-json.md). Dans la mesure où *local.settings.json* peut contenir des secrets téléchargés à partir d’Azure, le fichier est exclu du contrôle de code source par défaut dans le fichier *.gitignore*.

1. Accédez au dossier du projet :

    ```
    cd LocalFunctionProj
    ```
    
1. Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction, et l’argument `--template` spécifie le déclencheur de la fonction. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` crée un fichier de code HttpExample.cs.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` crée un sous-dossier correspondant au nom de la fonction qui contient le fichier de code approprié au langage choisi pour le projet, et un fichier config nommé *function.json*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(Facultatif) Examiner le contenu du fichier

Si vous le souhaitez, vous pouvez passer à [Exécuter la fonction localement](#run-the-function-locally) et examiner le contenu du fichier plus tard.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* contient une méthode `Run` qui reçoit des données de requête dans la variable `req` de type [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest), décorée avec **HttpTriggerAttribute** qui définit le comportement du déclencheur. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

L’objet retourné est un [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) qui retourne un message de réponse sous la forme d’un [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou d’un [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* contient une fonction Python `main()`, qui se déclenche selon la configuration définie dans *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Pour un déclencheur HTTP, la fonction reçoit les données de requête dans la variable `req`, tel que cela est défini dans *function.json*. `req` est une instance de la [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). L’objet retourné, défini comme `$return` dans *function.json*, est une instance de la [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporte une fonction qui est déclenchée selon la configuration définie dans *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Pour un déclencheur HTTP, la fonction reçoit les données de requête dans la variable `req`, tel que cela est défini dans *function.json*. L’objet retourné, défini comme `$return` dans *function.json*, est la réponse. Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exporte une fonction qui est déclenchée selon la configuration définie dans *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Pour un déclencheur HTTP, la fonction reçoit les données de requête dans la variable `req` de type **HttpRequest**, tel que cela est défini dans *function.json*. L’objet retourné, défini comme `$return` dans *function.json*, est la réponse. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* définit un script de fonction qui est déclenché selon la configuration définie dans *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Pour un déclencheur HTTP, la fonction reçoit les données de requête passées au paramètre `$Request`, tel que cela est défini dans *function.json*. L’objet retourné, défini comme `Response` dans *function.json*, est passé à l’applet de commande `Push-OutputBinding` comme réponse. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* est un fichier config qui définit le `bindings` d’entrée et de sortie de la fonction, notamment le type du déclencheur. 
::: zone-end

::: zone pivot="programming-language-python"
Vous pouvez changer `scriptFile` pour appeler un autre fichier Python, si vous le souhaitez.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Chaque liaison nécessite une direction, un type et un nom unique. Le déclencheur HTTP comporte une liaison d’entrée de type [`httpTrigger`](functions-bindings-http-webhook-trigger.md) et une liaison de sortie de type [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Créer des ressources Azure de prise en charge pour votre fonction

Avant de déployer le code de votre fonction dans Azure, vous devez créer trois ressources :

- Un groupe de ressources, qui est un conteneur logique pour les ressources associées.
- Un compte de stockage, qui conserve l’état et d’autres informations spécifiques à vos projets.
- Une application de fonction, qui fournit l’environnement d’exécution de votre code de fonction. Une application de fonction est mappée à votre projet de fonction local. Elle vous permet de regrouper les fonctions en tant qu’unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

Utilisez les commandes Azure CLI suivantes pour créer ces éléments. Chaque commande fournit une sortie JSON à la fin de son exécution.

1. Si vous ne l’avez pas déjà fait, connectez-vous à Azure avec la commande [az login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources nommé `AzureFunctionsQuickstart-rg` dans la région `westeurope`. (Vous créez généralement votre groupe de ressources et vos ressources dans une région près de chez vous, en utilisant une région disponible à partir de la commande `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Vous ne pouvez pas héberger des applications Linux et Windows dans le même groupe de ressources. Si vous disposez d’un groupe de ressources existant nommé `AzureFunctionsQuickstart-rg` avec une application de fonction Windows ou une application web, vous devez utiliser un autre groupe de ressources.
    ::: zone-end  
    
1. Créez un compte de stockage universel dans votre groupe de ressources et votre région à l’aide de la commande [az storage account create](/cli/azure/storage/account#az-storage-account-create). Dans l’exemple suivant, remplacez `<STORAGE_NAME>` par un nom globalement unique qui vous convient. Les noms doivent contenir entre 3 et 24 caractères, et comporter uniquement des lettres minuscules. `Standard_LRS` spécifie un compte universel, qui est [pris en charge par Functions](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Le compte de stockage n’engendre que quelques centimes (USD) de dépense dans ce guide de démarrage rapide.
    
1. Créez l’application de fonction à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Dans l’exemple suivant, remplacez `<STORAGE_NAME>` par le nom du compte que vous avez utilisé à l’étape précédente, puis remplacez `<APP_NAME>` par le nom global unique qui vous convient. `<APP_NAME>` représente également le domaine DNS par défaut pour l’application de fonction. 

    ::: zone pivot="programming-language-python"  
    Si vous utilisez Python 3.8, remplacez `--runtime-version` par `3.8` et `--functions_version` par `3`.
    
    Si vous utilisez Python 3.6, remplacez `--runtime-version` par `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Si vous utilisez Node.js 8, remplacez aussi `--runtime-version` par `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Cette commande crée une application de fonction qui s’exécute dans votre runtime de langage spécifié dans le [Plan Consommation Azure Functions](functions-scale.md#consumption-plan), qui est gratuit pour l’utilisation faite ici. La commande provisionne également une instance d’Azure Application Insights associée dans le même groupe de ressources, avec laquelle vous pouvez superviser votre application de fonction et visualiser les journaux. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). L’instance n’entraîne aucun coût tant que vous ne l’activez pas.
    
## <a name="deploy-the-function-project-to-azure"></a>Déployer le projet de fonction sur Azure

::: zone pivot="programming-language-typescript"  
Avant d’utiliser Core Tools pour déployer votre projet sur Azure, vous créez une build prête pour la production de fichiers JavaScript à partir des fichiers sources TypeScript.

La commande suivante prépare votre projet TypeScript pour le déploiement :

```
npm run build:production 
```
::: zone-end  

Une fois les ressources nécessaires en place, vous êtes prêt à déployer votre projet Functions local sur l’application de fonction dans Azure à l’aide de la commande [func azure functionapp publish](functions-run-local.md#project-file-deployment). Dans l’exemple suivant, remplacez `<APP_NAME>` par le nom de votre application.

```
func azure functionapp publish <APP_NAME>
```

Si vous voyez l’erreur « Impossible de localiser l’application nommée... », attendez quelques secondes, puis réessayez, car Azure n’a peut-être pas complètement initialisé l’application après la commande `az functionapp create` précédente.

La commande de publication affiche des résultats similaires à la sortie suivante (tronquée par souci de simplicité) :

<pre>
...

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
</pre>

## <a name="invoke-the-function-on-azure"></a>Appeler la fonction sur Azure

Dans la mesure où votre fonction utilise un déclencheur HTTP, vous l’appelez en adressant une requête HTTP à son URL dans le navigateur ou à l’aide d’un outil semblable à curl. Dans les deux cas, le paramètre d’URL `code` est votre [clé de fonction](functions-bindings-http-webhook-trigger.md#authorization-keys) unique qui autorise l’appel de votre point de terminaison de fonction.

# <a name="browser"></a>[Browser](#tab/browser)

Copiez l’**URL d’appel** complète affichée au sein de la sortie de la commande de publication dans la barre d’adresse d’un navigateur, en ajoutant le paramètre de requête `&name=Functions`. Le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

![Sortie de la fonction exécutée sur Azure dans un navigateur](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[curl](#tab/curl)

Exécutez [`curl`](https://curl.haxx.se/) avec l’**URL d’appel**, en ajoutant le paramètre `&name=Functions`. La sortie de la commande doit correspondre au texte « Hello Functions ».

![Sortie de la fonction exécutée sur Azure à l’aide de curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Pour voir les journaux en quasi temps réel d’une application de fonction publiée, utilisez le [flux de métriques temps réel Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez à l’étape suivante, [Ajouter une liaison de sortie de file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md), gardez toutes vos ressources en place, car vous allez tirer parti de ce que vous avez déjà fait.

Sinon, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter à une file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md)
