---
title: Créer votre première fonction à l’aide d’Azure CLI
description: Apprenez à créer votre première fonction Azure pour une exécution serverless à l’aide d’Azure CLI et d’Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 7ff57519cfbb99fa705aff6c970951730c501f3e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846451"
---
# <a name="create-your-first-function-from-the-command-line"></a>Créer votre première fonction à partir de la ligne de commande

Cette rubrique de démarrage rapide vous guide dans la création de votre première fonction à partir de la ligne de commande ou d’un terminal. Utilisez Azure CLI pour créer une application de fonction, qui est l’infrastructure [sans serveur](https://azure.microsoft.com/solutions/serverless/) hébergeant votre fonction. Le projet de code de fonction est généré à partir d’un modèle à l’aide [d’Azure Functions Core Tools](functions-run-local.md), qui est également utilisé pour déployer le projet d’application de fonction dans Azure.

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

Lorsque vous y êtes invités, sélectionnez un worker runtime parmi les choix de langues suivants :

+ `dotnet` : crée un projet de bibliothèque de classes .NET (.csproj).
+ `node` : créer un projet JavaScript.

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

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. La Function App fournit un environnement d’exécution sans serveur de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Créez une Function App à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

Dans la commande suivante, indiquez un nom d’application de fonction unique là où se trouve l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. La valeur `<app_name>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous devez également définir le runtime `<language>` pour votre application de fonction à partir de `dotnet` (C#) ou `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name> --runtime <language> 
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

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

