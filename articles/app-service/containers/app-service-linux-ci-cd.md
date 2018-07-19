---
title: Déploiement continu à partir d’un registre de conteneurs Docker avec Web App pour conteneurs - Azure | Microsoft Docs
description: Comment configurer le déploiement continu à partir d’un registre de conteneurs Docker dans Web App pour conteneurs.
keywords: azure app service, linux, docker, acr, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130959"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Déploiement continu avec Web App pour conteneurs

Dans ce didacticiel, vous allez configurer le déploiement continu d’une image conteneur personnalisée à partir des référentiels [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) managés ou du [Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Activer la fonctionnalité de déploiement continu

Activez la fonctionnalité de déploiement continu avec [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et en exécutant la commande suivante :

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

Dans le [portail Azure](https://portal.azure.com/), sélectionnez l’option **App Service** sur le côté gauche de la page.

Sélectionnez le nom de l’application pour laquelle vous souhaitez configurer le déploiement continu Docker Hub.

Dans la page **Conteneur Docker**, sélectionnez **Activé**, puis sélectionnez **Enregistrer** pour activer le déploiement continu.

![Capture d’écran du paramètre d’application](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Préparer l’URL du webhook

Vous pouvez obtenir l’URL du Webhook avec [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et en exécutant la commande suivante :

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Prenez note de l’URL du webhook. Vous en aurez besoin dans la prochaine section.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Vous pouvez obtenir votre `publishingusername` et `publishingpwd` en téléchargeant le profil de publication de l’application web à l’aide du portail Azure.

![Capture d’écran de l’ajout de webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Appeler un webhook

Pour ajouter un webhook, suivez les étapes décrites dans ces guides :

- [Azure Container Registry](../../container-registry/container-registry-webhook.md) à l’aide de l’URL du webhook
- [Webhooks pour Docker Hub](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure App Service sur Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Créer une application web .NET Core dans App Service sur Linux](quickstart-dotnetcore.md)
* [Créer une application Web Ruby dans App Service sur Linux](quickstart-ruby.md)
* [Déployer une application Web Docker/Go dans Web App pour conteneurs](quickstart-docker-go.md)
* [Questions fréquentes (FAQ) sur Azure App Service sur Linux](./app-service-linux-faq.md)
* [Gérer Web App pour conteneurs à l’aide d’Azure CLI](./app-service-linux-cli.md)
