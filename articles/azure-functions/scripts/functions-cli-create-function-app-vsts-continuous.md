---
title: Créer une fonction déployée à partir de Visual Studio Team Services dans Azure | Microsoft Docs
description: Créer une Function App et déployer le code de fonction à partir de Visual Studio Team Services
services: functions
keywords: ''
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 1b54cfebd3ae36fc8025aeb4ea9c91d336bc5343
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988950"
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>Créer une application de fonction et déployer le code de fonction à partir de Visual Studio Team Services

Cette rubrique vous explique comment utiliser Azure Functions pour créer une application de fonction [serverless](https://azure.microsoft.com/overview/serverless-computing/) à l’aide du [plan de consommation](../functions-scale.md#consumption-plan). L’application de fonction, qui est un conteneur pour vos fonctions, est déployée en continu à partir d’un référentiel de Visual Studio Team Services (VSTS). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Dans le cadre de cette rubrique, vous avez besoin des éléments suivants :

* Un référentiel VSTS contenant votre projet d’application de fonction et pour lequel vous disposez des autorisations d’administration nécessaires
* Un [jeton d’accès personnel (PAT)](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) permettant d’accéder à votre référentiel VSTS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous préférez utiliser Azure CLI localement, vous devez installer et utiliser la version 2.0 ou une version ultérieure. Pour déterminer la version Azure CLI, exécutez `az --version`. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Cet exemple crée une Function App Azure et déploie le code de la fonction à partir de Visual Studio Team Services.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un compte de stockage, une application de fonction et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Crée le compte de stockage requis par l’application de fonction. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Crée une application de fonction dans le [plan Consommation](../functions-scale.md#consumption-plan) serverless. |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Associe une Function App à un référentiel Git ou Mercurial. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
