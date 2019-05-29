---
title: Exemple de script Azure CLI - Mapper un domaine personnalisé à une Function App | Microsoft Docs
description: Exemple de script Azure CLI - Mapper un domaine personnalisé à une Function App dans Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 790d095cfb1b59aed1b9014fc474f9ad6e1b3328
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131300"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapper un domaine personnalisé à une Function App

Cet exemple de script crée une application de fonction dans un plan App Service, puis la mappe à un domaine personnalisé que vous indiquez. Quand votre application de fonction est hébergée dans un [plan Premium](../functions-scale.md#premium-plan-public-preview) ou un [plan App Service](../functions-scale.md#app-service-plan), vous pouvez mapper un domaine personnalisé à l’aide d’un enregistrement CNAME ou d’un enregistrement A. Pour les applications de fonction dans un [plan Consommation](../functions-scale.md#consumption-plan), seule l’option CNAME est prise en charge. Cet exemple crée un plan App Service et requiert le mappage d’un enregistrement A au domaine. 

Pour exécuter cet exemple de script, vous devez avoir déjà configuré un enregistrement A dans votre domaine personnalisé qui pointe vers le nom de domaine par défaut de votre application web. Pour plus d’informations, consultez [Map custom domain instructions for Azure App Service](https://aka.ms/appservicecustomdns) (Mapper des instructions de domaine personnalisé pour Azure App Service). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez utiliser Azure CLI 2.0 ou une version ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Crée un compte de stockage requis par la Function App. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Crée un plan App Service requis pour mapper un domaine personnalisé. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Crée une application de fonction dans le plan App Service. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Mappe un domaine personnalisé à une Function App. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
