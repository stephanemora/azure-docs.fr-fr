---
title: Configurer le déploiement continu
description: Découvrez comment activer CI/CD vers Azure App Service à partir de GitHub, BitBucket, Azure Repos ou d’autres dépôts. Sélectionnez le pipeline de build qui répond à vos besoins.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 692b07c82c329a93d79ad3a87beec5dbe1c595d3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669981"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Déploiement continu vers Azure App Service

[Azure App Service](overview.md) permet un déploiement continu à partir des référentiels GitHub, BitBucket et [Azure Repos](https://azure.microsoft.com/services/devops/repos/) en extrayant les dernières mises à jour. Cet article explique comment utiliser le Portail Azure pour procéder à un déploiement continu de votre application via le service de builds Kudu ou [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Pour plus d'informations sur les services de contrôle de code source, consultez [Création d’un dépôt (GitHub)], [Création d’un dépôt (BitBucket)] ou [Créer un référentiel Git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autoriser Azure App Service 

Pour utiliser Azure Repos, assurez-vous que votre organisation Azure DevOps est liée à votre abonnement Azure. Pour plus d'informations, consultez [Configurer un compte Azure DevOps Services afin de le déployer sur une application web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

Pour Bitbucket ou GitHub, autorisez Azure App Service à se connecter à votre référentiel. Un service de contrôle de source ne nécessite qu'une seule autorisation. 

1. Sélectionnez **App Services** dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), puis sélectionnez l'application web que vous souhaitez déployer. 
   
1. Sur la page de l'application, sélectionnez **Centre de déploiement** dans le menu de gauche.
   
