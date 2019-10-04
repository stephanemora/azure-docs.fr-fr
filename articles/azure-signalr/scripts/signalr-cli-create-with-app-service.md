---
title: Exemple de script Azure CLI - Créer un service SignalR avec App Service
description: Exemple de script Azure CLI - Créer un service SignalR avec App Service
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: d0f0747aa393475265be4aeb9ca05000fbd5b97b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565754"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Créer un service SignalR avec App Service

Cet exemple de script crée une nouvelle ressource de service Azure SignalR, utilisée pour transmettre les mises à jour en temps réel de contenus aux clients. Ce script ajoute également une application web et un plan App Service pour héberger une application web ASP.NET Core qui utilise le service SignalR. L’application web est configurée avec un paramètre d’application nommé *AzureSignalRConnectionString* pour se connecter à la nouvelle ressource de service SignalR.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Ce script utilise l’extension *signalr* pour Azure CLI. Exécutez la commande suivante afin d’installer l’extension *signalr* pour l’interface Azure CLI avant d’utiliser cet exemple de script :

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Consignez le nom généré pour le nouveau groupe de ressources. Il est affiché dans la sortie. Vous utiliserez ce nom de groupe de ressources au moment de la suppression de l’ensemble des ressources du groupe.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Chaque commande du tableau renvoie à une documentation spécifique. Ce script utilise les commandes suivantes :

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Crée une ressource Azure SignalR Service. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Répertorie les clés qui seront utilisées par votre application lors de la transmission des mises à jour en temps réel de contenus avec SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Crée un plan Azure App Service pour l’hébergement des applications web. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Crée une application web Azure utilisant le plan d’hébergement App Service. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Ajoute un nouveau paramètre d’application pour l’application web. Ce paramètre d’application est utilisé pour stocker la chaîne de connexion SignalR. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Des exemples supplémentaires de script de l’interface CLI du service Azure SignalR sont disponibles dans la [documentation sur le service Azure SignalR](../signalr-reference-cli.md).
