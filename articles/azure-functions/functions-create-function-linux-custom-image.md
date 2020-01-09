---
title: Créer une fonction Azure Functions sur Linux avec une image personnalisée
description: Découvrez comment créer une exécution d’Azure Functions sur une image Linux personnalisée.
ms.date: 09/27/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5a7fbecca2dc7585ff7110d53deccbbbbf23087c
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551486"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Créer une fonction sur Linux avec une image personnalisée

Azure Functions vous permet d’héberger vos fonctions sur Linux dans votre propre conteneur personnalisé. Vous pouvez également [héberger dans un conteneur Azure App Service par défaut](functions-create-first-azure-function-azure-cli-linux.md). Cette fonctionnalité nécessite [le runtime Functions 2.x](functions-versions.md).

Dans ce didacticiel, vous apprenez à déployer vos fonctions vers Azure en tant qu’image personnalisée de Docker. Ce modèle est utile quand vous devez personnaliser l’image du conteneur intégrée. Une image personnalisée peut vous être utile lorsque vos fonctions nécessitent une version de langue spécifique, une dépendance particulière ou une configuration qui n’est pas fournie dans l’image intégrée. Les images de base prises en charge pour Azure Functions se trouvent dans le [référentiel d’images de base Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base). 

Ce didacticiel vous guide tout au long de l’utilisation d’Azure Functions Core Tools pour créer une fonction dans une image personnalisée Linux. Vous publiez cette image dans une application de fonction dans Azure, créée à l’aide d’Azure CLI. Plus tard, vous mettez à jour votre fonction pour vous connecter à Stockage File d’attente Azure. Vous activez également.  

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une application de fonction et un fichier Docker à l’aide de Core Tools.
> * Créer une image personnalisée à l’aide de Docker
> * Publier une image personnalisée dans un registre de conteneurs
> * Création d’un compte Azure Storage.
> * Créer un plan d’hébergement Premium.
> * Déployer une application de fonction à partir de Docker Hub
> * Ajouter des paramètres d’application à l’application de fonction
> * Activer le déploiement continu.
> * Activer les connexions SSH au conteneur.
> * Ajouter une liaison de sortie de Stockage File d’attente. 

Les étapes suivantes sont prises en charge sur un ordinateur Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Conditions préalables requises

Avant d’exécuter cet exemple, vous devez disposer des éléments suivants :

* Installez [Azure Core Tools version 2.x](functions-run-local.md#v2).

* Installez [Azure CLI]( /cli/azure/install-azure-cli). Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Exécutez `az --version` pour trouver la version qui est à votre disposition.  
Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/bash).

* Un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Créer le projet local

