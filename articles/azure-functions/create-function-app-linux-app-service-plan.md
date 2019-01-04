---
title: Créer une application de fonction dans un plan App Service sur Linux
description: Découvrez comment créer une application de fonction qui s’exécute sur Linux dans un plan App Service, à l’aide d’Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52854590"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Créer une application de fonction dans un plan App Service sur Linux (préversion)

Azure Functions vous permet d’héberger vos fonctions sur Linux dans un conteneur Azure App Service par défaut. Cet article vous montre comment utiliser Azure CLI pour créer une application de fonction Linux dans Azure, qui s’exécute dans un [plan App Service](functions-scale.md#app-service-plan). Vous pouvez également [apporter votre propre conteneur personnalisé](functions-create-function-linux-custom-image.md). L’hébergement dans Linux est en préversion.

Dans un plan App Service, vous êtes responsable de la mise à l’échelle de votre application de fonction. Pour tirer parti des fonctionnalités serverless d’Azure Functions, vous pouvez également héberger vos fonctions sur Linux, dans un [plan Consommation](functions-scale.md#consumption-plan).

Vous pouvez suivre les étapes ci-dessous en utilisant un ordinateur Mac, Windows ou Linux.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

+ Un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0.21 ou ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Créer un plan App Service Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Créer une application de fonction sur Linux

Vous devez disposer d’une application de fonction pour héberger l’exécution de vos fonctions sur Linux. L’application de fonction fournit un environnement pour l’exécution de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Créez une application de fonction à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create) dans le cadre d’un plan App Service Linux.

Dans la commande suivante, indiquez un nom d’application de fonction unique là où se trouve l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. La valeur `<app_name>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous devez également définir le runtime `<language>` pour votre application de fonction à partir de `dotnet` (C#), `node` (JavaScript) ou `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Après la création de l’application de fonction et son déploiement, Azure CLI affiche des informations semblables à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
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

`myAppServicePlan` étant un plan Linux, l’image docker intégrée est utilisée pour créer le conteneur qui exécute l’application de fonction sur Linux.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>Étapes suivantes

Cet article vous montre comment créer une application de fonction hébergée sur Linux dans Azure. Vous pouvez désormais [déployer un projet de fonction](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest) dans cette application de fonction. Vous pouvez utiliser Azure Functions Core Tools pour [créer un projet Functions](functions-run-local.md#create-a-local-functions-project) sur votre ordinateur local et le déployer dans votre nouvelle application de fonction Linux.  

> [!div class="nextstepaction"] 
> [Coder et tester Azure Functions localement](functions-run-local.md)
