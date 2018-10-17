---
title: Créer votre première fonction à l’aide d’Azure CLI
description: Apprenez à créer votre première fonction Azure pour une exécution serverless à l’aide d’Azure CLI et d’Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: f65ff3e38e3768dccf04e9eaa4345ffcea4daa9e
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451118"
---
# <a name="create-your-first-function-from-the-command-line"></a>Créer votre première fonction à partir de la ligne de commande

Cette rubrique de démarrage rapide vous guide dans la création de votre première fonction à partir de la ligne de commande ou d’un terminal. Utilisez Azure CLI pour créer une application de fonction, qui est l’infrastructure [sans serveur](https://azure.microsoft.com/overview/serverless-computing/) hébergeant votre fonction. Le projet de code de fonction est généré à partir d’un modèle à l’aide [d’Azure Functions Core Tools](functions-run-local.md), qui est également utilisé pour déployer le projet d’application de fonction dans Azure.

Vous pouvez suivre les étapes ci-dessous en utilisant un ordinateur Mac, Windows ou Linux.

## <a name="prerequisites"></a>Prérequis

Avant d’exécuter cet exemple, vous devez disposer des éléments suivants :

+ Installez [Azure Core Tools version 2.x](functions-run-local.md#v2).

+ Installez [Azure CLI]( /cli/azure/install-azure-cli). Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Exécutez `az --version` pour trouver la version qui est à votre disposition. Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/bash).

+ Un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Créer le projet d’application de fonction local

Exécutez la commande suivante à partir de la ligne de commande pour créer un projet d’application de fonction dans le dossier `MyFunctionProj` du répertoire local actif. Un référentiel GitHub est également créé dans `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Lorsque vous y êtes invité, utilisez les touches de direction pour sélectionner un runtime worker parmi les choix de langage suivants :

+ `dotnet` : crée un projet de bibliothèque de classes .NET (.csproj).
+ `node` : créer un projet JavaScript.

Lorsque la commande s’exécute, une sortie similaire à la suivante s’affiche :

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>Créer une fonction

La commande suivante accède au nouveau projet et crée une fonction déclenchée via HTTP nommée `MyHtpTrigger`.

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

Lorsque la commande s’exécute, une sortie similaire à la suivante s’affiche, qui est une fonction JavaScript :

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>Modifier la fonction

Par défaut, le modèle crée une fonction qui requiert une clé de fonction pour effectuer des requêtes. Pour simplifier le test de la fonction dans Azure, vous devez mettre à jour la fonction pour autoriser l’accès anonyme. La façon dont vous apportez cette modification dépend du langage du projet de fonctions.

### <a name="c"></a>C\#

Ouvrez le fichier de code MyHttpTrigger.cs qui correspond à votre nouvelle fonction et mettez à jour l’attribut **AuthorizationLevel** dans la définition de fonction sur la valeur `anonymous` et enregistrez vos modifications.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Ouvrez le fichier function.json pour votre nouvelle fonction, ouvrez-le dans un éditeur de texte, mettez à jour la propriété **authLevel** dans **bindings.httpTrigger** sur `anonymous`, puis enregistrez vos modifications.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
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
```

Vous pouvez maintenant appeler la fonction dans Azure sans avoir à fournir la clé de fonction. La clé de fonction n’est jamais requise lors de l’exécution locale.

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

La commande suivante démarre l’application de fonction. L’application s’exécute à l’aide du même runtime Azure Functions que celui qui se trouve dans Azure.

```bash
func host start --build
```

L’option `--build` est requise pour compiler les projets C#. Vous n’avez pas besoin de cette option pour un projet JavaScript.

Lorsque l’hôte Functions démarre, il écrit une sortie similaire à la suivante, tronquée pour une meilleure lisibilité :

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copiez l’URL de votre fonction `HTTPTrigger` à partir de la sortie du runtime et collez-la dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. Ce qui suit montre la réponse dans le navigateur à la requête GET retournée par la fonction locale :

![Tester localement dans le navigateur](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

Maintenant que vous avez exécuté votre fonction localement, vous pouvez créer l’application de fonction et d’autres ressources nécessaires dans Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. La Function App fournit un environnement d’exécution sans serveur de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Créez une Function App à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

Dans la commande suivante, indiquez un nom d’application de fonction unique là où se trouve l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. La valeur `<app_name>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Le paramètre _deployment-source-url_ est un dépôt d’exemples dans GitHub qui contient une fonction « Hello World » déclenchée par HTTP.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

La définition du paramètre _consumption-plan-location_ signifie que l’application de fonction est hébergée dans un plan d’hébergement Consommation. Dans ce plan serverless, les ressources sont ajoutées dynamiquement selon vos fonctions, et vous ne payez que lors de l’exécution des fonctions. Pour plus d’informations, consultez [Choisir le plan d’hébergement approprié](functions-scale.md).

Une fois la Function App créée, Azure CLI affiche des informations semblables à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>Configurer l’application de fonction

Core Tools version 2.x crée des projets à l’aide de modèles pour le runtime Azure Functions 2.x. Par conséquent, vous devez vous assurer que le runtime version 2.x est utilisé dans Azure. Définir le paramètre d’application `FUNCTIONS_WORKER_RUNTIME` sur `~2` épingle l’application de fonction à la dernière version 2.x. Configurez les paramètres d’application à l’aide de la commande [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

Dans la commande Azure CLI suivante, « <app_name> » est le nom de votre application de fonction.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
