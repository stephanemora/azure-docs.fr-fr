---
title: 'Démarrage rapide : Créer une application de fonction sur Azure Arc'
description: Démarrez avec Azure Functions sur Azure Arc en déployant votre première application de fonction.
ms.topic: quickstart
ms.date: 05/10/2021
ms.openlocfilehash: 8e65eeaae942eafa34a89054ff06951f2bde8741
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371856"
---
# <a name="create-your-first-function-on-azure-arc-preview"></a>Créer votre première fonction sur Azure Arc (préversion)

Dans ce guide de démarrage rapide, vous créez un projet Azure Functions et vous le déployez dans une application de fonction qui s’exécute sur un [cluster Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md). Pour plus d’informations, consultez [App Service, Functions et Logic Apps sur Azure Arc](../app-service/overview-arc-integration.md). Ce scénario prend en charge uniquement les applications de fonction s’exécutant sur Linux.   

> [!NOTE]
> La prise en charge de l’exécution de fonctions sur un cluster Kubernetes avec Azure Arc est actuellement disponible en préversion.  
>  
> La publication de projets de fonction PowerShell sur des clusters Kubernetes avec Azure Arc n’est pas prise en charge actuellement. Si vous devez déployer des fonctions PowerShell sur des clusters Kubernetes avec Azure Arc, [créez votre application de fonction dans un conteneur](create-first-function-arc-custom-container.md). 

## <a name="prerequisites"></a>Prérequis

Sur votre ordinateur local :

# <a name="c"></a>[C\#](#tab/csharp)

+ [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.
+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/) version 12. La version 10 de Node.js est également prise en charge.
+ [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.
+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.

# <a name="python"></a>[Python](#tab/python)

+ [Versions de Python prises en charge par Azure Functions](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.
+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>Créer le projet de fonction local

Dans Azure Functions, un projet de fonction est l’unité de déploiement et d’exécution pour une ou plusieurs fonctions qui répondent chacune à un déclencheur spécifique. Toutes les fonctions d’un projet partagent les mêmes configurations locale et d’hébergement. Dans cette section, vous créez un projet de fonction qui contient une seule fonction.

1. Exécutez la commande `func init`, de la façon suivante, pour créer un projet Functions dans un dossier nommé *LocalFunctionProj* avec le runtime spécifié :  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript
    ```

    # <a name="python"></a>[Python](#tab/python)

    Python requiert un environnement virtuel ; les commandes à utiliser diffèrent entre Bash et la ligne de commande Windows.
    
     + bash : 

        ```bash
        python -m venv .venv
        source .venv/bin/activate
        ```
    
     + ligne de commande :

        ```cmd
        py -m venv .venv
        .venv\scripts\activate
        ```  

    À présent, créez le projet dans l’environnement virtuel. 
    
    ```console
    func init LocalFunctionProj --python
    ```

    ---

1. Accédez au dossier du projet :

    ```console
    cd LocalFunctionProj
    ```

    Ce dossier contient divers fichiers pour le projet, notamment des fichiers config nommés [local.settings.json](functions-run-local.md#local-settings-file) et [host.json](functions-host-json.md). Par défaut, le fichier *local.settings.json* est exclu du contrôle de code source dans le fichier *.gitignore*. Cette exclusion est due au fait que le fichier peut contenir des secrets téléchargés à partir d’Azure.

1. Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction (HttpExample) et où l’argument `--template` spécifie le déclencheur de la fonction (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  
[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>Créer des ressources Azure 

Avant de pouvoir déployer votre code de fonction dans votre nouvel environnement App Service Kubernetes, vous devez créer deux ressources supplémentaires :

- Un [compte de stockage](../storage/common/storage-account-create.md), qui est actuellement requis par les outils, mais ne fait pas partie de l’environnement.
- Une application de fonction, qui fournit le contexte d’exécution de votre code de fonction. L’application de fonction s’exécute dans l’environnement App Service Kubernetes et est mappée à votre projet de fonction local. Une application de fonction permet de regrouper des fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage de ressources.

> [!NOTE]
> Les applications de fonction s’exécutent dans un environnement App Service Kubernetes dans le cadre d’un plan Dedicated (App Service). Si vous créez votre application de fonction sans plan existant, le plan approprié est créé automatiquement.  

### <a name="create-storage-account"></a>Créer un compte de stockage

Utilisez la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) pour créer un compte de stockage universel dans votre groupe de ressources et votre région :

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

> [!NOTE]  
> Un compte de stockage est actuellement requis par les outils Azure Functions. 

Dans l’exemple précédent, remplacez `<STORAGE_NAME>` par un nom qui vous convient et qui est unique dans Stockage Azure. Les noms doivent contenir entre 3 et 24 caractères, et comporter uniquement des lettres minuscules. `Standard_LRS` spécifie un compte universel, qui est [pris en charge par Functions](storage-considerations.md#storage-account-requirements). La valeur `--location` est une région Azure standard. 

### <a name="create-the-function-app"></a>Créer l’application de fonction

Exécutez la commande [az functionapp create](/cli/azure/functionapp#az_functionapp_create) pour créer une application de fonction dans l’environnement.

# <a name="c"></a>[C\#](#tab/csharp)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet 
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8
```
---

Dans cet exemple, remplacez `<CUSTOM_LOCATION_ID>` par l’ID de l’emplacement personnalisé que vous avez déterminé pour l’environnement App Service Kubernetes. De plus, remplacez `<STORAGE_NAME>` par le nom du compte que vous avez utilisé à l’étape précédente, puis remplacez `<APP_NAME>` par le nom global unique approprié dans votre cas. 

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

Dans la mesure où l’exécution d’un déploiement complet sur un cluster Kubernetes avec Azure Arc peut prendre du temps, vous pouvez réexécuter la commande suivante pour vérifier vos fonctions publiées :

```command
func azure functionapp list-functions
``` 

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre application de fonction s’exécute dans un conteneur dans un environnement App Service Kubernetes avec Arc, vous pouvez la connecter au service Stockage Azure en ajoutant une liaison de sortie Stockage File d’attente.

# <a name="c"></a>[C\#](#tab/csharp)  

> [!div class="nextstepaction"]
> [Se connecter à une file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  

> [!div class="nextstepaction"]
> [Se connecter à une file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)

# <a name="python"></a>[Python](#tab/python)  

> [!div class="nextstepaction"]
> [Se connecter à une file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

---