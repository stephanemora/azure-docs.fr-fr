---
title: Configurer le déploiement continu
description: Découvrez comment activer CI/CD vers Azure App Service à partir de GitHub, BitBucket, Azure Repos ou d’autres dépôts. Sélectionnez le pipeline de build qui répond à vos besoins.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/03/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 5af8294518759181326e7736ef755f0a83581014
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564955"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Déploiement continu vers Azure App Service

[Azure App Service](overview.md) permet un déploiement continu à partir des référentiels [GitHub](https://help.github.com/articles/create-a-repo), [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html) et [Azure Repos](/azure/devops/repos/git/creatingrepo) en extrayant les dernières mises à jour.

> [!NOTE]
> La page **Centre de développement (classique)** dans le portail Azure, qui est l’ancienne expérience de déploiement, sera déconseillée en mars 2021. Cette modification n’affecte aucun paramètre de déploiement existant dans votre application, et vous pouvez continuer à gérer le déploiement d’applications dans la page du **Centre de déploiement**.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Configurer la source de déploiement

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application App Service.

1. Dans le menu de gauche, cliquez sur **Centre de déploiement** > **Paramètres**. 

1. Dans **Source**, sélectionnez l’une des options CI/CD.

    ![Montre comment choisir une source de déploiement dans le Centre de déploiement pour Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Choisissez l’onglet correspondant à votre sélection pour les étapes.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) correspond au fournisseur de build par défaut. Pour le modifier, cliquez sur **Changer de fournisseur**  >  **Service de build App Service** (Kudu) > **OK**.

    > [!NOTE]
    > Pour utiliser Azure Pipelines en tant que fournisseur de build pour votre application App Service, ne le configurez pas dans App Service. Configurez plutôt CI/CD directement depuis Azure Pipelines. L’option **Azure Pipelines** permet de simplement pointer dans la bonne direction.

1. La premiière fois que vous effectuez un déploiement à partir de GitHub, cliquez sur **Autoriser** et suivez les invites d’autorisation. Si vous souhaitez effectuer un déploiement à partir d’un référentiel d’utilisateur différent, cliquez sur **Changer de compte**.

1. Après avoir autorisé votre compte Azure auprès de GitHub, sélectionnez l'**organisation**, le **référentiel** et la **branche** pour lesquels configurer CI/CD.

1. Une fois GitHub Actions sélectionné en tant que fournisseur de build, vous pouvez sélectionner le fichier de workflow de votre choix à l’aide des listes déroulantes **Pile d’exécution** et **Version**. Azure valide ce fichier de workflow dans le référentiel GitHub sélectionné afin de gérer les tâches de génération et de déploiement. Pour afficher le fichier avant d’enregistrer vos modifications, cliquez sur **Aperçu du fichier**.

    > [!NOTE]
    > App Service détecte le [paramètre de pile de langage](configure-common.md#configure-language-stack-settings) de votre application et sélectionne le modèle de workflow le plus approprié. Si vous choisissez un autre modèle, il peut déployer une application qui ne s’exécute pas correctement. Pour plus d’informations, consultez [Fonctionnement du fournisseur de build GitHub Actions](#how-the-github-actions-build-provider-works).

1. Cliquez sur **Enregistrer**.
   
    Les nouvelles validations correspondant au référentiel et à la branche sélectionnés sont déployées en continu dans votre application App Service. Vous pouvez suivre les validations et les déploiements sous l’onglet **Journaux** .

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

L’intégration BitBucket utilise les services de build App Service (Kudu) pour l’automatisation de la génération.

4. La premiière fois que vous effectuez un déploiement à partir de BitBucket, cliquez sur **Autoriser** et suivez les invites d’autorisation. Si vous souhaitez effectuer un déploiement à partir d’un référentiel d’utilisateur différent, cliquez sur **Changer de compte**.

1. Pour Bitbucket, sélectionnez l'**Équipe**, le **Référentiel** et la **Branche** que vous souhaitez déployer en continu.

1. Cliquez sur **Enregistrer**.
   
    Les nouvelles validations correspondant au référentiel et à la branche sélectionnés sont déployées en continu dans votre application App Service. Vous pouvez suivre les validations et les déploiements sous l’onglet **Journaux** .
   
# <a name="local-git"></a>[Git local](#tab/local)

Consultez [Déploiement Git local vers Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Azure Repos en tant que source de déploiement est pris en charge pour les applications Windows.
>

4. Le service de build App Service (Kudu) correspond au fournisseur de build par défaut.

    > [!NOTE]
    > Pour utiliser Azure Pipelines en tant que fournisseur de build pour votre application App Service, ne le configurez pas dans App Service. Configurez plutôt CI/CD directement depuis Azure Pipelines. L’option **Azure Pipelines** permet de simplement pointer dans la bonne direction.

1. Sélectionnez l'**organisation Azure DevOps**, le **projet**, le **référentiel** et la **branche** que vous souhaitez déployer en continu. 

    Si votre organisation DevOps n'est pas répertoriée, assurez-vous qu'elle est liée à votre abonnement Azure. Pour plus d’informations, consultez l’article [Créer une connexion au service Azure](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Désactiver le déploiement continu

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application App Service.

1. Dans le menu de gauche, cliquez sur **Centre de déploiement** > **Paramètres** > **Déconnecter**. 

    ![Montre comment déconnecter la synchronisation de votre dossier cloud avec votre application App Service dans le Portail Azure.](media/app-service-continuous-deployment/disable.png)

1. Par défaut, le fichier de workflow GitHub Actions est conservé dans votre référentiel, mais continue de déclencher le déploiement vers votre application. Pour le supprimer de votre référentiel, sélectionnez **Supprimer le fichier de workflow**.

1. Cliquez sur **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Fonctionnement du fournisseur de build GitHub Actions

Le fournisseur de build GitHub Actions est une option pour [CI/CD à partir de GitHub](#configure-deployment-source), et effectue les opérations suivantes pour configurer CI/CD :

- Dépose un fichier de workflow GitHub Actions dans votre référentiel GitHub pour gérer les tâches de génération et de déploiement vers App Service.
- Ajoute le profil de publication de votre application en tant que secret GitHub. Le fichier de workflow utilise ce secret pour s’authentifier auprès d’App Service.
- Capture des informations à partir des [journaux d’exécution de workflow](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) et les affiche sous l’onglet **Journaux** dans le **Centre de déploiement** de votre application.

Vous pouvez personnaliser le fournisseur de build GitHub Actions comme suit :

- Personnalisez le fichier de workflow après qu’il a été généré dans votre référentiel GitHub. Pour plus d’informations, consultez [Syntaxe de workflow pour GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Assurez-vous que le workflow est déployé dans App Service avec l’action [azure/webapps-deploy](https://github.com/Azure/webapps-deploy).
- Si la branche sélectionnée est protégée, vous pouvez toujours afficher un aperçu du fichier de workflow sans enregistrer la configuration, puis l’ajouter manuellement à votre référentiel. Cette méthode ne permet pas l’intégration des journaux avec le portail Azure.
- Plutôt qu’un profil de publication, effectuez le déploiement à l’aide d’un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) dans Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>S’authentifier avec un principal de service

1. Générez un principal de service à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) dans [Azure CLI](/cli/azure/). Dans l’exemple suivant, remplacez *\<subscription-id>* , *\<group-name>* et *\<app-name>* par vos propres valeurs :

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Pour des raisons de sécurité, accordez l’accès minimal requis au principal du service. L’étendue dans l’exemple précédent est limitée à l’application App Service spécifique, et non à l’ensemble du groupe de ressources.
    
1. Enregistrez l’intégralité de la sortie JSON pour l’étape suivante, y compris le niveau supérieur `{}`.

1. Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

1. Collez l’intégralité de la sortie JSON de la commande Azure CLI dans le champ de valeur du secret. Nommez le secret comme `AZURE_CREDENTIALS`.

1. Dans le fichier de workflow généré par le **Centre de déploiement**, modifiez l'étape `azure/webapps-deploy` avec du code similaire à l’exemple suivant (qui est modifié à partir d’un fichier de workflow Node.js) :

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Déployer à partir d’autres référentiels

Pour les applications Windows, vous pouvez configurer manuellement le déploiement continu à partir d’un dépôt Git ou Mercurial dans le cloud que le portail ne prend pas directement en charge, comme [GitLab](https://gitlab.com/). Pour ce faire, vous devez choisir Git externe dans la liste déroulante **Source**. Pour plus d’informations, consultez [Configurer le déploiement continu avec des étapes manuelles](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Plus de ressources

* [Déployer à partir d’Azure Pipelines vers Azure App Service](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Examiner les problèmes courants liés au déploiement continu](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Utilisation d'Azure PowerShell](/powershell/azure/)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
