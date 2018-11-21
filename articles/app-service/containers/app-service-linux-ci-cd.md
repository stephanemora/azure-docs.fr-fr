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
ms.date: 11/08/2018
ms.author: msangapu;yili
ms.openlocfilehash: b26366edddc223b842cc5d38473bda42422f1840
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298528"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Déploiement continu avec Web App pour conteneurs

Dans ce didacticiel, vous allez configurer le déploiement continu d’une image conteneur personnalisée à partir des référentiels [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) managés ou du [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Activer le déploiement continu avec ACR

![Capture d’écran du webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez l’option **App Service** sur le côté gauche de la page.
3. Sélectionnez le nom de l’application pour laquelle vous souhaitez configurer le déploiement continu.
4. Dans la page **Paramètres de conteneur**, sélectionnez **Conteneur unique**.
5. Sélectionnez **Azure Container Registry**.
6. Sélectionnez **Déploiement continu > Activé**.
7. Sélectionnez **Enregistrer** pour activer le déploiement continu.

## <a name="use-the-acr-webhook"></a>Utiliser le webhook ACR

Une fois que le déploiement continu a été activé, vous pouvez afficher le webhook qui vient d’être créé sur votre page de webhooks Azure Container Registry.

![Capture d’écran du webhook ACR](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Dans votre registre Container Registry, cliquez sur Webhooks pour afficher les webhooks actifs.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Activer le déploiement continu avec Docker Hub (facultatif)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez l’option **App Service** sur le côté gauche de la page.
3. Sélectionnez le nom de l’application pour laquelle vous souhaitez configurer le déploiement continu.
4. Dans la page **Paramètres de conteneur**, sélectionnez **Conteneur unique**.
5. Sélectionnez **Docker Hub**.
6. Sélectionnez **Déploiement continu > Activé**.
7. Sélectionnez **Enregistrer** pour activer le déploiement continu.

![Capture d’écran du paramètre d’application](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Copiez l’URL du webhook. Pour ajouter un webhook pour Docker Hub, suivez les instructions figurant dans <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Webhooks pour Docker Hub</a>.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure App Service sur Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Créer une application web .NET Core dans App Service sur Linux](quickstart-dotnetcore.md)
* [Créer une application Web Ruby dans App Service sur Linux](quickstart-ruby.md)
* [Déployer une application Web Docker/Go dans Web App pour conteneurs](quickstart-docker-go.md)
* [Questions fréquentes (FAQ) sur Azure App Service sur Linux](./app-service-linux-faq.md)
* [Gérer Web App pour conteneurs à l’aide d’Azure CLI](./app-service-linux-cli.md)
