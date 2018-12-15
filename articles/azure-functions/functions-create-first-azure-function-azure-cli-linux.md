---
title: Créer votre première fonction sur Linux dans Azure
description: Apprenez à créer votre première fonction hébergée sur Linux dans Azure à l’aide d’Azure CLI et d’Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: b6df653f89f05a9b253ecea102ed8310ff2a53b7
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438274"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Créer votre première fonction hébergée sur Linux à l’aide de Core Tools et d’Azure CLI (préversion)

Azure Functions vous permet d’exécuter votre code dans un environnement Linux [sans serveur](https://azure.com/serverless) et sans avoir à créer une machine virtuelle ou à publier une application web au préalable. L’hébergement Linux est actuellement en préversion et requiert le [runtime Functions 2.0](functions-versions.md). Pour en savoir plus sur les considérations relatives à la préversion pour l’exécution de vos applications de fonction sur Linux, consultez [cet article sur les fonctions sur Linux ](https://aka.ms/funclinux).

Cet article de démarrage rapide vous guide tout au long de l’utilisation d’Azure CLI pour créer votre première application de fonction exécutée sur Linux. Le code de fonction est créé en local, puis déployé sur Azure à l’aide d’[Azure Functions Core Tools](functions-run-local.md).

Les étapes suivantes sont prises en charge sur un ordinateur Mac, Windows ou Linux. Cet article vous montre comment créer des fonctions en JavaScript ou C#. Pour savoir comment créer des fonctions Python, consultez [Créer votre première fonction Python à l’aide de Core Tools et d’Azure CLI (préversion)](functions-create-first-function-python.md).

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
+ `python` : crée un projet Python. Pour plus d’informations sur les fonctions Python, consultez [Démarrage rapide Python](functions-create-first-function-python.md).

Lorsque la commande s’exécute, une sortie similaire à la suivante s’affiche :

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Créer un plan App Service Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Créer une application de fonction Linux dans Azure

Vous devez disposer d’une application de fonction pour héberger l’exécution de vos fonctions sur Linux. L’application de fonction fournit un environnement sans serveur pour l’exécution de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Créez une application de fonction exécutée sur Linux à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

Dans la commande suivante, utilisez un nom d’application de fonction unique là où se trouve l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. `<app_name>` représente également le domaine DNS par défaut pour l’application de fonction. Ce nom doit être unique parmi toutes les applications dans Azure. Vous devez également définir le runtime `<language>` pour votre application de fonction à partir de `dotnet` (C#), `node` (JavaScript) ou `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

> [!NOTE]
> Si vous disposez d’un groupe de ressources existant nommé `myResourceGroup` avec une application App Service n’étant pas hébergée sur Linux, vous devez utiliser un autre groupe de ressources. Vous ne pouvez pas héberger des applications Windows et Linux dans le même groupe de ressources.  

Une fois que l’application de fonction est créée, le message suivant s’affiche :

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Vous pouvez désormais publier votre projet sur la nouvelle application de fonction dans Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu comment héberger votre application de fonction dans un conteneur Azure App Service par défaut. Vous pouvez également héberger vos fonctions sur Linux dans votre propre conteneur personnalisé.

> [!div class="nextstepaction"]
> [Créer une fonction sur Linux en utilisant une image personnalisée](functions-create-function-linux-custom-image.md)
