---
title: Créer une application de fonction dans un plan Premium - Azure CLI
description: Créer une application de fonction dans un plan Premium scalable dans Azure avec l’interface de ligne de commande Azure
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.openlocfilehash: 3587e5b5e46867ff3abe97b3593c9732be3afa22
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2020
ms.locfileid: "85117248"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>Créer une application de fonction dans un plan Premium - Azure CLI

Cet exemple de script Azure Functions crée une application de fonction, qui constitue un conteneur pour vos fonctions. L’application de fonction qui est créée utilise un [plan Premium scalable](../functions-premium-plan.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0 ou une version ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Ce script crée une application de fonction avec un [plan Premium](../functions-premium-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Chaque commande du tableau renvoie à une documentation spécifique. Ce script utilise les commandes suivantes :

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crée un compte de stockage Azure. |
| [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Crée un plan Premium dans une [référence SKU](../functions-premium-plan.md#available-instance-skus) spécifique. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crée une application de fonction dans le plan App Service. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
