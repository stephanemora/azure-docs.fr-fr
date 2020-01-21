---
title: Créer votre première fonction à l’aide d’Azure CLI
description: Apprenez à créer votre première fonction Azure pour une exécution serverless à l’aide d’Azure CLI et d’Azure Functions Core Tools.
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 222e4a98974a1af40ff860cfc4fdb246d9c97bca
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769385"
---
# <a name="quickstart-create-your-first-function-from-the-command-line-using-azure-cli"></a>Démarrage rapide : Créer votre première fonction à partir de la ligne de commande à l’aide d’Azure CLI

Cette rubrique de démarrage rapide vous guide dans la création de votre première fonction à partir de la ligne de commande ou d’un terminal. Utilisez Azure CLI pour créer une application de fonction, qui est l’infrastructure [sans serveur](https://azure.microsoft.com/solutions/serverless/) hébergeant votre fonction. Le projet de code de fonction est généré à partir d’un modèle à l’aide [d’Azure Functions Core Tools](functions-run-local.md), qui est également utilisé pour déployer le projet d’application de fonction dans Azure.

Vous pouvez suivre les étapes ci-dessous en utilisant un ordinateur Mac, Windows ou Linux.

## <a name="prerequisites"></a>Conditions préalables requises

Avant d’exécuter cet exemple, vous devez disposer des éléments suivants :

+ [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 ou ultérieure.

+ Installez [Azure CLI](/cli/azure/install-azure-cli). Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Exécutez `az --version` pour trouver la version qui est à votre disposition. Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/bash).

+ Un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Activer les offres groupées d’extension

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. La Function App fournit un environnement d’exécution sans serveur de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement, la mise à l’échelle et le partage des ressources. Créez une Function App à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

Dans la commande suivante, indiquez un nom d’application de fonction unique là où se trouve l’espace réservé `<APP_NAME>`, et le nom du compte de stockage pour `<STORAGE_NAME>`. La valeur `<APP_NAME>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous devez également définir le runtime `<language>` pour votre application de fonction à partir de `dotnet` (C#) ou `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
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

