---
title: Configurer le déploiement continu
description: Découvrez comment activer CI/CD vers Azure App Service à partir de GitHub, Bitbucket, Azure Repos ou d’autres dépôts. Sélectionnez le pipeline de build qui répond à vos besoins.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bff93e0dcfb49871cefa68d16d7a7db85c0f69ce
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122642088"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Déploiement continu vers Azure App Service

[Azure App Service](overview.md) permet un déploiement continu à partir des dépôts [GitHub](https://help.github.com/articles/create-a-repo), [Bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html) et [Azure Repos](/azure/devops/repos/git/creatingrepo) en extrayant les dernières mises à jour.

> [!NOTE]
> La page **Centre de développement (classique)** dans le portail Azure, une ancienne version de la fonctionnalité de déploiement, est déconseillée depuis mars 2021. Cette modification n’affecte pas les paramètres de déploiement existants dans votre application, et vous pouvez continuer à gérer le déploiement d’applications dans la page **Centre de déploiement** du portail.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-the-deployment-source"></a>Configurer la source de déploiement

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application App Service.

1. Dans le volet gauche, sélectionnez **Centre de déploiement**. Sélectionnez ensuite **Paramètres**. 

1. Dans la zone **Source**, sélectionnez l’une des options CI/CD :

    ![Capture d’écran montrant comment choisir la source de déploiement.](media/app-service-continuous-deployment/choose-source.png)

Sélectionnez l’onglet qui correspond à votre fournisseur de build pour continuer.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) correspond au fournisseur de build par défaut. Pour changer le fournisseur, sélectionnez **Changer de fournisseur**  > **Service de build App Service** (Kudu) > **OK**.

    > [!NOTE]
    > Pour utiliser Azure Pipelines comme fournisseur de build pour votre application App Service, configurez CI/CD directement à partir d’Azure Pipelines. Ne le configurez pas dans App Service. L’option **Azure Pipelines** permet de simplement pointer dans la bonne direction.

1. La première fois que vous effectuez un déploiement à partir de GitHub, sélectionnez **Autoriser** et suivez les invites d’autorisation. Si vous souhaitez effectuer un déploiement à partir d’un autre dépôt d’utilisateur, sélectionnez **Changer de compte**.

