---
title: 'Interface CLI : Créer une application ASP.NET Core à partir d’ACR'
description: Découvrez comment utiliser Azure CLI pour automatiser le déploiement et la gestion de votre application App Service. Cet exemple montre comment créer une application Linux ASP.NET Core à partir d’ACR.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/13/2018
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 4b60bd15785803cb5e86d64d331d946e920187f4
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109751758"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>Créer une application ASP.NET Core dans un conteneur Docker au sein d’App Service à partir d’Azure Container Registry

Cet exemple de script crée un groupe de ressources, un plan App Service Linux et une application. Il déploie ensuite une application ASP.NET Core à l’aide d’un conteneur Docker de Azure Container Registry.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Vous avez besoin d’Azure CLI version 2.0.52 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une application App Service et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Crée un plan App Service. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Crée une application App Service. |
| [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set) | Définit le conteneur Docker pour l’application App Service. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../samples-cli.md).
