---
title: Exemple de script Azure CLI - Mapper un domaine personnalisé à une Function App | Microsoft Docs
description: Exemple de script Azure CLI - Mapper un domaine personnalisé à une Function App dans Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/26/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 7d3fc71bc53e85fa7555dbee5ee79b3f06f27fe8
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960336"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapper un domaine personnalisé à une Function App

Cet exemple de script crée une Function App avec les ressources associées, puis la mappe à `www.<yourdomain>`. Quand votre application de fonction est hébergée dans un [plan App Service](../functions-scale.md#app-service-plan), vous pouvez mapper un domaine personnalisé à l’aide d’un enregistrement CNAME ou d’un enregistrement A. Pour les applications de fonction dans un [plan Consommation](../functions-scale.md#consumption-plan), seule l’option CNAME est prise en charge.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez utiliser Azure CLI 2.0 ou une version ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Crée un compte de stockage requis par la Function App. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Crée un plan App Service requis pour mapper un domaine personnalisé. |
| [az functionapp create]() | Crée une Function App. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Mappe un domaine personnalisé à une Function App. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Functions dans la [documentation d’Azure Functions]().
