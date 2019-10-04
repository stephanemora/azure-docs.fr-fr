---
title: Exemple de script Azure CLI - Lier un certificat SSL personnalisé à une application | Microsoft Docs
description: Exemple de script Azure CLI - Lier un certificat SSL personnalisé à une application
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 89483c6396ea344a035ec22bf79e2f90dada4c96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136913"
---
# <a name="bind-a-custom-ssl-certificate-to-an-app-service-app-using-cli"></a>Lier un certificat SSL personnalisé à une application App Service à l’aide de l’interface CLI

Cet exemple de script crée une application dans App Service avec ses ressources associées, puis lie le certificat SSL d’un nom de domaine personnalisé à celle-ci. Pour cet exemple, vous aurez besoin des éléments suivants :

* Un accès à la page de configuration DNS du Registre de votre domaine.
* Un fichier .PFX valide et son mot de passe pour le certificat SSL que vous voulez charger et lier.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez utiliser Azure CLI 2.0 ou version ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Crée un plan App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Crée une application App Service. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | Mappe un domaine personnalisé à une application App Service. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-upload) | Charge un certificat SSL sur une application App Service. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-bind) | Lie un certificat SSL chargé à une application App Service. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../samples-cli.md).
