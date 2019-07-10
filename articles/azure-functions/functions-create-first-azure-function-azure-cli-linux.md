---
title: Créer votre première fonction sur Linux dans Azure
description: Apprenez à créer votre première fonction hébergée sur Linux dans Azure à l’aide d’Azure CLI et d’Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 03/12/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc, fasttrack-edit
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: 857646bb1b9b317f1e51218d258616e775056b43
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442286"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Créer votre première fonction hébergée sur Linux à l’aide de Core Tools et d’Azure CLI (préversion)

Azure Functions vous permet d’exécuter votre code dans un environnement Linux [sans serveur](https://azure.com/serverless) et sans avoir à créer une machine virtuelle ou à publier une application web au préalable. L’hébergement Linux nécessite [le runtime Functions 2.0](functions-versions.md). La prise en charge de l’exécution d’une application de fonction sur Linux dans un [plan de consommation](functions-scale.md#consumption-plan) serverless est actuellement en préversion. Pour en savoir plus, voir [cet article de considérations relatives à la préversion](https://aka.ms/funclinux).

Cet article de démarrage rapide vous guide tout au long de l’utilisation d’Azure CLI pour créer votre première application de fonction exécutée sur Linux. Le code de fonction est créé en local, puis déployé sur Azure à l’aide d’[Azure Functions Core Tools](functions-run-local.md).

Les étapes suivantes sont prises en charge sur un ordinateur Mac, Windows ou Linux. Cet article vous montre comment créer des fonctions en JavaScript ou C#. Pour savoir comment créer des fonctions Python, consultez [Créer votre première fonction Python à l’aide de Core Tools et d’Azure CLI (préversion)](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Prérequis

Avant d’exécuter cet exemple, vous devez disposer des éléments suivants :

- Installer [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 ou ultérieure.

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
+ `node` : créer un projet JavaScript ou TypeScript. Lorsque vous y êtes invité, choisissez `JavaScript`.
+ `python` : crée un projet Python. Pour plus d’informations sur les fonctions Python, consultez [Démarrage rapide Python](functions-create-first-function-python.md).

Lorsque la commande s’exécute, une sortie similaire à la suivante s’affiche :

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Utilisez la commande suivante pour accéder au nouveau dossier du projet `MyFunctionProj`.

```bash
cd MyFunctionProj
```

## <a name="enable-extension-bundles"></a>Activer les offres groupées d’extension

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Créer une application de fonction Linux dans Azure

Vous devez disposer d’une application de fonction pour héberger l’exécution de vos fonctions sur Linux. L’application de fonction fournit un environnement sans serveur pour l’exécution de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Créez une application de fonction exécutée sur Linux à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

Dans la commande suivante, utilisez un nom d’application de fonction unique là où se trouve l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. `<app_name>` représente également le domaine DNS par défaut pour l’application de fonction. Ce nom doit être unique parmi toutes les applications dans Azure. Vous devez également définir le runtime `<language>` pour votre application de fonction à partir de `dotnet` (C#), `node` (JavaScript/TypeScript) ou `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Une fois que l’application de fonction est créée, le message suivant s’affiche :

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Vous pouvez désormais publier votre projet sur la nouvelle application de fonction dans Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]