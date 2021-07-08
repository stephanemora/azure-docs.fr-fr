---
title: 'Démarrage rapide : Créer une application de fonction sur Azure Arc dans un conteneur personnalisé'
description: Démarrez avec Azure Functions sur Azure Arc en déployant votre première application de fonction dans un conteneur personnalisé Linux.
ms.topic: quickstart
ms.date: 05/11/2021
ms.openlocfilehash: 28079b9a7821350736647d49815c541f6a79d119
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525639"
---
# <a name="create-your-first-function-on-azure-arc-using-a-custom-container-preview"></a>Créer votre première fonction sur Azure Arc dans un conteneur personnalisé (préversion)

Dans ce guide de démarrage rapide, vous créez un projet Azure Functions s’exécutant dans un conteneur personnalisé et vous le déployez sur un [cluster Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md) à partir de votre compte Docker Hub. Pour plus d’informations, consultez [App Service, Functions et Logic Apps sur Azure Arc](../app-service/overview-arc-integration.md). Ce scénario prend en charge uniquement les applications de fonction s’exécutant sur Linux.   

> [!NOTE]
> La prise en charge de l’exécution de fonctions sur un cluster Kubernetes avec Azure Arc est actuellement disponible en préversion.  

## <a name="prerequisites"></a>Prérequis

Sur votre ordinateur local :

# <a name="c"></a>[C\#](#tab/csharp)

+ [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2) (version 3.0.3245 ou ultérieure).
+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/) version 12. La version 10 de Node.js est également prise en charge.
+ [Azure Functions Core Tools](functions-run-local.md#v2) (version 3.0.3245 ou ultérieure).
+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

# <a name="python"></a>[Python](#tab/python)

+ [Versions de Python prises en charge par Azure Functions](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2) (version 3.0.3245 ou ultérieure).
+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>Créer le projet de fonction local

Dans Azure Functions, un projet de fonction est le contexte pour une ou plusieurs fonctions qui répondent chacune à un déclencheur spécifique. Toutes les fonctions d’un projet partagent les mêmes configurations locale et d’hébergement. Dans cette section, vous créez un projet de fonction qui contient une seule fonction.

1. Exécutez la commande `func init`, de la façon suivante, pour créer un projet Functions dans un dossier nommé *LocalFunctionProj* avec le runtime spécifié :  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet --docker
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript --docker
    ```

    # <a name="python"></a>[Python](#tab/python)

    Python requiert un environnement virtuel ; les commandes à utiliser diffèrent entre Bash et la ligne de commande Windows.
    
     + Bash : 

        ```bash
        python -m venv .venv
        source .venv/bin/activate
        ```
    
     + Ligne de commande :

        ```cmd
        py -m venv .venv
        .venv\scripts\activate
        ```  

    À présent, créez le projet dans l’environnement virtuel. 
    
    ```console
    func init LocalFunctionProj --python --docker
    ```
    ---

    L’option `--docker` génère un `Dockerfile` pour le projet, qui définit un conteneur personnalisé approprié pour une utilisation avec Azure Functions et le runtime sélectionné.

1. Accédez au dossier du projet :

    ```console
    cd LocalFunctionProj
    ```

    Ce dossier contient les autres fichiers Dockerfile du projet, notamment des fichiers de configuration nommés [local.settings.json](functions-run-local.md#local-settings-file) et [host.json](functions-host-json.md). Par défaut, le fichier *local.settings.json* est exclu du contrôle de code source dans le fichier *.gitignore*. Cette exclusion est due au fait que le fichier peut contenir des secrets téléchargés à partir d’Azure.

1. Ouvrez le `Dockerfile` généré et recherchez la balise `3.0` de l’image de base. S’il existe une balise `3.0`, remplacez-la par une balise `3.0.15885`. Dans une application JavaScript par exemple, le fichier Docker doit être modifié ainsi : `FROM mcr.microsoft.com/azure-functions/node:3.0.15885`. Cette version de l’image de base prend en charge le déploiement vers un cluster Kubernetes avec Azure Arc. 

1. Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction (HttpExample) et où l’argument `--template` spécifie le déclencheur de la fonction (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  

## <a name="build-the-container-image-and-test-locally"></a>Générer l’image conteneur et tester localement

Le fichier Dockerfile situé à la racine du projet décrit l’environnement minimal requis pour l’exécution de l’application de fonction dans un conteneur. La liste complète des images de base prises en charge pour Azure Functions se trouve sur la [pages des images de base Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

1. À la racine du dossier du projet, exécutez la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/), puis indiquez un nom (`azurefunctionsimage`) et une balise (`v1.0.0`).   

    La commande suivante génère l’image Docker pour le conteneur.

    ```console
    docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
    ```

    Dans cet exemple, remplacez `<DOCKER_ID>` par votre ID de compte Docker Hub. Une fois la commande terminée, vous pouvez exécuter le nouveau conteneur localement.
    
1. Pour tester la build, exécutez l’image dans un conteneur local avec la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/), en ajoutant l’argument pour les ports, `-p 8080:80`.

    ```console
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```

    Là encore, remplacez `<DOCKER_ID` par votre ID Docker, en ajoutant l’argument pour les ports, `-p 8080:80`

1. Après l’exécution de l’image dans un conteneur local, accédez à `http://localhost:8080/api/HttpExample?name=Functions`, qui doit afficher le même message « hello » que précédemment. Étant donné que la fonction HTTP déclenchée utilise une autorisation anonyme, vous pouvez toujours appeler la fonction même si elle est déjà exécutée dans le conteneur. Les paramètres de la clé d’accès à la fonction sont appliqués lors de l’exécution locale dans un conteneur. Si vous rencontrez des problèmes pour appeler la fonction, vérifiez que l’[accès à la fonction](functions-bindings-http-webhook-trigger.md#authorization-keys) est défini sur anonyme.  

Après avoir vérifié l’application de fonction dans le conteneur, arrêtez Docker avec **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Envoyer (push) l’image sur Docker Hub

Docker Hub est un registre de conteneurs qui héberge des images, et qui fournit des services d’image et de conteneur. Pour partager votre image, ce qui inclut un déploiement sur Azure, vous devez l’envoyer dans un registre.

1. Si vous ne vous êtes pas déjà connecté à Docker, utilisez la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/), en remplaçant `<docker_id>` par votre ID Docker. Cette commande vous invite à entrer votre nom d’utilisateur et votre mot de passe. Un message « Connexion réussie » confirme que vous êtes connecté.

    ```console
    docker login
    ```
    
