---
title: Créer une application de fonction avec Azure Cosmos DB - Azure CLI
description: Exemple de script Azure CLI - Créer une fonction Azure qui se connecte à une base de données Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 261ede1493b93ccd3cfed19125bbabc3be871698
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786286"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Créer une fonction Azure Functions qui se connecte à une base de données Azure Cosmos DB

Cet exemple de script Azure Functions crée une application de fonction et connecte la fonction à une base de données Azure Cosmos DB. Le paramètre de l’application créée contenant la connexion peut être utilisé avec une [liaison ou un déclencheur Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ce tutoriel nécessite l’interface Azure CLI version 2.0 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Cet exemple crée une Function App Azure et ajoute un point de terminaison et une clé d’accès Cosmos DB aux paramètres d’application.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources avec un emplacement. |
| [az storage accounts create](/cli/azure/storage/account#az_storage_account_create) | Créez un compte de stockage. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Crée une application de fonction dans le [plan Consommation](../consumption-plan.md) serverless. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Crée une base de données Azure Cosmos DB. |
| [az cosmosdb show](/cli/azure/cosmosdb#az_cosmosdb_show)| Obtient la connexion au compte de base de données. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az_cosmosdb_list_keys)| Obtient les clés pour la base de données. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Définit la chaîne de connexion en tant que paramètre d’application dans l’application de fonction. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
