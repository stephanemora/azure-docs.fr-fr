---
title: Déploiement continu - Azure App Service | Microsoft Docs
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
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 384f709bb32f973efec39518eaa895e25136fe23
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66390635"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Déploiement continu vers Azure App Service
Cet article vous explique comment configurer un déploiement continu pour [Azure App Service](overview.md). Azure App Service permet un déploiement continu depuis BitBucket, GitHub et [Azure DevOps Services](https://www.visualstudio.com/team-services/) en extrayant les mises à jour les plus récentes de votre référentiel existant pour les envoyer dans l’un de ces services.

Pour savoir comment configurer manuellement le déploiement continu à partir d’un référentiel cloud non répertorié par le portail Azure (par exemple, [GitLab](https://gitlab.com/)), consultez la section [Configurer manuellement un déploiement continu](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publiez votre référentiel préparé sur l’un des services pris en charge. Pour plus d’informations sur la publication de votre projet sur ces services, consultez [Création d’un dépôt (GitHub)], [Création d’un dépôt (BitBucket)] et [Bien démarrer avec les solutions Azure DevOps Services].

## <a name="deploy-continuously-from-github"></a>Déployer en continu depuis GitHub

Pour activer le déploiement continu avec GitHub, accédez à votre page d’application Azure App Service dans le [portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **GitHub** > **Autoriser**. Suivez les instructions d’autorisation. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Une seule autorisation auprès de GitHub suffit. Si vous possédez déjà une autorisation, cliquez simplement sur **Continuer**. Vous pouvez modifier le compte GitHub autorisé en cliquant sur **Changer de compte**.

![](media/app-service-continuous-deployment/github-continue.png)

Sur la page **Fournisseur de générations**, choisissez le fournisseur de générations, puis cliquez sur > **Continuer**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Option 1 : utiliser le serveur de builds Kudu App Service

Sur la page **Configurer**, sélectionnez l’organisation, le référentiel et la branche à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

Pour déployer à partir d’un dépôt d’organisation GitHub, accédez à GitHub et choisissez **Settings** > **Applications** > **Authorized OAuth Apps**. Cliquez ensuite sur « Azure App Service ».

![Settings > Applications > Authorized OAuth Apps > Azure App Service](media/app-service-continuous-deployment/github-settings-navigation.png)

Dans la page suivante, accordez à App Service un accès aux dépôts de votre organisation en cliquant sur le bouton « Grant » à droite.

![Cliquer sur « Grant » pour accorder à App Service un accès aux dépôts de l’organisation](media/app-service-continuous-deployment/grant-access.png)

Votre organisation doit maintenant figurer dans la liste « Organisation » de la page **Configurer** du Centre de déploiement.

### <a name="option-2-use-azure-pipelines-preview"></a>Option 2 : utiliser Azure Pipelines (préversion)

> [!NOTE]
> Pour qu’App Service puisse créer les Azure Pipelines nécessaires dans votre organisation Azure DevOps Services, votre compte Azure doit avoir le rôle de **propriétaire** dans votre abonnement Azure.
>

Sur la page **Configurer**, dans la section **Code**, sélectionnez l’organisation, le référentiel et la branche à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

Dans la page **Configurer**, dans la section **Générer**, configurez une nouvelle organisation Azure DevOps Services ou spécifiez une organisation existante. Lorsque vous avez terminé, cliquez sur **Continuer**.

> [!NOTE]
> Si vous souhaitez utiliser une organisation Azure DevOps Services existante non répertoriée, vous devez [lier l’organisation Azure DevOps Services à votre abonnement Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Sur la page **Test**, choisissez si vous voulez activer les tests de charge, puis cliquez sur **Continuer**.

En fonction de la [tarification](https://azure.microsoft.com/pricing/details/app-service/plans/) de votre plan App Service, il peut arriver qu’une page proposant un **déploiement intermédiaire** s’affiche. Choisissez si [activer des emplacements de déploiement](deploy-staging-slots.md), puis cliquez sur **Continuer**.

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

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>Déployer en continu à partir d’Azure Repos (DevOps Services)

Pour activer le déploiement continu à partir d’[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index), accédez à votre page d’application Azure App Service dans le [Portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **Azure Repos** > **Continuer**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Sur la page **Fournisseur de générations**, choisissez le fournisseur de générations, puis cliquez sur > **Continuer**.

> [!NOTE]
> Si vous souhaitez utiliser une organisation Azure DevOps Services existante non répertoriée, vous devez [lier l’organisation Azure DevOps Services à votre abonnement Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

### <a name="option-1-use-app-service-kudu-build-server"></a>Option 1 : utiliser le serveur de builds Kudu App Service

Sur la page **Configurer**, sélectionnez l’organisation, le projet, le référentiel et la branche Azure DevOps Services à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>Option 2 : utiliser la livraison continue d’Azure DevOps Services

> [!NOTE]
> Pour qu’App Service puisse créer les Azure Pipelines nécessaires dans votre organisation Azure DevOps Services, votre compte Azure doit avoir le rôle de **propriétaire** dans votre abonnement Azure.
>

Sur la page **Configurer**, dans la section **Code**, sélectionnez l’organisation, le projet, le référentiel et la branche Azure DevOps Services à partir de laquelle vous souhaitez effectuer un déploiement continu. Lorsque vous avez terminé, cliquez sur **Continuer**.

Sur la page **Configurer**, dans la section **Build**, indiquez l’infrastructure de langage qu’Azure DevOps Services doit utiliser afin d’exécuter les tâches de build pour le référentiel sélectionné. Lorsque vous avez terminé, cliquez sur **Continuer**.

Sur la page **Test**, choisissez si activer les tests de charge, puis cliquez sur **Continuer**.

En fonction de la [tarification](https://azure.microsoft.com/pricing/details/app-service/plans/) de votre plan App Service, il peut arriver qu’une page proposant un **déploiement intermédiaire** s’affiche. Choisissez si [activer des emplacements de déploiement](deploy-staging-slots.md), puis cliquez sur **Continuer**. DevOps n’autorise pas la livraison continue à l’emplacement de production. Cette interdiction intentionnelle vise à empêcher les déploiements accidentels en production. Vous devez configurer la livraison continue vers un emplacement de préproduction, y vérifiez les modifications et permuter les emplacements une fois que vous êtes prêt.

### <a name="finish-configuration"></a>Terminer la configuration

Sur la page **Synthèse**, vérifiez les options, puis cliquez sur **Terminer**.

Une fois la configuration terminée, les nouvelles validations dans le référentiel sélectionné sont déployées en continu dans votre application App Service.

## <a name="disable-continuous-deployment"></a>Désactiver le déploiement continu

Pour désactiver le déploiement continu, accédez à votre page d’application Azure App Service dans le [portail Azure](https://portal.azure.com).

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **GitHub**, **Azure DevOps Services** ou **BitBucket** > **Déconnecter**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [How to investigate common issues with continuous deployment (Examen des problèmes courants liés au déploiement continu)](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Comment utiliser PowerShell pour Azure]
* [Documentation Git]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
* [Générer automatiquement un pipeline CI/CD à l’aide d’Azure pour déployer une application ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[Comment utiliser PowerShell pour Azure]: /powershell/azureps-cmdlets-docs
[Documentation Git]: https://git-scm.com/documentation

[Création d’un dépôt (GitHub)]: https://help.github.com/articles/create-a-repo
[Création d’un dépôt (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Bien démarrer avec les solutions Azure DevOps Services]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
