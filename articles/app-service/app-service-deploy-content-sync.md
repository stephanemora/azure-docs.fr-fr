---
title: Synchronisation de contenu à partir d’un dossier cloud dans Azure App Service
description: Apprenez à déployer votre application dans Azure App Service via la synchronisation de contenu à partir d’un dossier cloud.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: 3781010c74daa51c92813db85ee03eaa4c02a4cf
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233585"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronisation de contenu à partir d’un dossier cloud dans Azure App Service
Cet article vous explique comment synchroniser votre contenu avec [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) à partir de Dropbox et OneDrive. 

Le déploiement à la demande de synchronisation de contenu est généré par le [moteur de déploiement Kudu](https://github.com/projectkudu/kudu/wiki) App Service. Vous pouvez utiliser votre code et contenu d’application dans un dossier cloud désigné cloud, puis synchroniser avec App Service d’un seul clic. La synchronisation de contenu utilise le serveur de build Kudu. 

## <a name="enable-content-sync-deployment"></a>Activer le déploiement de la synchronisation de contenu

Pour activer la synchronisation de contenu, accédez à votre page d’application App Service dans le [portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **OneDrive** ou **Dropbox** > **Autoriser**. Suivez les instructions d’autorisation. 

![](media/app-service-deploy-content-sync/choose-source.png)

Vous ne devez autoriser qu’une seule fois avec OneDrive ou Dropbox. Si vous êtes déjà autorisé, cliquez simplement sur **Continuer**. Vous pouvez modifier le compte OneDrive ou Dropbox autorisé en cliquant sur **Changer de compte**.

![](media/app-service-deploy-content-sync/continue.png)

Sur la page **Configurer**, sélectionnez le dossier que vous voulez synchroniser. Ce dossier est créé sous le chemin de contenu désigné suivant dans OneDrive ou Dropbox. 
   
* **OneDrive** : `Apps\Azure Web Apps`
* **Dropbox** : `Apps\Azure`

Lorsque vous avez terminé, cliquez sur **Continuer**.

Sur la page **Résumé**, vérifiez les options, puis cliquez sur **Terminer**.

## <a name="synchronize-content"></a>Synchroniser le contenu

Lorsque vous voulez synchroniser le contenu dans votre dossier cloud avec App Service, revenez à la page **Centre de déploiement**, puis cliquez sur **Synchroniser**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > En raison de différences sous-jacentes entre les API, **OneDrive Entreprise** n’est pas pris en charge pour l’instant. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Désactiver le déploiement de la synchronisation de contenu

Pour désactiver la synchronisation de contenu, accédez à votre page d’application App Service dans le [portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **OneDrive** ou **Dropbox** > **Déconnecter**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer à partir du dépôt Git local](app-service-deploy-local-git.md)
