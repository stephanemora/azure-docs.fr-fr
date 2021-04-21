---
title: Créer une application de fonction avec un stockage connecté - Azure CLI
description: Exemple de script Azure CLI - Créer une fonction Azure qui se connecte à un Stockage Azure
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 539c3a7dd95045b2e569dbb339be0e5a0c845902
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786277"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Créer une application de fonction avec une connexion au compte de stockage nommée 

Cet exemple de script Azure Functions crée une application de fonction et connecte la fonction à un compte de stockage Azure. Le paramètre de l’application créée contenant la connexion peut être utilisé avec [une liaison ou un déclencheur de stockage](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ce tutoriel nécessite l’interface Azure CLI version 2.0 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="sample-script"></a>Exemple de script

Cet exemple crée une Function App Azure et ajoute la chaîne de connexion de stockage à un paramètre d’application.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources avec un emplacement. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Créez un compte de stockage. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Crée une application de fonction dans le [plan Consommation](../consumption-plan.md) serverless. |
| [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) | Obtient la chaîne de connexion pour le compte. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Définit la chaîne de connexion en tant que paramètre d’application dans l’application de fonction. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
