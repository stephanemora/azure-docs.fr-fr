---
title: Déploiement continu vers Azure App Service | Microsoft Docs
description: Découvrez comment activer le déploiement continu vers Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: d83d1ad74d04356f73f18a744c2d1509b5efc280
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233842"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Déploiement continu vers Azure App Service
Cet article vous explique comment configurer un déploiement continu pour [Azure App Service](app-service-web-overview.md). Azure App Service permet un déploiement continu depuis BitBucket, GitHub et [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) en extrayant les mises à jour les plus récentes de votre référentiel existant pour les envoyer dans l’un de ces services.

Pour savoir comment configurer manuellement le déploiement continu à partir d’un référentiel cloud non répertorié par le portail Azure (par exemple, [GitLab](https://gitlab.com/)), consultez la section [Configurer manuellement un déploiement continu](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publiez votre référentiel préparé sur l’un des services pris en charge. Pour plus d’informations sur la publication de votre projet sur ces services, consultez [Création d’un référentiel (GitHub)], [Création d’un dépôt (BitBucket)] et [Prise en main de VSTS].

## <a name="deploy-continuously-from-github"></a>Déployer en continu depuis GitHub

Pour activer le déploiement continu avec GitHub, accédez à votre page d’application Azure App Service dans le [portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **GitHub** > **Autoriser**. Suivez les instructions d’autorisation. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Une seule autorisation auprès de GitHub suffit. Si vous possédez déjà une autorisation, cliquez simplement sur **Continuer**. Vous pouvez modifier le compte GitHub autorisé en cliquant sur **Changer de compte**.

![](media/app-service-continuous-deployment/github-continue.png)

Sur la page **Fournisseur de générations**, choisissez le fournisseur de générations, puis cliquez sur > **Continuer**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Option 1 : utiliser le serveur de builds Kudu App Service

Sur la page **Configurer**, sélectionnez l’organisation, le référentiel et la branche à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Option 2 : utiliser la livraison continue avec VSTS

> [!NOTE]
> Pour qu’Azure App Service puisse créer la build nécessaire et libérer des définitions dans le compte VSTS, votre compte Azure doit avoir pour rôle celui de **Propriétaire** dans votre abonnement Azure.
>

Sur la page **Configurer**, dans la section **Code**, sélectionnez l’organisation, le référentiel et la branche à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

Sur la page **Configurer**, dans la section **Build**, configurez un nouveau compte VSTS ou indiquez un compte existant. Lorsque vous avez terminé, cliquez sur **Continuer**.

> [!NOTE]
> Si vous voulez utiliser un compte VSTS existant qui ne figure pas dans la liste, vous devez [associer le compte VSTS à votre abonnement Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Sur la page **Test**, choisissez si activer les tests de charge, puis cliquez sur **Continuer**.

En fonction de la [tarification](/pricing/details/app-service/plans/) de votre plan App Service, il peut arriver qu’une page proposant un **déploiement intermédiaire** s’affiche. Choisissez si [activer des emplacements de déploiement](web-sites-staged-publishing.md), puis cliquez sur **Continuer**.

### <a name="finish-configuration"></a>Terminer la configuration

Sur la page **Synthèse**, vérifiez les options, puis cliquez sur **Terminer**.

Une fois la configuration terminée, les nouvelles validations dans le référentiel sélectionné sont déployées en continu dans votre application App Service.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Déployer en continu depuis BitBucket

Pour activer le déploiement continu avec BitBucket, accédez à votre page d’application Azure App Service dans le [portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **BitBucket** > **Autoriser**. Suivez les instructions d’autorisation. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Une seule autorisation auprès de BitBucket suffit. Si vous possédez déjà une autorisation, cliquez simplement sur **Continuer**. Vous pouvez modifier le compte BitBucket autorisé en cliquant sur **Changer de compte**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

Sur la page **Configurer**, sélectionnez le référentiel et la branche à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

Sur la page **Synthèse**, vérifiez les options, puis cliquez sur **Terminer**.

Une fois la configuration terminée, les nouvelles validations dans le référentiel sélectionné sont déployées en continu dans votre application App Service.

## <a name="deploy-continuously-from-vsts"></a>Déployer en continu depuis VSTS

Pour activer le déploiement continu avec VSTS, accédez à votre page d’application Azure App Service dans le [portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **VSTS** > **Autoriser**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Sur la page **Fournisseur de générations**, choisissez le fournisseur de générations, puis cliquez sur > **Continuer**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Option 1 : utiliser le serveur de builds Kudu App Service

Sur la page **Configurer**, sélectionnez le compte VSTS, le projet, le référentiel et la branche à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Option 2 : utiliser la livraison continue avec VSTS

> [!NOTE]
> Pour qu’Azure App Service puisse créer la build nécessaire et libérer des définitions dans le compte VSTS, votre compte Azure doit avoir pour rôle celui de **Propriétaire** dans votre abonnement Azure.
>

Sur la page **Configurer**, dans la section **Code**, sélectionnez le compte VSTS, le projet, le référentiel et la branche à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

> [!NOTE]
> Si vous voulez utiliser un compte VSTS existant qui ne figure pas dans la liste, vous devez [associer le compte VSTS à votre abonnement Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Sur la page **Configurer**, dans la section **Build**, indiquez l’infrastructure de langage que VSTS doit utiliser afin d’exécuter les tâches de build pour le référentiel sélectionné. Lorsque vous avez terminé, cliquez sur **Continuer**.

Sur la page **Test**, choisissez si activer les tests de charge, puis cliquez sur **Continuer**.

En fonction de la [tarification](/pricing/details/app-service/plans/) de votre plan App Service, il peut arriver qu’une page proposant un **déploiement intermédiaire** s’affiche. Choisissez si [activer des emplacements de déploiement](web-sites-staged-publishing.md), puis cliquez sur **Continuer**. 

### <a name="finish-configuration"></a>Terminer la configuration

Sur la page **Synthèse**, vérifiez les options, puis cliquez sur **Terminer**.

Une fois la configuration terminée, les nouvelles validations dans le référentiel sélectionné sont déployées en continu dans votre application App Service.

## <a name="disable-continuous-deployment"></a>Désactiver le déploiement continu

Pour désactiver le déploiement continu, accédez à votre page d’application Azure App Service dans le [portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **GitHub**, **VSTS** ou **BitBucket** > **Déconnecter**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [How to investigate common issues with continuous deployment (Examen des problèmes courants liés au déploiement continu)](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Comment utiliser PowerShell pour Azure]
* [Documentation Git]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
* [Générer automatiquement un pipeline CI/CD à l’aide d’Azure pour déployer une application ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Comment utiliser PowerShell pour Azure]: /powershell/azureps-cmdlets-docs
[Documentation Git]: http://git-scm.com/documentation

[Création d’un référentiel (GitHub)]: https://help.github.com/articles/create-a-repo
[Création d’un dépôt (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Prise en main de VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
