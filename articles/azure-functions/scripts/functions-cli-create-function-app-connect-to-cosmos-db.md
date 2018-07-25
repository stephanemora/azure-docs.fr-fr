---
title: Créer une fonction Azure qui se connecte à une base de données Azure Cosmos DB | Documents Microsoft
description: Exemple de script Azure CLI - Créer une fonction Azure qui se connecte à une base de données Azure Cosmos DB
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: ''
tags: functions
ms.assetid: ''
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 9154cef897d38e617c2f9dccdc8a47fe1af72104
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989459"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Créer une fonction Azure Functions qui se connecte à une base de données Azure Cosmos DB

Cet exemple de script Azure Functions crée une application de fonction et connecte la fonction à une base de données Azure Cosmos DB. Le paramètre de l’application créée contenant la connexion peut être utilisé avec une [liaison ou un déclencheur Azure Cosmos DB](..\functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous utilisez l’interface de ligne de commande en local, veillez à exécuter Azure CLI 2.0 ou une version plus récente. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Cet exemple crée une Function App Azure et ajoute un point de terminaison et une clé d’accès Cosmos DB aux paramètres d’application.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Crée un groupe de ressources avec un emplacement. |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Créez un compte de stockage. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Crée une application de fonction dans le [plan Consommation](../functions-scale.md#consumption-plan) serverless. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az-cosmosdb-create) | Crée une base de données Azure Cosmos DB. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).