1. Sur la page **Centre de déploiement**, choisissez **GitHub** ou **Bitbucket**, puis sélectionnez **Autoriser**. 
   
   ![Sélectionnez le service de contrôle de code source, puis choisissez Autoriser.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Si nécessaire, connectez-vous au service et suivez les instructions d'autorisation. 

## <a name="enable-continuous-deployment"></a>Activer le déploiement continu 

Après avoir autorisé un service de contrôle de code source, configurez votre application pour un déploiement continu via le [serveur de builds Kudu App Service](#option-1-use-the-app-service-build-service) intégré, ou via [Azure Pipelines](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Option 1 : utiliser le service de builds App Service

Vous pouvez utiliser le serveur de builds Kudu App Service intégré pour procéder à des déploiements continus à partir de GitHub, Bitbucket ou Azure Repos. 

1. Sélectionnez **App Services** dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), puis sélectionnez l'application web que vous souhaitez déployer. 
   
1. Sur la page de l'application, sélectionnez **Centre de déploiement** dans le menu de gauche.
   
1. Sélectionnez votre fournisseur de contrôle de code source autorisé sur la page **Centre de déploiement**, puis choisissez **Continuer**. Pour GitHub ou Bitbucket, vous pouvez également sélectionner **Modifier le compte** pour changer de compte autorisé. 
   
   > [!NOTE]
   > Pour utiliser Azure Repos, assurez-vous que votre organisation Azure DevOps Services est liée à votre abonnement Azure. Pour plus d'informations, consultez [Configurer un compte Azure DevOps Services afin de le déployer sur une application web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. Pour GitHub ou Azure Repos, sur la page **Fournisseur de builds**, sélectionnez **Service de builds App Service**, puis **Continuer**. Bitbucket utilise toujours le service de builds App Service.
   
   ![Sélectionnez Service de builds App Service, puis Continuer.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Sur la page **Configurer** :
   
   - Pour GitHub, sélectionnez l'**Organisation**, le **Référentiel** et la **Branche** que vous souhaitez déployer en continu.
     
     > [!NOTE]
     > En l'absence de référentiel, il peut être nécessaire d'autoriser Azure App Service dans GitHub. Accédez à votre référentiel GitHub, puis sélectionnez **Paramètres** > **Applications** > **Applications OAuth autorisées**. Sélectionnez **Azure App Service**, puis **Octroyer**.
     
   - Pour Bitbucket, sélectionnez l'**Équipe**, le **Référentiel** et la **Branche** que vous souhaitez déployer en continu.
     
   - Pour Azure Repos, sélectionnez l'**Organisation Azure DevOps**, le **Projet**, le **Référentiel** et la **Branche** que vous souhaitez déployer en continu.
     
     > [!NOTE]
     > Si votre organisation Azure DevOps n'est pas répertoriée, assurez-vous qu'elle est liée à votre abonnement Azure. Pour plus d'informations, consultez [Configurer un compte Azure DevOps Services afin de le déployer sur une application web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
     
1. Sélectionnez **Continuer**.
   
   ![Renseignez les informations relatives au référentiel, puis sélectionnez Continuer.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Une fois le fournisseur de builds configuré, vérifiez les paramètres sur la page **Résumé**, puis sélectionnez **Terminer**.
   
   Les nouvelles validations correspondant au référentiel et à la branche sélectionnés sont déployées en continu dans votre application App Service. Vous pouvez suivre les validations et les déploiements sur la page **Centre de déploiement**.
   
   ![Suivre les validations et les déploiements dans le Centre de déploiement](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Option 2 : utiliser Azure Pipelines 

Si votre compte dispose des autorisations nécessaires, vous pouvez configurer Azure Pipelines pour un déploiement continu à partir de référentiels GitHub ou Azure Repos. Pour plus d'informations sur le déploiement via Azure Pipelines, consultez [Déployer une application web dans Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Pour permettre à Azure App Service de créer des pipelines Azure à diffusion continue dans votre organisation Azure DevOps : 

- Votre compte Azure doit être autorisé à écrire dans Azure Active Directory et à créer un service. 
  
- Votre compte Azure doit disposer du rôle **Propriétaire** dans votre abonnement Azure.

- Vous devez être administrateur dans le projet Azure DevOps que vous souhaitez utiliser.

Pour configurer Azure Pipelines (préversion) :

1. Sélectionnez **App Services** dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), puis sélectionnez l'application web que vous souhaitez déployer. 
   
1. Sur la page de l'application, sélectionnez **Centre de déploiement** dans le menu de gauche.
   
1. Dans la page **Fournisseur de générations**, sélectionnez **Azure pipelines (préversion)** , puis **Continuer**. 
   
1. Sur la page **Configurer**, section **Code** :
   
   - Pour GitHub, sélectionnez l'**Organisation**, le **Référentiel** et la **Branche** que vous souhaitez déployer en continu.
     
     > [!NOTE]
     > En l'absence de référentiel, il peut être nécessaire d'autoriser Azure App Service dans GitHub. Accédez à votre référentiel GitHub, puis sélectionnez **Paramètres** > **Applications** > **Applications OAuth autorisées**. Sélectionnez **Azure App Service**, puis **Octroyer**.
     
   - Pour Azure Repos, sélectionnez l'**Organisation Azure DevOps**, le **Projet**, le **Référentiel** et la **Branche** que vous souhaitez déployer en continu, ou configurez une nouvelle Organisation Azure DevOps.
     
     > [!NOTE]
     > Si votre organisation Azure DevOps existante n'est pas répertoriée, vous serez peut-être amené à devoir la lier à votre abonnement Azure. Pour plus d’informations, consultez [Définir votre pipeline de mise en production CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Sélectionnez **Continuer**.
   
1. Pour Azure Repos, dans la section **Build**, spécifiez l'infrastructure de langage qu'Azure Pipelines doit utiliser pour exécuter les tâches de build, puis sélectionnez **Continuer**.
   
1. Sur la page **Test**, choisissez d'activer ou non les tests de charge, puis sélectionnez **Continuer**.
   
1. En fonction du [niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/plans/) de votre plan App Service, il peut arriver qu’une page **Déployer en préproduction** s'affiche. Choisissez si vous souhaitez [activer des emplacements de déploiement](deploy-staging-slots.md), puis sélectionnez **Continuer**.
   
   > [!NOTE]
   > Azure Pipelines ne permet pas la livraison continue vers l'emplacement de production. Cette restriction vise à empêcher les déploiements accidentels en production. Configurez la livraison continue vers un emplacement de préproduction, vérifiez les modifications, puis permutez les emplacements une fois que vous êtes prêt.
   
1. Une fois le fournisseur de builds configuré, vérifiez les paramètres sur la page **Résumé**, puis sélectionnez **Terminer**.
   
   Les nouvelles validations correspondant au référentiel et à la branche sélectionnés sont déployées en continu dans votre application App Service. Vous pouvez suivre les validations et les déploiements sur la page **Centre de déploiement**.
   
   ![Suivre les validations et les déploiements dans le Centre de déploiement](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Désactiver le déploiement continu

Pour désactiver le déploiement continu, sélectionnez **Déconnecter** en haut de la page **Centre de déploiement** de votre application.

![Désactiver le déploiement continu](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Utiliser des dépôts non pris en charge

Pour les applications Windows, vous pouvez configurer manuellement le déploiement continu à partir d’un dépôt Git ou Mecurial sur le cloud que le portail ne prend pas directement en charge, comme [GitLab](https://gitlab.com/). Pour cela, choisissez la case Externe dans la page **Centre de déploiement**. Pour plus d’informations, consultez [Configurer le déploiement continu avec des étapes manuelles](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Examiner les problèmes courants liés au déploiement continu](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Utilisation d'Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Documentation Git](https://git-scm.com/documentation)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[Création d’un dépôt (GitHub)]: https://help.github.com/articles/create-a-repo
[Création d’un dépôt (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Créer un référentiel Git (Azure Repos)]: /azure/devops/repos/git/creatingrepo
