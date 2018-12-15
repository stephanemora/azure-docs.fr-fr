---
title: Créer une fonction dans Azure déployée à partir d’Azure DevOps | Microsoft Docs
description: Créer une application de fonction Function App et déployer le code de fonction à partir d’Azure DevOps
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: azure-functions
ms.custom: mvc
ms.openlocfilehash: 17e33e3c0e6b06266025a7f0e6403789c9468a16
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957473"
---
# <a name="create-a-function-app-and-deploy-function-code-from-azure-devops"></a>Créer une application de fonction et déployer le code de fonction à partir d’Azure DevOps

Cette rubrique vous explique comment utiliser Azure Functions pour créer une application de fonction [serverless](https://azure.microsoft.com/solutions/serverless/) à l’aide du [plan de consommation](../functions-scale.md#consumption-plan). L’application de fonction, qui est un conteneur pour vos fonctions, est déployée en continu à partir d’un dépôt Azure DevOps. 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Dans le cadre de cette rubrique, vous avez besoin des éléments suivants :

* Un dépôt Azure DevOps qui contient votre projet d’application de fonction, et pour lequel vous disposez d’autorisations d’administration.
* Un [PAT (jeton d’accès personnel)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) qui permet d’accéder au dépôt Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous préférez utiliser Azure CLI localement, vous devez installer et utiliser la version 2.0 ou une version ultérieure. Pour déterminer la version Azure CLI, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Cet exemple crée une application de fonction Azure et déploie le code de fonction à partir d’Azure DevOps.

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
