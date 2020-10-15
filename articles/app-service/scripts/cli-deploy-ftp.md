---
title: 'Interface CLI : Déployer des fichiers d’application avec FTP'
description: Découvrez comment utiliser Azure CLI pour automatiser le déploiement et la gestion de votre application App Service. Cet exemple montre comment créer une application et déployer des fichiers avec FTP.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: cbc473fb10cadc5fd6da1ea88fbb767249dc7dfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88960647"
---
# <a name="create-an-app-service-app-and-deploy-files-with-ftp-using-azure-cli"></a>Créer une application App Service et déployer des fichiers par FTP à l’aide d’Azure CLI

Cet exemple de script crée une application dans App Service avec les ressources associées, puis déploie une page HTML statique par FTP. Pour le chargement FTP, le script utilise [cURL](https://en.wikipedia.org/wiki/CURL) à titre d’exemple. Vous pouvez utiliser l’outil FTP de votre choix pour charger vos fichiers.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez utiliser Azure CLI 2.0 ou version ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create an app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script 

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Crée un plan App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Crée une application App Service. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment?view=azure-cli-latest#az-webapp-deployment-list-publishing-profiles) | Affiche les détails des profils de déploiement d’applications disponibles. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../samples-cli.md).