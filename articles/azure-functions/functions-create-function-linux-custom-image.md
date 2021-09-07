---
title: Créer une fonction Azure Functions sur Linux avec une image personnalisée
description: Découvrez comment créer une exécution d’Azure Functions sur une image Linux personnalisée.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: a6e3ad07f9ba46bd15fe662f370ae2ffc3deb4a8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830511"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Créer une fonction sur Linux avec un conteneur personnalisé

Dans ce tutoriel, vous allez créer et déployer votre code sur Azure Functions en tant que conteneur Docker personnalisé en utilisant une image de base Linux. Vous utilisez une image personnalisée généralement quand vos fonctions nécessitent une version d’un langage spécifique, ou quand elles ont une dépendance ou une configuration spécifique qui n’est pas fournie par l’image intégrée.

::: zone pivot="programming-language-other"
Azure Functions prend en charge n’importe quel langage ou runtime à l’aide de [gestionnaires personnalisés](functions-custom-handlers.md). Pour certains langages, tels que le langage de programmation R utilisé dans ce tutoriel, vous devez installer le runtime ou des bibliothèques supplémentaires en tant que dépendances qui demandent d’utiliser un conteneur personnalisé.
::: zone-end

Le déploiement du code de votre fonction dans un conteneur Linux personnalisé nécessite un hébergement de [plan Premium](functions-premium-plan.md) ou de [Plan dédié (App Service)](dedicated-plan.md). Effectuer ce tutoriel entraîne des coûts de quelques dollars US dans votre compte Azure, que vous pouvez réduire en [nettoyant les ressources](#clean-up-resources) quand vous avez terminé.

Vous pouvez également utiliser un conteneur Azure App Service par défaut, comme décrit dans [Créer votre première fonction hébergée sur Linux](./create-first-function-cli-csharp.md?pivots=programming-language-python). Les images de base prises en charge pour Azure Functions se trouvent dans le [référentiel d’images de base Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

Dans ce tutoriel, vous allez apprendre à :

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
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
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Créer une application de fonction et un Dockerfile avec Azure Functions Core Tools.
> * Créer une image personnalisée à l’aide de Docker
> * Publier une image personnalisée dans un registre de conteneurs
> * Créer des ressources de prise en charge dans Azure pour l’application de fonction
> * Déployer une application de fonction à partir de Docker Hub
> * Ajouter des paramètres d’application à l’application de fonction
> * Activer le déploiement continu.
> * Activer les connexions SSH au conteneur.
::: zone-end

Vous pouvez suivre ce tutoriel sur n’importe quel ordinateur exécutant Windows, macOS ou Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Un [ID Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Créer et tester un projet de fonction local

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Dans un terminal ou une invite de commandes, exécutez la commande suivante dans le langage de votre choix pour créer un projet d’application de fonction dans le dossier actuel.  
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[In-process](#tab/in-process)
```console
func init --worker-runtime dotnet --docker
```

# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)
```console
func init --worker-runtime dotnet-isolated --docker
```
---
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Dans un dossier vide, exécutez la commande suivante pour générer le projet Functions à partir d’un [archétype Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

Le paramètre `-DjavaVersion` dit au runtime Functions quelle version de Java utiliser. Utilisez `-DjavaVersion=11` si vous voulez que vos fonctions s’exécutent sur Java 11. Si vous ne spécifiez pas `-DjavaVersion`, par défaut Maven utilise Java 8. Pour plus d’informations, consultez [Versions de Java](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> La variable d’environnement `JAVA_HOME` doit être définie sur l’emplacement d’installation de la version appropriée du JDK pour suivre cet article.

Maven vous invite à entrer les valeurs nécessaires pour terminer la génération du projet lors du déploiement.   
Fournissez les valeurs suivantes à l’invite :

| Prompt | Valeur | Description |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Valeur qui identifie de façon unique votre projet parmi tous les projets, avec respect des [règles de nommage de package](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pour Java. |
| **artifactId** | `fabrikam-functions` | Valeur qui correspond au nom du fichier jar, sans numéro de version. |
| **version** | `1.0-SNAPSHOT` | Choisissez la valeur par défaut. |
| **package** | `com.fabrikam.functions` | Valeur qui correspond au package Java pour le code de fonction généré. Utilisez la valeur par défaut. |

Tapez `Y` ou appuyez sur Entrée pour confirmer.

Maven crée les fichiers projet dans un nouveau dossier avec le nom d’_artifactId_, qui est `fabrikam-functions` dans cet exemple. 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init --worker-runtime custom --docker
```
::: zone-end

L’option `--docker` génère un `Dockerfile` pour le projet, qui définit un conteneur personnalisé approprié pour une utilisation avec Azure Functions et le runtime sélectionné.

::: zone pivot="programming-language-java"  
Accédez au dossier du projet :

```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[In-process](#tab/in-process)
Aucune modification n’est requise pour le fichier Dockerfile.
# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)
Ouvrez le fichier Dockerfile et ajoutez les lignes suivantes après la première instruction `FROM`, si elles ne sont pas déjà présentes :

```docker
# Build requires 3.1 SDK
COPY --from=mcr.microsoft.com/dotnet/core/sdk:3.1 /usr/share/dotnet /usr/share/dotnet
```
---
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction, et l’argument `--template` spécifie le déclencheur de la fonction. `func new` crée un fichier de code C# dans votre projet.

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end

::: zone pivot="programming-language-other,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction, et l’argument `--template` spécifie le déclencheur de la fonction. `func new` crée un sous-dossier correspondant au nom de la fonction qui contient un fichier config nommé *function.json*.

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end  
::: zone pivot="programming-language-other" 
Au moyen d’un éditeur de texte, dans le dossier du projet, créez un fichier nommé *handler.R*. Ajoutez le code suivant en tant que contenu.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

Dans *host.json*, modifiez la section `customHandler` pour configurer la commande de démarrage du gestionnaire personnalisé.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Pour tester la fonction localement, démarrez l’hôte du runtime Azure Functions local à la racine du dossier du projet : 
::: zone pivot="programming-language-csharp"  
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Une fois que le point de terminaison `HttpExample` apparaît dans la sortie, accédez à `http://localhost:7071/api/HttpExample?name=Functions`. Le navigateur doit afficher un message « hello » qui renvoie `Functions` par écho, c’est-à-dire la valeur fournie au paramètre de requête `name`.

Utilisez **Ctrl**-**C** pour arrêter l’hôte.

## <a name="build-the-container-image-and-test-locally"></a>Générer l’image conteneur et tester localement

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Facultatif) Examinez le *Dockerfile* à la racine du dossier du projet. Le Dockerfile décrit l’environnement requis pour exécuter l’application de fonction sur Linux.  La liste complète des images de base prises en charge pour Azure Functions se trouve sur la [pages des images de base Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Examinez le *Dockerfile* à la racine du dossier du projet. Le Dockerfile décrit l’environnement requis pour exécuter l’application de fonction sur Linux. Les applications de gestionnaire personnalisé utilisent l’image `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` comme base.

Modifiez le *Dockerfile* pour installer R. Remplacez le contenu de *Dockerfile* par le code suivant.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

À la racine du dossier du projet, exécutez la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/), puis indiquez un nom (`azurefunctionsimage`) et une balise (`v1.0.0`). Remplacez `<DOCKER_ID>` par votre ID de compte Docker Hub. Cette commande génère l’image Docker pour le conteneur.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Une fois la commande terminée, vous pouvez exécuter le nouveau conteneur localement.
    
Pour tester la génération, exécutez l’image dans un conteneur local avec la commande [Docker run](https://docs.docker.com/engine/reference/commandline/run/), en remplaçant à nouveau `<DOCKER_ID` par votre ID Docker et en ajoutant l’argument pour les ports, `-p 8080:80` :

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp"
# <a name="in-process"></a>[In-process](#tab/in-process)
Après le démarrage de l’image dans le conteneur local, accédez à `http://localhost:8080/api/HttpExample?name=Functions`, qui doit afficher le même message « hello » que précédemment. Étant donné que la fonction déclenchée par HTTP que vous avez créée utilise une autorisation anonyme, vous pouvez appeler la fonction en cours d’exécution dans le conteneur sans avoir à obtenir de clé d’accès. Pour en savoir plus, consultez [Clés d’autorisation].
# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)
Après le démarrage de l’image dans le conteneur local, accédez à `http://localhost:8080/api/HttpExample`, qui doit afficher le même message d’accueil que précédemment. Étant donné que la fonction déclenchée par HTTP que vous avez créée utilise une autorisation anonyme, vous pouvez appeler la fonction en cours d’exécution dans le conteneur sans avoir à obtenir de clé d’accès. Pour en savoir plus, consultez [Clés d’autorisation].

---
::: zone-end
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Après le démarrage de l’image dans le conteneur local, accédez à `http://localhost:8080/api/HttpExample?name=Functions`, qui doit afficher le même message « hello » que précédemment. Étant donné que la fonction déclenchée par HTTP que vous avez créée utilise une autorisation anonyme, vous pouvez appeler la fonction en cours d’exécution dans le conteneur sans avoir à obtenir de clé d’accès. Pour en savoir plus, consultez [Clés d’autorisation].
::: zone-end  

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

## <a name="create-supporting-azure-resources-for-your-function"></a>Créer des ressources Azure de prise en charge pour votre fonction

Avant de déployer le code de votre fonction dans Azure, vous devez créer trois ressources :

- Un [groupe de ressources](../azure-resource-manager/management/overview.md), qui est un conteneur logique pour les ressources associées.
- Un [compte de stockage](../storage/common/storage-account-create.md), qui sert à conserver l’état et d’autres informations sur vos fonctions.
- Une application de fonction, qui fournit l’environnement d’exécution de votre code de fonction. Une application de fonction est mappée à votre projet de fonction local. Elle vous permet de regrouper les fonctions en tant qu’unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

Utilisez les commandes suivantes pour créer ces éléments. Azure CLI et PowerShell sont pris en charge.

1. Si vous ne l’avez pas déjà fait, connectez-vous à Azure :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    La commande [az login](/cli/azure/reference-index#az_login) vous connecte à votre compte Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    L’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) vous connecte à votre compte Azure.

    ---

1. Créez un groupe de ressources nommé `AzureFunctionsContainers-rg` dans la région de votre choix :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location <REGION>
    ```
 
    La commande [az group create](/cli/azure/group#az_group_create) crée un groupe de ressources. Dans la commande ci-dessus, remplacez `<REGION>` par une région près de chez vous en utilisant un code de région disponible retourné par la commande [az account list-locations](/cli/azure/account#az_account_list_locations).

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsContainers-rg -Location <REGION>
    ```

    La commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) crée un groupe de ressources. Vous créez généralement votre groupe de ressources et vos ressources dans une région près de chez vous, en utilisant une région disponible qui est retournée par l’applet de commande [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

1. Créez un compte de stockage universel dans votre groupe de ressources et votre région :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```

    La commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) crée le compte de stockage. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsContainers-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    L’applet de commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) crée le compte de stockage.

    ---

    Dans l’exemple précédent, remplacez `<STORAGE_NAME>` par un nom qui vous convient et qui est unique dans Stockage Azure. Les noms doivent contenir entre 3 et 24 caractères, et comporter uniquement des lettres minuscules. `Standard_LRS` spécifie un compte universel, qui est [pris en charge par Functions](storage-considerations.md#storage-account-requirements).
    
1. Utilisez la commande pour créer un plan Premium pour Azure Functions nommé `myPremiumPlan` dans le niveau tarifaire **Elastic Premium 1** (`--sku EP1`), dans votre `<REGION>` et dans un conteneur Linux (`--is-linux`).

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location <REGION> --number-of-workers 1 --sku EP1 --is-linux
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```powershell
    New-AzFunctionAppPlan -ResourceGroupName AzureFunctionsContainers-rg -Name MyPremiumPlan -Location <REGION> -Sku EP1 -WorkerType Linux
    ```
    ---
    Nous utilisons ici le plan Premium, qui peut être mis à l’échelle en fonction des besoins. Pour en savoir plus sur l’hébergement, consultez [Comparaison des plans d’hébergement Azure Functions](functions-scale.md). Pour calculer les coûts, consultez la [page Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

    La commande provisionne également une instance Azure Application Insights associée dans le même groupe de ressources, avec laquelle vous pouvez superviser votre application de fonction et visualiser les journaux. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). L’instance n’entraîne aucun coût tant que vous ne l’activez pas.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Créer et configurer une application de fonction sur Azure avec l’image

Une application de fonction sur Azure gère l’exécution de vos fonctions dans votre plan d’hébergement. Dans cette section, vous utilisez les ressources Azure de la section précédente pour créer une application de fonction à partir d’une image sur Docker Hub et pour la configurer avec une chaîne de connexion à Stockage Azure.

1. Créez une application de fonction avec la commande suivante :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp create --name <APP_NAME> --storage-account <STORAGE_NAME> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```

    Dans la commande [az functionapp create](/cli/azure/functionapp#az_functionapp_create), le paramètre *deployment-container-image-name* spécifie l’image à utiliser pour l’application de fonction. Vous pouvez utiliser la commande [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) pour voir des informations sur l’image utilisée pour le déploiement. Vous pouvez aussi utiliser la commande [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) pour déployer à partir d’une autre image.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -PlanName myPremiumPlan -StorageAccount <STORAGE_NAME> -DockerImageName <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```
    ---
    
    Dans cet exemple, remplacez `<STORAGE_NAME>` par le nom que vous avez utilisé dans la section précédente pour le compte de stockage. Remplacez aussi `<APP_NAME>` par un nom globalement unique qui vous convient et `<DOCKER_ID>` par votre ID DockerHub.    
    
    > [!TIP]  
    > Vous pouvez utiliser le [`DisableColor`paramètre](functions-host-json.md#console) dans le fichier host.json pour empêcher l’écriture des caractères de contrôle ANSI dans les journaux de conteneur. 

1. Utilisez la commande suivante afin d’obtenir la chaîne de connexion pour le compte de stockage que vous avez créé :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv
    ```

    La chaîne de connexion pour le compte de stockage est retournée avec la commande [az storage account show-connection-string](/cli/azure/storage/account). 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    $storage_name = "glengagtestdockerstorage"
    $key = (Get-AzStorageAccountKey -ResourceGroupName AzureFunctionsContainers-rg -Name $storage_name)[0].Value
    $string = "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=" + $storage_name + ";AccountKey=" + $key
    Write-Output($string) 
    ```
    La clé retournée par l’applet de commande [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) est utilisée pour construire la chaîne de connexion pour le compte de stockage.

    ---    

    Remplacez `<STORAGE_NAME>` par le nom du compte de stockage que vous avez créé.

1. Ajoutez ce paramètre à l’application de fonction en utilisant la commande suivante : 
 
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp config appsettings set --name <APP_NAME> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<CONNECTION_STRING>
    ```
    La commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) crée le paramètre. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"AzureWebJobsStorage"="<CONNECTION_STRING>"}
    ```
    L’applet de commande [Update-AzFunctionAppSetting](/powershell/module/az.functions/update-azfunctionappsetting) crée le paramètre.

    ---

    Dans cette commande, remplacez `<APP_NAME>` par le nom de votre application de fonction et `<CONNECTION_STRING>` par la chaîne de connexion issue de l’étape précédente. La connexion doit être une longue chaîne codée qui commence par `DefaultEndpointProtocol=`.
 

1. La fonction peut maintenant utiliser cette chaîne de connexion pour accéder au compte de stockage.

> [!NOTE]    
> Si vous publiez votre image personnalisée sur un compte de conteneur privé, vous devez utiliser à la place des variables d’environnement dans le Dockerfile pour la chaîne de connexion. Pour plus d’informations, consultez l’[instruction ENV](https://docs.docker.com/engine/reference/builder/#env). Vous devez également définir les variables `DOCKER_REGISTRY_SERVER_USERNAME` et `DOCKER_REGISTRY_SERVER_PASSWORD`. Pour utiliser les valeurs, vous devez regénérer l’image, envoyer l’image vers le registre, puis redémarrer l’application de fonction sur Azure.

## <a name="verify-your-functions-on-azure"></a>Vérifier vos fonctions sur Azure

Une fois l’image déployée sur votre application de fonction dans Azure, vous pouvez appeler la fonction comme avant via des requêtes HTTP.
Dans votre navigateur, accédez à une URL telle que la suivante :

::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`  
::: zone-end  
::: zone pivot="programming-language-csharp"  
# <a name="in-process"></a>[In-process](#tab/in-process) 
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`
# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)
`https://<APP_NAME>.azurewebsites.net/api/HttpExample`

---
:::zone-end  

Remplacez `<APP_NAME>` par le nom de votre application de fonction. Quand vous accédez à cette URL, le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

## <a name="enable-continuous-deployment-to-azure"></a>Activer le déploiement continu sur Azure

Vous pouvez activer Azure Functions pour mettre à jour automatiquement votre déploiement d’une image chaque fois que vous mettez à jour l’image dans le registre.

1. Activez le déploiement continu et récupérez l’URL du webhook avec les commandes suivantes :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <APP_NAME> --resource-group AzureFunctionsContainers-rg
    ```
    
    La commande [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) active le déploiement continu et retourne l’URL du webhook de déploiement. Vous pouvez récupérer cette URL ultérieurement avec la commande [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url).

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"DOCKER_ENABLE_CI" = "true"}
    Get-AzWebAppContainerContinuousDeploymentUrl -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg
    ```
    
    Le paramètre d’application `DOCKER_ENABLE_CI` détermine si le déploiement continu est activé à partir du dépôt de conteneurs.  L’applet de commande [Get-AzWebAppContainerContinuousDeploymentUrl](/powershell/module/az.websites/get-azwebappcontainercontinuousdeploymenturl) retourne l’URL du webhook de déploiement.

    ---    

    Comme avant, remplacez `<APP_NAME>` par le nom de votre application de fonction. 

1. Copiez l’URL du webhook de déploiement dans le Presse-papiers.

1. Ouvrez [Docker Hub](https://hub.docker.com/), connectez-vous, puis sélectionnez **Référentiels** dans la barre de navigation. Recherchez et sélectionnez l’image, sélectionnez l’onglet **Webhooks**, spécifiez un **Nom de webhook**, collez votre URL dans **URL du webhook**, puis sélectionnez **Créer** :

    ![Ajoutez le webhook à votre référentiel DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Avec le webhook défini, Azure Functions redéploie votre image chaque fois que vous la mettez à jour dans Docker Hub.

## <a name="enable-ssh-connections"></a>Activer les connexions SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Avec le protocole SSH activé, vous pouvez vous connecter à votre conteneur en utilisant App Service Advanced Tools (Kudu). Pour faciliter la connexion à votre conteneur via SSH, Azure Functions fournit une image de base pour laquelle SSH est déjà activé. Vous devez seulement modifier votre Dockerfile, puis regénérer et redéployer l’image. Vous pouvez ensuite vous connecter au conteneur via les outils avancés (Kudu).

1. Dans votre Dockerfile, ajoutez la chaîne `-appservice` à l’image de base dans votre instruction `FROM` :

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
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
    
1. Regénérez l’image avec la commande `docker build`, en remplaçant `<docker_id>` par votre ID Docker :

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Envoyez l’image mise à jour sur Docker Hub, ce qui doit prendre beaucoup moins de temps que lors du premier envoi : seuls les segments mis à jour de l’image doivent être chargés.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions redéploie automatiquement l’image sur votre application de fonctions ; le processus prend moins d’une minute.

1. Dans un navigateur, ouvrez `https://<app_name>.scm.azurewebsites.net/` (remplacez `<app_name>` par votre nom unique). Cette URL est le point de terminaison des outils avancés (Kudu) pour votre conteneur d’application de fonction.

1. Connectez-vous à votre compte Azure, puis sélectionnez **SSH** pour établir une connexion avec votre conteneur. La connexion peut prendre quelques instants si Azure est toujours en train de mettre à jour l’image conteneur.

1. Une fois la connexion établie avec votre conteneur, exécutez la commande `top` pour voir les processus en cours d’exécution. 

    ![Commande top Linux s’exécutant dans une session SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-azure-queue-storage"></a>Écrire dans le Stockage File d’attente Azure

Azure Functions vous permet de connecter vos fonctions à d’autres services et ressources Azure sans devoir écrire votre propre code d’intégration. Ces *liaisons*, qui représentent l’entrée et la sortie, sont déclarées dans la définition de la fonction. Les données issues des liaisons sont fournies à la fonction en tant que paramètres. Un *déclencheur* est un type spécial de liaison d’entrée. Si une fonction ne peut avoir qu’un seul déclencheur, elle peut avoir plusieurs liaisons d’entrée et de sortie. Pour en savoir plus, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

Cette section vous montre comment intégrer votre fonction à un stockage File d’attente Azure. La liaison de sortie que vous ajoutez à cette fonction écrit des données d’une requête HTTP dans un message en file d’attente.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Inscrire des extensions de liaison
::: zone-end 

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Ajouter du code pour utiliser la liaison de sortie

Une fois la liaison de file d’attente définie, vous pouvez mettre à jour votre fonction pour qu’elle écrive des messages dans la file d’attente en utilisant le paramètre de liaison.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Mettre à jour l’image dans le registre

1. Dans le dossier racine, réexécutez la commande `docker build` et mettez cette fois à jour la version dans l’étiquette vers `v1.0.1`. Comme précédemment, remplacez `<docker_id>` par votre ID de compte Docker Hub :

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Envoyez l’image mise à jour dans le référentiel avec `docker push` :

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Comme vous avez configuré la livraison continue, la mise à jour de l’image dans le registre met à jour automatiquement votre application de fonction dans Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Afficher le message dans la file d’attente Stockage Azure

Dans un navigateur, utilisez la même URL qu’auparavant pour appeler votre fonction. Le navigateur doit afficher la même réponse qu’auparavant, car vous n’avez pas modifié cette partie du code de la fonction. Le code ajouté a cependant écrit un message en utilisant le paramètre d’URL `name` dans la file d’attente de stockage `outqueue`.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

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

[Clés d’autorisation]: functions-bindings-http-webhook-trigger.md#authorization-keys