1. Une fois connecté, envoyez l’image à Docker Hub en utilisant la commande [docker push](https://docs.docker.com/engine/reference/commandline/push/), en remplaçant à nouveau `<docker_id>` par votre ID Docker.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Selon la rapidité de votre réseau, le premier initial de l’image peut prendre quelques minutes (l’envoi des modifications suivantes est beaucoup plus rapide). Pendant que vous attendez, vous pouvez passer à la section suivante et créer des ressources Azure dans un autre terminal.

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>Créer des ressources Azure 

Avant de pouvoir déployer votre conteneur dans votre nouvel environnement App Service Kubernetes, vous devez créer deux ressources supplémentaires :

- Un [compte de stockage](../storage/common/storage-account-create.md), qui est actuellement requis par les outils, mais ne fait pas partie de l’environnement.
- Une application de fonction, qui fournit le contexte d’exécution de votre conteneur. L’application de fonction s’exécute dans l’environnement App Service Kubernetes et est mappée à votre projet de fonction local. Une application de fonction permet de regrouper des fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage de ressources.

> [!NOTE]
> Les applications de fonction s’exécutent dans un environnement App Service Kubernetes dans le cadre d’un plan Dedicated (App Service). Si vous créez votre application de fonction sans plan existant, un plan est créé automatiquement.  

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
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```
---

Dans cet exemple, remplacez `<CUSTOM_LOCATION_ID>` par l’ID de l’emplacement personnalisé que vous avez déterminé pour l’environnement App Service Kubernetes. Effectuez également ces trois remplacements : `<STORAGE_NAME>` par le nom du compte que vous avez utilisé à l’étape précédente, `<APP_NAME>` par le nom global unique approprié dans votre cas et `<DOCKER_ID>` par votre ID Docker Hub. 

Le paramètre *deployment-container-image-name* spécifie l’image à utiliser pour l’application de fonction. Vous pouvez utiliser la commande [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) pour voir des informations sur l’image utilisée pour le déploiement. Vous pouvez aussi utiliser la commande [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) pour déployer à partir d’une autre image.

Au moment où vous créez l’application de fonction, l’image initiale est tirée (pull) de Docker Hub. Vous pouvez également [activer le déploiement continu sur Azure](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) à partir de Docker Hub.  

Pour savoir comment activer SSH dans l’image, consultez [Activer les connexions SSH](functions-create-function-linux-custom-image.md#enable-ssh-connections).

### <a name="set-required-app-settings"></a>Définir les paramètres de l’application obligatoires

Exécutez les commandes suivantes afin de créer un paramètre d’application pour la chaîne de connexion du compte de stockage :

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv)
az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
```

Vous devez exécuter ce code dans Cloud Shell ou dans Bash sur votre ordinateur local. Remplacez `<STORAGE_NAME>` par le nom du compte de stockage et `<APP_NAME>` par le nom de l’application de fonction.  

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
