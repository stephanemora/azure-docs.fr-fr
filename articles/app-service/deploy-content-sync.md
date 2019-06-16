---
title: Synchroniser le contenu à partir d’un dossier cloud - Azure App Service
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
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 65f372196671e95f7d9af7f47011e9ca1f9de316
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769615"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronisation de contenu à partir d’un dossier cloud dans Azure App Service
Cet article vous explique comment synchroniser votre contenu avec [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) à partir de Dropbox et OneDrive. 

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

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **Déconnecter**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer à partir du dépôt Git local](deploy-local-git.md)
