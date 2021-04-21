---
title: configurer les informations d’identification de déploiement
description: Découvrez les types d’informations d’identification de déploiement dans Azure App Service et comment les configurer et les utiliser.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: b77a26f61e1168846156de990806bbed2f7c41e3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789534"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurer les informations d’identification de déploiement pour Azure App Service
Pour sécuriser le déploiement d’applications à partir d’un ordinateur local, [Azure App Service](./overview.md) prend en charge deux types d’informations d’identification pour le [déploiement Git local](deploy-local-git.md) et le [déploiement FTP/S](deploy-ftp.md). Ces informations d’identification ne sont pas les mêmes que les informations d’identification de votre abonnement Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> La page **Centre de développement (classique)** dans le portail Azure, qui est l’ancienne expérience de déploiement, sera déconseillée en mars 2021. Cette modification n’affecte aucun paramètre de déploiement existant dans votre application, et vous pouvez continuer à gérer le déploiement d’applications dans la page du **Centre de déploiement** .

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Configurer les informations d’identification de portée utilisateur

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Exécutez la commande [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set). Remplacez \<username> et \<password> par un nom d’utilisateur et un mot de passe de déploiement. 

- Le nom d’utilisateur doit être unique dans Azure et, pour les push Git locaux, ne doit pas contenir le symbole « @ ». 
- Le mot de passe doit comporter au moins huit caractères et inclure deux des trois éléments suivants : lettres, chiffres et symboles. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

La sortie JSON affiche le mot de passe comme étant `null`.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Vous ne pouvez pas configurer les informations d’identification de portée utilisateur avec Azure PowerShell. Utilisez une autre méthode ou envisagez d’[utiliser des informations d’identification de portée application](#appscope). 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Vous pouvez configurer vos informations d’identification de portée utilisateur sur la [page Ressource](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) d’une application. Quelle que soit l’application dans laquelle vous configurez ces informations d’identification, ces dernières s’appliquent à toutes les applications de tous les abonnements de votre compte Azure. 

Sur le [portail Azure](https://portal.azure.com), vous devez disposer d’au moins une application avant de pouvoir accéder à la page Informations d’identification de déploiement. Pour configurer les informations d’identification de portée utilisateur :

1. Dans le menu gauche de votre application, sélectionnez **Centre de déploiement** > **Informations d’identification FTPS** ou **Informations d’identification Git/FTPS locales**.

    ![Montre comment vous pouvez sélectionner le tableau de bord FTP à partir du Centre de déploiement dans Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Faites défiler jusqu’à **Étendue d'utilisateur**, configurez le **nom d' utilisateur** et le **mot de passe**, puis sélectionnez **Enregistrer**.

Une fois que vous avez défini vos informations d’identification de déploiement, vous trouverez le nom d’utilisateur du déploiement *Git* dans la page **Vue d’ensemble** de votre application.

![Montre comment trouver le nom d’utilisateur du déploiement Git sur la page Vue d’ensemble de votre application.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Si le déploiement Git est configuré, la page présente un **Nom d’utilisateur Git/Déploiement** ; sinon, un **Nom d’utilisateur FTP/Déploiement** est affiché.

> [!NOTE]
> Azure n’affiche pas votre mot de passe du déploiement de portée utilisateur. Si vous oubliez le mot de passe, vous pouvez réinitialiser vos informations d’identification en suivant les étapes décrites dans cette section.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Utiliser les informations d’identification de portée utilisateur avec FTP/FTPS

L’authentification auprès d’un point de terminaison FTP/FTPS à l’aide d’informations d’identification de portée utilisateur nécessite un nom d’utilisateur au format suivant : `<app-name>\<user-name>`

Dans la mesure où les informations d’identification de portée utilisateur sont liées à l’utilisateur et non à une ressource spécifique, le nom d’utilisateur doit respecter ce format pour que l’action de connexion soit dirigée vers le point de terminaison d’application approprié.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Obtenir des informations d’identification de portée application

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Récupérez les informations d’identification de portée application à l’aide de la commande [az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles). Par exemple :

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Pour le [déploiement Git local](deploy-local-git.md), vous pouvez également utiliser la commande [az webapp deployment list-publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) pour obtenir un URI distant Git pour votre application, avec les informations d’identification de portée application déjà incorporées. Par exemple :

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Récupérez les informations d’identification de portée application à l’aide de la commande [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile). Par exemple :

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Dans le menu gauche de votre application, sélectionnez **Centre de déploiement** > **Informations d’identification FTPS** ou **Informations d’identification Git/FTPS locales**.

    ![Montre comment vous pouvez sélectionner le tableau de bord FTP à partir du Centre de déploiement dans Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Dans la section **Étendue de l'application**, sélectionnez le lien **Copier** pour copier le nom d’utilisateur ou le mot de passe.

-----

## <a name="reset-application-scope-credentials"></a>Réinitialiser des informations d’identification de portée application

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Réinitialisez les informations d’identification de portée application à l’aide de la commande [az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) :

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Réinitialisez les informations d’identification de portée application à l’aide de la commande [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) :

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Dans le menu gauche de votre application, sélectionnez **Centre de déploiement** > **Informations d’identification FTPS** ou **Informations d’identification Git/FTPS locales**.

    ![Montre comment vous pouvez sélectionner le tableau de bord FTP à partir du Centre de déploiement dans Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Dans la section **Portée de l'application**, sélectionnez **Réinitialiser**.

-----

## <a name="disable-basic-authentication"></a>Désactiver l’authentification de base

Certaines organisations doivent répondre aux exigences de sécurité et préfèrent désactiver l’accès via FTP ou WebDeploy. De cette manière, les membres de l’organisation peuvent accéder aux services d’application uniquement par le biais d’API contrôlées par Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Pour désactiver l’accès FTP au site, exécutez la commande CLI suivante. Remplacez les espaces réservés par les noms de votre groupe de ressources et de votre site. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Pour confirmer que l’accès FTP est bloqué, vous pouvez essayer de vous authentifier à l’aide d’un client FTP tel que FileZilla. Pour récupérer les informations d’identification de publication, accédez au panneau Vue d’ensemble de votre site et cliquez sur Télécharger le profil de publication. Essayez de vous authentifier en utilisant le nom d’hôte FTP du fichier, le nom d’utilisateur et le mot de passe. Vous obtenez une réponse d’erreur 401, indiquant que vous n’êtes pas autorisé.

### <a name="webdeploy-and-scm"></a>WebDeploy et SCM

Pour désactiver l’accès d’authentification de base au port WebDeploy et au site SCM, exécutez la commande CLI suivante. Remplacez les espaces réservés par les noms de votre groupe de ressources et de votre site. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Pour confirmer que les informations d’identification du profil de publication sont bloquées sur WebDeploy, essayez de [publier une application web à l’aide de Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Désactiver l’accès à l’API

L’API de la section précédente est associée au contrôle d’accès en fonction du rôle Azure (Azure RBAC), ce qui signifie que vous pouvez [créer un rôle personnalisé](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) et affecter des utilisateurs moins privilégiés au rôle afin qu’ils ne puissent pas activer l’authentification de base sur des sites. Pour configurer le rôle personnalisé, [suivez ces instructions](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

Vous pouvez également utiliser [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) pour auditer toutes les demandes d’authentification réussies et utiliser [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) pour appliquer cette configuration à tous les sites de votre abonnement.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser ces informations d’identification pour déployer votre application à partir de [Git local](deploy-local-git.md) ou à l’aide de [FTP/S](deploy-ftp.md).