Exécutez la commande suivante à partir de la ligne de commande pour créer un projet d’application de fonction dans le dossier `MyFunctionProj` du répertoire local actif. Pour un projet Python, vous [devez effectuer l’exécution dans un environnement virtuel](functions-create-first-function-python.md#create-and-activate-a-virtual-environment).

```bash
func init MyFunctionProj --docker
```

Lorsque vous incluez l’option `--docker`, un fichier Docker est généré pour ce projet. Ce fichier est utilisé pour créer un conteneur personnalisé dans lequel exécuter le projet. L’image de base utilisée dépend du langage du worker runtime choisi.  

Lorsque vous y êtes invités, choisissez un worker runtime parmi les langages suivants :

* `dotnet` : crée un projet de bibliothèque de classes .NET Core (.csproj).
* `node` : créer un projet JavaScript.
* `python` : crée un projet Python.  

Utilisez la commande suivante pour accéder au nouveau dossier du projet `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Créer à partir du fichier Docker

Jetez un coup d’œil au _fichier Docker_ dans le dossier racine du projet. Ce fichier décrit l’environnement exigé pour exécuter l’application de fonction sur Linux. L’exemple suivant est un fichier Docker qui crée un conteneur exécutant une application de fonction sur le worker runtime JavaScript (Node.js) : 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> La liste complète des images de base prises en charge pour Azure Functions se trouve sur la [pages des images de base Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Exécutez la commande `build`.

Dans le dossier racine, exécutez la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/), puis indiquez un nom, `mydockerimage`, et une balise, `v1.0.0`. Remplacez `<docker-id>` par votre ID de compte Docker Hub. Cette commande génère l’image Docker pour le conteneur.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Une fois la commande terminée, vous pouvez exécuter le nouveau conteneur localement.

### <a name="run-the-image-locally"></a>Exécuter localement l’image
Vérifiez que l’image générée fonctionne en exécutant l’image Docker dans un conteneur local. Exécutez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) puis passez le nom et la balise de l’image. Veillez à spécifier le port à l’aide de l’argument `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Tandis que l’image personnalisée s’exécute dans un conteneur Docker local, vérifiez que l’application de fonction et le conteneur fonctionnent correctement en accédant à <http://localhost:8080>.

![Exécutez l’application de fonction localement.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> À ce stade, lorsque vous essayez d’appeler votre fonction HTTP spécifique, vous recevez une réponse d’erreur HTTP 401. Cela est dû au fait que votre fonction s’exécute dans le conteneur local comme dans Azure, ce qui signifie que la clé de fonction est requise. Étant donné que le conteneur n’a pas encore été publié dans une application de fonction, aucune clé de fonction n’est disponible. Vous verrez plus tard que, lorsque vous utilisez Core Tools pour publier votre conteneur, les clés de fonction vous sont indiquées. Si vous souhaitez tester votre fonction en cours d’exécution dans le conteneur local, vous pouvez remplacer la [clé d’autorisation](functions-bindings-http-webhook.md#authorization-keys) par `anonymous`. 

Une fois que vous avez vérifié l’application de fonction dans le conteneur, arrêtez l’exécution. À présent, vous pouvez transmettre l’image personnalisée à votre compte Docker Hub.

## <a name="push-to-docker-hub"></a>Envoyer (Push) à Docker Hub

Un registre est une application qui héberge des images et fournit des services d’image et de conteneur. Pour partager votre image, vous devez la transférer vers un registre. Docker Hub est un registre d’images Docker qui vous permet d’héberger vos propres référentiels publics ou privés.

Avant de pousser une image, vous devez vous connecter à Docker Hub par le biais de la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/). À l’invite de commandes dans la console, remplacez `<docker-id>` par votre nom de compte et tapez votre mot de passe. Pour les autres options de mot de passe Docker Hub, consultez la [documentation de la commande docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Un message indiquant que votre connexion a réussi confirme que vous êtes connecté. Une fois connecté, vous pouvez transférer l’image à Docker Hub au moyen de la commande [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

L’envoi ayant réussi, vous pouvez utiliser l’image comme source de déploiement pour une nouvelle application de fonction dans Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Créer un plan Premium

Hébergement Linux pour les conteneurs de fonctions personnalisés pris en charge sur les [plans dédiés (App Service)](functions-scale.md#app-service-plan) et les [plans Premium](functions-premium-plan.md#features). Ce tutoriel utilise un plan Premium, qui peut être mis à l’échelle en fonction des besoins. Pour en savoir plus sur l’hébergement, consultez [Comparaison des plans d’hébergement Azure Functions](functions-scale.md).

L’exemple suivant crée un plan Premium nommé `myPremiumPlan` dans le niveau tarifaire **Élastique Premium 1** (`--sku EP1`), dans la région USA Ouest (`-location WestUS`) et dans un conteneur Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Créer une application à partir de l’image

Une application de fonction gère l’exécution de vos fonctions dans votre plan d’hébergement. Créez une application de fonction à partir de l’image Docker Hub à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

Dans la commande suivante, indiquez un nom d’application de fonction unique là où se trouve l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. La valeur `<app_name>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Comme précédemment, `<docker-id>` est le nom de votre compte Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Le paramètre _deployment-container-image-name_ indique l’image hébergée sur Docker Hub à utiliser pour créer l’application de fonction. Utilisez la commande [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) pour afficher des informations sur l’image utilisée pour le déploiement. Utilisez la commande [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) pour déployer à partir d’une autre image.

## <a name="configure-the-function-app"></a>Configurer l’application de fonction

La fonction a besoin que la chaîne de connexion se connecte au compte de stockage par défaut. Lorsque vous publiez votre image personnalisée sur un compte de conteneur privé, préférez plutôt définir ces paramètres d’application comme des variables d’environnement dans le Dockerfile au moyen de l’[instruction ENV](https://docs.docker.com/engine/reference/builder/#env), ou équivalent.

Dans ce cas, `<storage_name>` est le nom du compte de stockage Blob que vous avez créé. Pour afficher la chaîne de connexion, exécutez la commande [az storage account show-connection-string](/cli/azure/storage/account). Ajoutez ces paramètres d’application dans l’application de fonction à l’aide de la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Si votre conteneur est privé, vous devez également définir les paramètres d’application suivants :  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Vous devez arrêter puis redémarrer votre application de fonction pour que ces valeurs soient récupérées.

## <a name="verify-your-functions"></a>Vérifier vos fonctions

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

La fonction déclenchée par HTTP que vous avez créée nécessite une [clé de fonction](functions-bindings-http-webhook.md#authorization-keys) lors de l’appel du point de terminaison. À ce stade, le moyen le plus simple d’obtenir votre URL de fonction, y compris la clé, est à partir du portail [Azure portal]. 

> [!TIP]
> Vous pouvez également obtenir vos clés de fonction en utilisant les [API de gestion des clés](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), ce qui vous oblige à présenter [un jeton du porteur pour l’authentification](/cli/azure/account#az-account-get-access-token).

Localisez votre nouvelle application de fonction dans le [Azure portal] en tapant son nom dans la zone **Rechercher** en haut de la page, puis en sélectionnant la ressource **App service**.

Sélectionnez la fonction **MyHttpTrigger**, puis **</> Get function URL** > **default (Function key)**  > **Copy**.

![Copier l’URL de fonction à partir du portail Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

Dans cette URL, la clé de fonction est le paramètre de requête `code`. 

> [!NOTE]  
> Dans la mesure où votre application de fonction est déployée en tant que conteneur, vous ne pouvez pas apporter de modifications à votre code de fonction dans le portail. À la place, vous devez mettre à jour le projet dans le conteneur local et le republier sur Azure.

Collez l’URL de fonction dans la barre d’adresse de votre navigateur. Ajoutez la valeur de la chaîne de requête `&name=<yourname>` à la fin de cette URL et appuyez sur la touche `Enter` de votre clavier pour exécuter la requête. Vous devez voir la réponse renvoyée par la fonction affichée dans le navigateur.

L’exemple suivant montre la réponse dans le navigateur :

![Réponse de la fonction dans le navigateur.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

L’URL de demande inclut une clé qui est requise, par défaut, pour accéder à votre fonction sur HTTP. 

## <a name="enable-continuous-deployment"></a>Activer le déploiement continu

Un des avantages de l’utilisation de conteneurs réside dans la prise en charge du déploiement continu. Functions vous permet de déployer automatiquement des mises à jour lorsque votre conteneur est mis à jour dans le registre. Activez le déploiement continu avec la commande [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config).

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Cette commande renvoie l’URL du webhook de déploiement une fois le déploiement continu activé. Vous pouvez également utiliser la commande [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) afin d’obtenir cette URL. 

Copiez l’URL de déploiement et accédez à votre référentiel DockerHub, choisissez l’onglet **Webhooks**, tapez un **nom de Webhook** pour votre webhook, collez votre URL dans **URL du Webhook**, puis choisissez le signe plus ( **+** ).

![Ajoutez le webhook à votre référentiel DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Une fois le webhook défini, toutes les mises à jour de l’image liée dans DockerHub entraînent le téléchargement et l’installation de la dernière image dans l’application de fonction.

## <a name="enable-ssh-connections"></a>Activer les connexions SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Avec le protocole SSH activé, vous pouvez vous connecter à votre conteneur en utilisant App Service Advanced Tools (Kudu). Pour faciliter la connexion à votre conteneur via SSH, Functions fournit une image de base pour laquelle SSH est déjà activé. 

### <a name="change-the-base-image"></a>Changer l’image de base

Dans votre fichier dockerfile, ajoutez la chaîne `-appservice` à l’image de base dans votre instruction `FROM`, ce qui, pour un projet JavaScript, se présente comme suit.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

Les différences entre les deux images de base autorisent les connexions SSH à votre conteneur. Ces différences sont détaillées dans le [tutoriel App Services](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Regénérer et redéployer l’image

Dans le dossier racine, exécutez à nouveau la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/), comme précédemment, remplacez `<docker-id>` par votre ID de compte Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Renvoyez (Push back) à Docker Hub l’image mise à jour.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

L’image mise à jour est redéployée dans votre application de fonction.

### <a name="connect-to-your-container-in-azure"></a>Se connecter à votre conteneur dans Azure

Dans le navigateur, accédez au point de terminaison `scm.` Advanced Tools (Kudu) suivant pour votre conteneur d’application de fonction, et remplacez `<app_name>` par le nom de votre application de fonction.

```
https://<app_name>.scm.azurewebsites.net/
```

Connectez-vous à votre compte Azure, puis sélectionnez l’onglet **SSH** pour créer une connexion SSH à votre conteneur.

Une fois la connexion établie, exécutez la commande `top` pour voir les processus en cours d’exécution. 

![Commande top Linux s’exécutant dans une session SSH.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Écrire dans le Stockage File d’attente

Functions vous permet de connecter des services Azure et d’autres ressources à des fonctions sans avoir à écrire votre propre code d’intégration. Ces *liaisons*, qui représentent l’entrée et la sortie, sont déclarées dans la définition de la fonction. Les données issues des liaisons sont fournies à la fonction en tant que paramètres. Un *déclencheur* est un type spécial de liaison d’entrée. Si une fonction ne peut avoir qu’un seul déclencheur, elle peut avoir plusieurs liaisons d’entrée et de sortie. Pour en savoir plus, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

Cette section vous montre comment intégrer votre fonction à une file d’attente de stockage Azure. La liaison de sortie que vous ajoutez à cette fonction écrit des données d’une requête HTTP dans un message en file d’attente.

### <a name="download-the-function-app-settings"></a>Télécharger les paramètres de l’application de fonction

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Activer les offres groupées d’extension

Comme vous utilisez une liaison de sortie de Stockage File d’attente, vous devez disposer de l’extension de liaisons de Stockage avant d’exécuter le projet. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript / Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

À l’exception des déclencheurs HTTP et de minuterie, les liaisons sont implémentées sous la forme de packages d’extension. Exécutez la commande [dotnet add package](/dotnet/core/tools/dotnet-add-package) suivante dans la fenêtre de terminal pour ajouter le package d’extension de Stockage à votre projet.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Lorsque vous utilisez Visual Studio, vous pouvez également utiliser le gestionnaire de package NuGet pour ajouter ce package.

---

À présent, vous pouvez ajouter une liaison de sortie de stockage à votre projet.

### <a name="add-an-output-binding"></a>Ajouter une liaison de sortie

Dans Functions, chaque type de liaison requiert la définition d’une `direction`, d’un `type` et d’un `name` unique dans le fichier function.json. La façon dont vous définissez ces attributs dépend du langage de votre application de fonction.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript / Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Ajouter le code qui utilise la liaison de sortie

Une fois que la liaison est définie, vous pouvez utiliser l’attribut `name` de la liaison pour y accéder en tant qu’attribut dans la signature de fonction. En utilisant une liaison de sortie, vous n’avez pas besoin de recourir au code du SDK Stockage Azure pour l’authentification, l’obtention d’une référence de file d’attente ou l’écriture de données. La liaison de sortie de file d’attente et le runtime Functions effectuent ces tâches.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Mettre à jour le conteneur hébergé

Dans le dossier racine, exécutez à nouveau la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/) et, cette fois-ci, mettez à jour la version dans la balise vers `v1.0.2`. Comme effectué précédemment, remplacez `<docker-id>` par votre ID de compte Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Renvoyez au dépôt l’image mise à jour.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Vérifier les mises à jour dans Azure

Utilisez la même URL que précédemment à partir du navigateur pour déclencher votre fonction. La même réponse doit s’afficher. Toutefois, cette fois-ci, la chaîne que vous transmettez comme paramètre `name` est écrite dans la file d’attente de stockage `outqueue`.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé correctement votre conteneur personnalisé sur une application de fonction dans Azure, apprenez-en davantage par la lecture des rubriques suivantes :

+ [Supervision des fonctions](functions-monitoring.md)
+ [Mise à l’échelle et options d’hébergement](functions-scale.md)
+ [Hébergement serverless basé sur Kubernetes](functions-kubernetes-keda.md)

[Azure portal]: https://portal.azure.com