1. Après avoir autorisé votre compte Azure avec GitHub, sélectionnez l’**organisation**, le **dépôt** et la **branche** pour lesquels configurer CI/CD. Si vous ne trouvez pas d’organisation ni de dépôt, vous avez peut-être besoin d’activer des autorisations supplémentaires sur GitHub. Pour plus d’informations, consultez [Gestion de l’accès aux dépôts de votre organisation](https://docs.github.com/organizations/managing-access-to-your-organizations-repositories).

1. Quand GitHub Actions est sélectionné en tant que fournisseur de build, vous pouvez sélectionner le fichier de workflow de votre choix à l’aide des listes déroulantes **Pile d’exécution** et **Version**. Azure valide ce fichier de workflow dans le dépôt GitHub sélectionné afin de gérer les tâches de génération et de déploiement. Pour afficher le fichier avant d’enregistrer vos modifications, sélectionnez **Aperçu du fichier**.

    > [!NOTE]
    > App Service détecte le [paramètre de pile de langage](configure-common.md#configure-language-stack-settings) de votre application et sélectionne le modèle de workflow le plus approprié. Si vous choisissez un autre modèle, il peut déployer une application qui ne s’exécute pas correctement. Pour plus d’informations, consultez [Fonctionnement du fournisseur de build GitHub Actions](#how-the-github-actions-build-provider-works).

1. Sélectionnez **Enregistrer**.
   
    Les nouvelles validations correspondant au dépôt et à la branche sélectionnés sont déployées en continu dans votre application App Service. Vous pouvez suivre les validations et les déploiements sous l’onglet **Journaux**.

# <a name="bitbucket"></a>[Bitbucket](#tab/bitbucket)

L’intégration de Bitbucket utilise les services de build App Service (Kudu) pour l’automatisation de la génération.

4. La première fois que vous effectuez un déploiement à partir de Bitbucket, sélectionnez **Autoriser** et suivez les invites d’autorisation. Si vous souhaitez effectuer un déploiement à partir d’un autre dépôt d’utilisateur, sélectionnez **Changer de compte**.

1. Pour Bitbucket, sélectionnez l'**Équipe**, le **Référentiel** et la **Branche** que vous souhaitez déployer en continu.

1. Sélectionnez **Enregistrer**.
   
    Les nouvelles validations correspondant au référentiel et à la branche sélectionnés sont déployées en continu dans votre application App Service. Vous pouvez suivre les validations et les déploiements sous l’onglet **Journaux**.
   
# <a name="local-git"></a>[Git local](#tab/local)

Consultez [Déploiement Git local vers Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)
   
   > [!NOTE]
   > Azure Repos est pris en charge en tant que source de déploiement pour les applications Windows.
   >

4. Le service de build App Service (Kudu) correspond au fournisseur de build par défaut.

    > [!NOTE]
    > Pour utiliser Azure Pipelines comme fournisseur de build pour votre application App Service, configurez CI/CD directement à partir d’Azure Pipelines. Ne le configurez pas dans App Service. L’option **Azure Pipelines** permet de simplement pointer dans la bonne direction.

1. Sélectionnez l'**organisation Azure DevOps**, le **projet**, le **référentiel** et la **branche** que vous souhaitez déployer en continu. 

    Si votre organisation DevOps n'est pas répertoriée, assurez-vous qu'elle est liée à votre abonnement Azure. Pour plus d’informations, consultez l’article [Créer une connexion au service Azure](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Désactiver le déploiement continu

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application App Service.

1. Dans le volet gauche, sélectionnez **Centre de déploiement**. Ensuite, sélectionnez **Paramètres** > **Déconnecter** :

    ![Capture d’écran montrant comment déconnecter la synchronisation de votre dossier cloud avec votre application App Service dans le portail Azure](media/app-service-continuous-deployment/disable.png)

1. Par défaut, le fichier de workflow GitHub Actions est conservé dans votre référentiel, mais continue de déclencher le déploiement vers votre application. Pour supprimer ce fichier de votre dépôt, sélectionnez **Supprimer le fichier de workflow**.

1. Sélectionnez **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Fonctionnement du fournisseur de build GitHub Actions

Le fournisseur de build GitHub Actions est une option pour [CI/CD à partir de GitHub](#configure-the-deployment-source). Il effectue les actions suivantes pour configurer CI/CD :

- Dépose un fichier de workflow GitHub Actions dans votre référentiel GitHub pour gérer les tâches de génération et de déploiement vers App Service.
- Ajoute le profil de publication de votre application en tant que secret GitHub. Le fichier de workflow utilise ce secret pour s’authentifier auprès d’App Service.
- Capture des informations à partir des [journaux d’exécution de workflow](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) et les affiche sous l’onglet **Journaux** dans le Centre de déploiement de votre application.

Vous pouvez personnaliser le fournisseur de build GitHub Actions comme suit :

- Personnalisez le fichier de workflow après qu’il a été généré dans votre référentiel GitHub. Pour plus d’informations, consultez [Syntaxe de workflow pour GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Assurez-vous que le workflow est déployé dans App Service avec l’action [azure/webapps-deploy](https://github.com/Azure/webapps-deploy).
- Si la branche sélectionnée est protégée, vous pouvez toujours afficher un aperçu du fichier de workflow sans enregistrer la configuration, puis l’ajouter manuellement dans votre dépôt. Cette méthode ne permet pas l’intégration des journaux avec le portail Azure.
- Au lieu d’utiliser un profil de publication, effectuez le déploiement à l’aide d’un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) dans Azure Active Directory.

#### <a name="authenticate-by-using-a-service-principal"></a>S’authentifier avec un principal de service

Cette configuration facultative remplace l’authentification par défaut par les profils de publication dans le fichier de workflow généré.

1. Générez un principal de service en utilisant la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) dans l’interface [Azure CLI](/cli/azure/). Dans l’exemple suivant, remplacez \<subscription-id> , \<group-name> et \<app-name> par vos propres valeurs :

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Pour des raisons de sécurité, accordez l’accès minimal requis au principal du service. L’étendue dans l’exemple précédent est limitée à l’application App Service spécifique, et non à l’ensemble du groupe de ressources.
    
1. Enregistrez l’intégralité de la sortie JSON pour l’étape suivante, y compris le niveau supérieur `{}`.

1. Dans [GitHub](https://github.com/), dans votre dépôt, sélectionnez **Paramètres** > **Secrets** > **Ajouter un nouveau secret**.

1. Collez l’intégralité de la sortie JSON de la commande Azure CLI dans le champ de valeur du secret. Nommez le secret comme `AZURE_CREDENTIALS`.

1. Dans le fichier de workflow généré par le Centre de déploiement, modifiez l’étape `azure/webapps-deploy` pour qu’elle ressemble à l’exemple suivant (qui est modifié à partir d’un fichier de workflow Node.js) :

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added.
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile.
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure.
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Déployer à partir d’autres référentiels

Pour les applications Windows, vous pouvez configurer manuellement le déploiement continu à partir d’un dépôt cloud Git ou Mercurial que le portail ne prend pas directement en charge, comme [GitLab](https://gitlab.com/). Pour ce faire, vous devez sélectionner **Git externe** dans la liste déroulante **Source**. Pour plus d’informations, consultez [Configurer le déploiement continu avec des étapes manuelles](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Plus de ressources

* [Déployer à partir d’Azure Pipelines vers Azure App Service](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Examiner les problèmes courants liés au déploiement continu](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Utilisation d'Azure PowerShell](/powershell/azure/)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
