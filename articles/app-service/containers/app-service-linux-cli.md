---
title: Gérer des conteneurs Linux personnalisés avec l’interface CLI
description: Apprenez à gérer les conteneurs Linux personnalisés dans Azure App Service à partir de la ligne de commande. Automatisez le provisionnement ou la maintenance d’applications.
keywords: azure app service, application web, cli, linux, oss
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255919"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Gérer les applications Web App for Containers à l’aide d’Azure CLI

À l’aide des commandes présentées dans cet article, vous pouvez créer et gérer une application Web App pour conteneurs à l’aide de l’interface de ligne de commande Azure.
Vous pouvez commencer à utiliser la nouvelle version de la CLI de deux manières :

* [Installation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) sur votre ordinateur.
* Utilisation d’[Azure Cloud Shell (version préliminaire)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Créer un plan App Service Linux

Pour créer un plan App Service Linux, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Créer une application web de conteneur Docker personnalisé

Pour créer une application web et la configurer pour utiliser un conteneur Docker personnalisé, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Activer la journalisation du conteneur Docker

Pour activer la journalisation du conteneur Docker, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Changer le conteneur Docker personnalisé pour une application Web App for Containers existante

Pour modifier une application créée précédemment, de l’image Docker actuelle en une nouvelle image, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Utilisation d’images Docker d’un registre privé

Vous pouvez configurer votre application pour utiliser des images d’un registre privé. Vous devez fournir l’URL de votre registre, le nom d’utilisateur et le mot de passe. Ceci peut être effectué à l’aide de la commande suivante :

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Activer les déploiements continus d’images Docker personnalisées

Avec la commande suivante, vous pouvez activer la fonctionnalité CD et obtenir l’URL webhook. Cette URL peut être utilisée pour configurer votre référentiel DockerHub ou Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Créer une application Web App for Containers à l’aide de l’une de nos infrastructures runtime intégrées

Pour créer une application PHP 5.6 Web App for Containers, vous pouvez utiliser la commande suivante.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Changer la version de l’infrastructure pour une application Web App for Containers existante

Pour modifier une application créée précédemment, de la version d’infrastructure actuelle en Node.js 6.11, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configurer des déploiements Git pour votre application web

Pour configurer des déploiements Git pour votre application, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce qu’Azure App Service sur Linux ?](app-service-linux-intro.md)
* [Installation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (version préliminaire)](../../cloud-shell/overview.md)
* [Configurer des environnements intermédiaires dans Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Déploiement continu avec Web App pour conteneurs](app-service-linux-ci-cd.md)
