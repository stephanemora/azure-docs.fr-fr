---
title: Créer une fonction Azure Functions sur Linux avec une image personnalisée
description: Découvrez comment créer une exécution d’Azure Functions sur une image Linux personnalisée.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions01
ms.openlocfilehash: 9c97606b21a6e98494fffb689567aaab6e2f0621
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210189"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Créer une fonction sur Linux avec un conteneur personnalisé

Dans ce tutoriel, vous allez créer et déployer du code Python sur Azure Functions en tant que conteneur Docker personnalisé en utilisant une image de base Linux. Vous utilisez une image personnalisée généralement quand vos fonctions nécessitent une version d’un langage spécifique, ou quand elles ont une dépendance ou une configuration spécifique qui n’est pas fournie par l’image intégrée.

Vous pouvez également utiliser un conteneur Azure App Service par défaut, comme décrit dans [Créer votre première fonction hébergée sur Linux](functions-create-first-azure-function-azure-cli-linux.md). Les images de base prises en charge pour Azure Functions se trouvent dans le [référentiel d’images de base Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une application de fonction et un Dockerfile avec Azure Functions Core Tools.
> * Créer une image personnalisée à l’aide de Docker
> * Publier une image personnalisée dans un registre de conteneurs
> * Créer des ressources de prise en charge dans Azure pour l’application de fonction
> * Déployer une application de fonction à partir de Docker Hub
> * Ajouter des paramètres d’application à l’application de fonction
> * Activer le déploiement continu.
> * Activer les connexions SSH au conteneur.
> * Ajouter une liaison de sortie de Stockage File d’attente. 

Vous pouvez suivre ce tutoriel sur n’importe quel ordinateur exécutant Windows, Mac OS ou Linux. Le suivi de ce tutoriel entraîne uun coût de quelques dollars USD dans votre compte Azure.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1846 ou ultérieure
- [Azure CLI](/cli/azure/install-azure-cli) version 2.0.77 ou ultérieure
- Le [runtime Azure Functions 2.x](functions-versions.md)
- Les composants du runtime de langage suivants :
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x ou ultérieur](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.JS](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 bits](https://www.python.org/downloads/release/python-3610/) ou [Python 3.7 - 64 bits](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.JS](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- Un [ID Docker](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Vérification du prérequis

1. Dans une fenêtre de terminal ou une fenêtre Commande, exécutez `func --version` pour vérifier que vous disposez d’Azure Functions Core Tools version 2.7.1846 ou ultérieure.
1. Exécutez `az --version` pour vérifier que vous disposez d’Azure CLI version 2.0.76 ou ultérieure.
1. Exécutez `az login` pour vous connecter à Azure et vérifier qu’il y a un abonnement actif.
1. Exécutez `docker login` pour vous connecter à Docker. Cette commande échoue si Docker n’est pas en cours d’exécution, auquel cas démarrez Docker et réessayez la commande.

## <a name="create-and-test-the-local-functions-project"></a>Créer et tester un projet de fonction local

1. Dans un terminal ou une invite de commandes, créez un dossier pour ce tutoriel à un emplacement approprié, puis accédez à ce dossier.

1. Suivez les instructions de [Créer et activer un environnement virtuel](functions-create-first-function-python.md#create-and-activate-a-virtual-environment) pour créer un environnement virtuel que vous allez utiliser dans ce tutoriel.

1. Exécutez la commande suivante pour le langage que vous avez choisi pour créer un projet d’application de fonction dans un dossier nommé `LocalFunctionsProject`. L’option `--docker` génère un `Dockerfile` pour le projet, qui définit un conteneur personnalisé approprié pour une utilisation avec Azure Functions et le runtime sélectionné.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Accédez au dossier du projet :

    ```
    cd LocalFunctionsProject
    ```
    
1. Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction, et l’argument `--template` spécifie le déclencheur de la fonction. `func new` crée un sous-dossier correspondant au nom de la fonction qui contient un fichier de code approprié au langage choisi du projet et un fichier de configuration nommé *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Pour tester la fonction localement, démarrez l’hôte du runtime Azure Functions local dans le dossier *LocalFunctionsProject* :
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Une fois que le point de terminaison `HttpExample` apparaît dans la sortie, accédez à `http://localhost:7071/api/HttpExample?name=Functions`. Le navigateur doit afficher un message comme « Hello, Functions » (qui varie légèrement selon le langage de programmation que vous avez choisi).

1. Utilisez **Ctrl**-**C** pour arrêter l’hôte.

## <a name="build-the-container-image-and-test-locally"></a>Générer l’image conteneur et tester localement

1. (Facultatif) Examinez le « Dockerfile » dans le dossier *LocalFunctionsProj*. Le Dockerfile décrit l’environnement nécessaire pour exécuter l’application de fonction sur Linux : 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > La liste complète des images de base prises en charge pour Azure Functions se trouve sur la [pages des images de base Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. Dans le dossier *LocalFunctionsProject*, exécutez la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/), puis spécifiez un nom, `azurefunctionsimage`, et une étiquette, `v1.0.0`. Remplacez `<docker_id>` par votre ID de compte Docker Hub. Cette commande génère l’image Docker pour le conteneur.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Une fois la commande terminée, vous pouvez exécuter le nouveau conteneur localement.
    
1. Pour tester la génération, exécutez l’image dans un conteneur local avec la commande [Docker run](https://docs.docker.com/engine/reference/commandline/run/), en remplaçant à nouveau `<docker_id>` par votre ID Docker et en ajoutant l’argument pour les ports, `-p 8080:80` :

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Une fois que l’image s’exécute dans un conteneur local, ouvrez un navigateur sur `http://localhost:8080`, qui doit afficher l’image de l’espace réservé montrée ci-dessous. L’image apparaît à ce stade, car votre fonction est exécutée dans le conteneur local, comme ce serait le cas dans Azure, ce qui signifie qu’elle est protégée par une clé d’accès telle que définie dans *function.json* avec la propriété `"authLevel": "function"`. Le conteneur n’a cependant pas encore été publié sur une application de fonction : la clé n’est donc pas encore disponible. Si vous voulez effectuer tester localement, arrêtez Docker, changez la propriété d’autorisation en `"authLevel": "anonymous"`, regénérez l’image, puis redémarrez Docker. Ensuite, réinitialisez `"authLevel": "function"` dans *function.json*. Pour plus d’informations, consultez [Clés d’autorisation](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Image d’espace réservé indiquant que le conteneur s’exécute localement](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Une fois que vous avez vérifié l’application de fonction dans le conteneur, arrêtez Docker avec **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Envoyer (push) l’image sur Docker Hub

Docker Hub est un registre de conteneurs qui héberge des images, et qui fournit des services d’image et de conteneur. Pour partager votre image, ce qui inclut un déploiement sur Azure, vous devez l’envoyer dans un registre.

1. Si vous ne vous êtes pas déjà connecté à Docker, utilisez la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/), en remplaçant `<docker_id>` par votre ID Docker. Cette commande vous invite à entrer votre nom d’utilisateur et votre mot de passe. Un message « Connexion réussie » confirme que vous êtes connecté.

    ```
    docker login
    ```
    
1. Une fois connecté, envoyez l’image à Docker Hub en utilisant la commande [docker push](https://docs.docker.com/engine/reference/commandline/push/), en remplaçant à nouveau `<docker_id>` par votre ID Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Selon la rapidité de votre réseau, le premier initial de l’image peut prendre quelques minutes (l’envoi des modifications suivantes est beaucoup plus rapide). Pendant que vous attendez, vous pouvez passer à la section suivante et créer des ressources Azure dans un autre terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Créer des ressources Azure de support pour votre fonction

Pour déployer votre code de fonction sur Azure, vous devez créer trois ressources :

- Un groupe de ressources, qui est un conteneur logique pour les ressources associées.
- Un compte de stockage Azure, qui conserve l’état et d’autres informations spécifiques à vos projets.
- Une application Azure Functions, qui fournit l’environnement d’exécution de votre code de fonction. Une application de fonction est mappée à votre projet de fonction local. Elle vous permet de regrouper les fonctions en tant qu’unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

Vous utilisez les commandes Azure CLI pour créer ces éléments. Chaque commande fournit une sortie JSON une fois l’opération terminée.

1. Connectez-vous à Azure avec la commande [az login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources nommé `AzureFunctionsContainers-rg` dans la région `westeurope`. (Vous créez généralement votre groupe de ressources et vos ressources dans une région près de chez vous, en utilisant une région disponible à partir de la commande `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Vous ne pouvez pas héberger des applications Linux et Windows dans le même groupe de ressources. Si vous disposez d’un groupe de ressources existant nommé `AzureFunctionsContainers-rg` avec une application de fonction Windows ou une application web, vous devez utiliser un autre groupe de ressources.
    
1. Créez un compte de stockage universel dans votre groupe de ressources et votre région à l’aide de la commande [az storage account create](/cli/azure/storage/account#az-storage-account-create). Dans l’exemple suivant, remplacez `<storage_name>` par un nom globalement unique qui vous convient. Les noms doivent contenir entre 3 et 24 caractères, et comporter uniquement des lettres minuscules. `Standard_LRS` spécifie un compte universel standard.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Le compte de stockage génère un coût de quelques cents USD seulement pour ce tutoriel.
    
1. Utilisez la commande pour créer un plan Premium pour Azure Functions nommé `myPremiumPlan` dans le niveau tarifaire **Elastic Premium 1** (`--sku EP1`), dans la région Europe Ouest (`-location westeurope` ou utilisez une région appropriée proche de vous) et dans un conteneur Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    L’hébergement Linux pour les conteneurs de fonctions personnalisées est pris en charge sur les [plans dédiés (App Service)](functions-scale.md#app-service-plan) et les [plans Premium](functions-premium-plan.md#features). Nous utilisons ici le plan Premium, qui peut être mis à l’échelle en fonction des besoins. Pour en savoir plus sur l’hébergement, consultez [Comparaison des plans d’hébergement Azure Functions](functions-scale.md). Pour calculer les coûts, consultez la [page Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

    La commande provisionne également une instance Azure Application Insights associée dans le même groupe de ressources, avec laquelle vous pouvez superviser votre application de fonction et visualiser les journaux. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). L’instance n’entraîne aucun coût tant que vous ne l’activez pas.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Créer et configurer une application de fonction sur Azure avec l’image

Une application de fonction sur Azure gère l’exécution de vos fonctions dans votre plan d’hébergement. Dans cette section, vous utilisez les ressources Azure de la section précédente pour créer une application de fonction à partir d’une image sur Docker Hub et pour la configurer avec une chaîne de connexion à Stockage Azure.

1. Créez l’application de fonction avec la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Dans l’exemple suivant, remplacez `<storage_name>` par le nom que vous avez utilisé dans la section précédente pour le compte de stockage. Remplacez aussi `<app_name>` par un nom globalement unique qui vous convient et `<docker_id>` par votre ID Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Le paramètre *deployment-container-image-name* spécifie l’image à utiliser pour l’application de fonction. Vous pouvez utiliser la commande [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) pour voir des informations sur l’image utilisée pour le déploiement. Vous pouvez aussi utiliser la commande [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) pour déployer à partir d’une autre image.

1. Récupérez la chaîne de connexion pour le compte de stockage que vous avez créé avec la commande [az storage account show-connection-string](/cli/azure/storage/account), en l’affectant à une variable de shell `storageConnectionString` :

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    
1. Add this setting to the function app by using the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command. In the following command, replace `<app_name>` with the name of your function app, and replace `<connection_string>` with the connection string from the previous step (a long encoded string that begins with "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. La fonction peut maintenant utiliser cette chaîne de connexion pour accéder au compte de stockage.

> [!TIP]
> Dans bash, vous pouvez utiliser une variable de shell pour capturer la chaîne de connexion, au lieu d’utiliser le Presse-papiers. Pour commencer, utilisez la commande suivante pour créer une variable avec la chaîne de connexion :
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Ensuite, référencez la variable dans la deuxième commande :
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Si vous publiez votre image personnalisée sur un compte de conteneur privé, vous devez utiliser à la place des variables d’environnement dans le Dockerfile pour la chaîne de connexion. Pour plus d’informations, consultez l’[instruction ENV](https://docs.docker.com/engine/reference/builder/#env). Vous devez également définir les variables `DOCKER_REGISTRY_SERVER_USERNAME` et `DOCKER_REGISTRY_SERVER_PASSWORD`. Pour utiliser les valeurs, vous devez regénérer l’image, envoyer l’image vers le registre, puis redémarrer l’application de fonction sur Azure.

## <a name="verify-your-functions-on-azure"></a>Vérifier vos fonctions sur Azure

Avec l’image déployée sur l’application de fonction sur Azure, vous pouvez désormais appeler la fonction via des requêtes HTTP. Comme la définition de *function.json* inclut la propriété `"authLevel": "function"`, vous devez d’abord obtenir la clé d’accès (également appelée « clé de fonction ») et l’inclure en tant que paramètre d’URL dans toutes les requêtes adressées au point de terminaison.

1. Récupérez l’URL de la fonction avec la clé (de fonction) d’accès en utilisant le portail Azure ou Azure CLI avec la commande `az rest`.

    # <a name="portal"></a>[Portail](#tab/portal)

    1. Connectez-vous au portail Azure, puis recherchez votre application de fonction en entrant son nom dans la zone **Rechercher** en haut de la page. Dans les résultats, sélectionnez la ressource **App Service**.

    1. Dans le volet de navigation gauche, sous **Fonctions (lecture seule)** , sélectionnez le nom de votre fonction.

    1. Dans le volet d’informations, sélectionnez **</> Obtenir l’URL de la fonction** :
    
        ![La commande Obtenir l’URL de la fonction sur le portail Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Dans la fenêtre contextuelle, sélectionnez **Par défaut (clé de fonction)** puis **Copier**. La clé est la chaîne de caractères qui suit `?code=`.

        ![Copie de l’URL de la fonction à partir du portail Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Dans la mesure où votre application de fonction est déployée en tant que conteneur, vous ne pouvez pas apporter de modifications à votre code de fonction dans le portail. Vous devez à la place mettre à jour le projet dans l’image locale, envoyer à nouveau l’image vers le registre, puis redéployer sur Azure. Vous pouvez configurer le déploiement continu dans une section ultérieure.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Construisez une chaîne d’URL au format suivant, en remplaçant `<subscription_id>`, `<resource_group>`et `<app_name>` par votre ID d’abonnement Azure, par le groupe de ressources de votre application de fonction et par le nom de votre application de fonction, respectivement :

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Par exemple, l’URL peut se présenter comme l’adresse suivante :

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Par souci pratique, vous pouvez à la place affecter l’URL à une variable d’environnement et l’utiliser dans la commande `az rest`.
    
    1. Exécutez la commande `az rest` suivante (disponible dans Azure CLI version 2.0.77 et ultérieure), en remplaçant `<uri>` par la chaîne d’URI de la dernière étape, en y incluant les guillemets :

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. La sortie de la commande est la clé de la fonction. L’URL complète de la fonction est alors `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` (remplacez `<app_name>`, `<function_name>` et `<key>` par vos valeurs spécifiques).
    
        > [!NOTE]
        > La clé récupérée ici est la clé d’*hôte* qui marche pour toutes les fonctions de l’application de fonction ; la méthode montrée pour le portail récupère la clé pour cette seule fonction.

    ---

1. Collez l’URL de la fonction dans la barre d’adresse de votre navigateur, en ajoutant le paramètre `&name=Azure` à la fin de cette URL. Un texte similaire à « Hello Azure » doit apparaître dans le navigateur.

    ![Réponse de la fonction dans le navigateur.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Pour tester l’autorisation, supprimez le paramètre code= de l’URL et vérifiez que vous ne recevez pas de réponse de la fonction.


## <a name="enable-continuous-deployment-to-azure"></a>Activer le déploiement continu sur Azure

Vous pouvez activer Azure Functions pour mettre à jour automatiquement votre déploiement d’une image chaque fois que vous mettez à jour l’image dans le registre.

1. Activez le déploiement continu avec la commande [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config), en remplaçant `<app_name>` par le nom de votre application de fonction :

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Cette commande active le déploiement continu et retourne l’URL du webhook de déploiement. (Vous pouvez récupérer cette URL ultérieurement avec la commande [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url).)

1. Copiez l’URL du webhook de déploiement dans le Presse-papiers.

1. Ouvrez [Docker Hub](https://hub.docker.com/), connectez-vous, puis sélectionnez **Référentiels** dans la barre de navigation. Recherchez et sélectionnez l’image, sélectionnez l’onglet **Webhooks**, spécifiez un **Nom de webhook**, collez votre URL dans **URL du webhook**, puis sélectionnez **Créer** :

    ![Ajoutez le webhook à votre référentiel DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Avec le webhook défini, Azure Functions redéploie votre image chaque fois que vous la mettez à jour dans Docker Hub.

## <a name="enable-ssh-connections"></a>Activer les connexions SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Avec le protocole SSH activé, vous pouvez vous connecter à votre conteneur en utilisant App Service Advanced Tools (Kudu). Pour faciliter la connexion à votre conteneur via SSH, Azure Functions fournit une image de base pour laquelle SSH est déjà activé. Vous devez seulement modifier votre Dockerfile, puis regénérer et redéployer l’image. Vous pouvez ensuite vous connecter au conteneur via les outils avancés (Kudu).

1. Dans votre Dockerfile, ajoutez la chaîne `-appservice` à l’image de base dans votre instruction `FROM` :

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    Les différences entre les images de base sont décrites dans le [tutoriel App Services - Images Docker personnalisées](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Regénérez l’image avec la commande `docker build`, en remplaçant `<docker_id>` par votre ID Docker :

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Envoyez l’image mise à jour sur Docker Hub, ce qui doit prendre beaucoup moins de temps que lors du premier envoi : seuls les segments mis à jour de l’image doivent être chargés.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions redéploie automatiquement l’image sur votre application de fonctions ; le processus prend moins d’une minute.

1. Dans un navigateur, ouvrez `https://<app_name>.scm.azurewebsites.net/` (remplacez `<app_name>` par votre nom unique). Cette URL est le point de terminaison des outils avancés (Kudu) pour votre conteneur d’application de fonction.

1. Connectez-vous à votre compte Azure, puis sélectionnez **SSH** pour établir une connexion avec votre conteneur. La connexion peut prendre quelques instants si Azure est toujours en train de mettre à jour l’image conteneur.

1. Une fois la connexion établie avec votre conteneur, exécutez la commande `top` pour voir les processus en cours d’exécution. 

    ![Commande top Linux s’exécutant dans une session SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Écrire dans une file d’attente Stockage Azure

Azure Functions vous permet de connecter vos fonctions à d’autres services et ressources Azure sans devoir écrire votre propre code d’intégration. Ces *liaisons*, qui représentent l’entrée et la sortie, sont déclarées dans la définition de la fonction. Les données issues des liaisons sont fournies à la fonction en tant que paramètres. Un *déclencheur* est un type spécial de liaison d’entrée. Si une fonction ne peut avoir qu’un seul déclencheur, elle peut avoir plusieurs liaisons d’entrée et de sortie. Pour en savoir plus, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

Cette section vous montre comment intégrer votre fonction à une file d’attente de stockage Azure. La liaison de sortie que vous ajoutez à cette fonction écrit des données d’une requête HTTP dans un message en file d’attente.

## <a name="retrieve-the-azure-storage-connection-string"></a>Récupérer la chaîne de connexion de Stockage Azure

Vous avez créé auparavant un compte de stockage Azure que l’application de fonction va utiliser. La chaîne de connexion pour ce compte est stockée de manière sécurisée dans les paramètres d’application au sein d’Azure. En téléchargeant le paramètre dans le fichier *local.settings.json*, vous pouvez utiliser cette connexion pour écrire dans une file d’attente de stockage du même compte au moment de l’exécution locale de la fonction. 

1. À partir de la racine du projet, exécutez la commande suivante, en remplaçant `<app_name>` par le nom de votre application de fonction créée dans le guide de démarrage rapide précédent. Cette commande remplace toutes les valeurs existantes dans le fichier.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Ouvrez *local.settings.json* et recherchez la valeur nommée `AzureWebJobsStorage`, qui est la chaîne de connexion du compte de stockage. Vous utilisez le nom `AzureWebJobsStorage` et la chaîne de connexion dans d’autres sections de cet article.

> [!IMPORTANT]
> Dans la mesure où *local.settings.json* contient des secrets téléchargés à partir d’Azure, excluez toujours ce fichier du contrôle de code source. Le fichier *.gitignore* créé avec un projet Functions local exclut le fichier par défaut.

### <a name="add-an-output-binding-to-functionjson"></a>Ajouter une liaison de sortie à function.json

Dans Azure Functions, chaque type de liaison nécessite la définition d’une `direction`, d’un `type` et d’un `name` unique dans le fichier *function.json*. Votre *function.json* contient déjà une liaison d’entrée pour le type « httpTrigger » et une liaison de sortie pour la réponse HTTP. Pour ajouter une liaison à une file d’attente de stockage, modifiez le fichier comme suit, ce qui ajoute une liaison de sortie pour le type « queue » (file d’attente), où la file d’attente apparaît dans le code sous la forme d’un argument d’entrée nommé `msg`. La liaison de file d’attente nécessite également le nom de la file d’attente à utiliser, dans le cas présent `outqueue`, et le nom du paramètre qui contient la chaîne de connexion, dans le cas présent `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

Dans un projet de bibliothèque de classes C#, les liaisons sont définies comme attributs de liaison sur la méthode de fonction. Le fichier *function.json* est ensuite généré automatiquement en fonction de ces attributs.

1. Pour la liaison de file d’attente, exécutez la commande [dotnet add package](/dotnet/core/tools/dotnet-add-package) suivante pour ajouter le package d’extension de Stockage à votre projet.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Ouvrez le fichier *HttpTrigger.cs* et ajoutez l’instruction `using` suivante :

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Ajoutez le paramètre suivant à la définition de la méthode `Run` :
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    La définition de la méthode `Run` doit maintenant correspondre au code suivant :
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

Le paramètre `msg` est un type `ICollector<T>`, qui représente une collection de messages écrits dans une liaison de sortie quand la fonction se termine. Dans ce cas, la sortie est une file d’attente de stockage nommée `outqueue`. La chaîne de connexion pour le compte de stockage est définie par `StorageAccountAttribute`. Cet attribut indique le paramètre qui contient la chaîne de connexion du compte de stockage et qui peut être appliqué au niveau de la classe, de la méthode ou du paramètre. Dans ce cas, vous pourriez omettre `StorageAccountAttribute`, car vous utilisez déjà le compte de stockage par défaut.

::: zone-end

::: zone pivot="programming-language-javascript"

Mettez à jour *function.json* comme ceci en ajoutant la liaison de file d’attente après la liaison HTTP :

```json
{
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
      "name": "res"
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
::: zone-end

::: zone pivot="programming-language-powershell"

Mettez à jour *function.json* comme ceci en ajoutant la liaison de file d’attente après la liaison HTTP :

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
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
::: zone-end

::: zone pivot="programming-language-python"

Mettez à jour *function.json* comme ceci en ajoutant la liaison de file d’attente après la liaison HTTP :

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
::: zone-end

::: zone pivot="programming-language-typescript"

Mettez à jour *function.json* comme ceci en ajoutant la liaison de file d’attente après la liaison HTTP :

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
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
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Ajouter le code qui utilise la liaison de sortie

Une fois la liaison définie, le nom de la liaison, dans le cas présent `msg`, apparaît dans le code de la fonction en tant qu’argument (ou dans l’objet `context` en JavaScript et en TypeScript). Vous pouvez ensuite utiliser cette variable pour écrire des messages dans la file d’attente. Vous ne devez écrire aucun code pour l’authentification, pour obtenir une référence à une file d’attente ou pour écrire des données. Toutes ces tâches d’intégration sont gérées de façon pratique dans le runtime Azure Functions et la liaison de sortie de la file d’attente.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
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
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Mettre à jour l’image dans le registre

1. Dans le dossier racine, réexécutez la commande `docker build` et mettez cette fois à jour la version dans l’étiquette vers `v1.0.1`. Comme précédemment, remplacez `<docker_id>` par votre ID de compte Docker Hub :

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Envoyez l’image mise à jour dans le référentiel avec `docker push` :

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Comme vous avez configuré la livraison continue, la mise à jour de l’image dans le registre met à jour automatiquement votre application de fonction dans Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Afficher le message dans la file d’attente Stockage Azure

Dans un navigateur, utilisez la même URL qu’auparavant pour appeler votre fonction. Le navigateur doit afficher la même réponse qu’auparavant, car vous n’avez pas modifié cette partie du code de la fonction. Le code ajouté a cependant écrit un message en utilisant le paramètre d’URL `name` dans la file d’attente de stockage `outqueue`.

Vous pouvez visualiser la file d’attente dans le [portail Azure](../storage/queues/storage-quickstart-queues-portal.md) ou dans l’[Explorateur Stockage Microsoft Azure](https://storageexplorer.com/). Vous pouvez également voir la file d’attente dans Azure CLI, comme indiqué dans les étapes suivantes :

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
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
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
    
    Comme vous devez déréférencer la collection de messages et décoder à partir de base64, exécutez PowerShell et utilisez la commande PowerShell.

    ---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez continuer à utiliser Azure Functions en utilisant les ressources que vous avez créées dans ce tutoriel, vous pouvez conserver toutes ces ressources en place. Comme vous avez créé un plan Premium pour Azure Functions, un ou deux dollars US vous seront facturés quotidiennement.

Pour éviter ces frais récurrents, supprimez le groupe de ressources `AzureFunctionsContainer-rg` pour supprimer toutes les ressources de ce groupe : 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Étapes suivantes

+ [Supervision des fonctions](functions-monitoring.md)
+ [Mise à l’échelle et options d’hébergement](functions-scale.md)
+ [Hébergement serverless basé sur Kubernetes](functions-kubernetes-keda.md)
