---
title: configurer les informations d’identification de déploiement
description: Découvrez les types d’informations d’identification de déploiement dans Azure App Service et comment les configurer et les utiliser.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c6f7c2422e043da6df498fe81da938576687b916
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649126"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurer les informations d’identification de déploiement pour Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) prend en charge deux types d’informations d’identification pour le [déploiement Git local](deploy-local-git.md) et le [déploiement FTP/S](deploy-ftp.md). Ces informations d’identification ne sont pas les mêmes que les informations d’identification de votre abonnement Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Configurer des informations d’identification au niveau de l’utilisateur

Vous pouvez configurer vos informations d’identification au niveau de l’utilisateur dans la [page Ressources](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) d’une application. Quelle que soit l’application dans laquelle vous configurez ces informations d’identification, ces dernières s’appliquent à toutes les applications et à tous les abonnements de votre compte Azure. 

### <a name="in-the-cloud-shell"></a>Dans Cloud Shell

Pour configurer l’utilisateur de déploiement dans [Cloud Shell](https://shell.azure.com), exécutez la commande [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Remplacez \<username> et \<password> par un nom d’utilisateur et un mot de passe de déploiement. 

- Le nom d’utilisateur doit être unique dans Azure et, pour les push Git locaux, ne doit pas contenir le symbole « @ ». 
- Le mot de passe doit comporter au moins huit caractères et inclure deux des trois éléments suivants : lettres, chiffres et symboles. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

La sortie JSON affiche le mot de passe comme étant `null`. Si vous obtenez une erreur `'Conflict'. Details: 409`, modifiez le nom d’utilisateur. Si vous obtenez une erreur `'Bad Request'. Details: 400`, utilisez un mot de passe plus fort. 

### <a name="in-the-portal"></a>Sur le portail

Sur le portail Azure, vous devez disposer d’au moins une application avant de pouvoir accéder à la page Informations d’identification de déploiement. Pour configurer les informations d’identification au niveau de l’utilisateur :

1. Sur le [portail Azure](https://portal.azure.com), dans le menu de gauche, sélectionnez **App Services** >  **\<une_application>**  > **Centre de déploiement** > **FTP** > **Tableau de bord**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ou bien, si vous avez déjà configuré le déploiement Git, sélectionnez **App Services** >  **&lt;une_application>**  > **Centre de déploiement** > **FTP/Informations d’identification**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Sélectionnez **Informations d’identification de l’utilisateur**, configurez le nom d’utilisateur et le mot de passe, puis sélectionnez **Enregistrer les informations d’identification**.

Une fois que vous avez défini vos informations d’identification de déploiement, vous trouverez le nom d’utilisateur du déploiement *Git* dans la page **Vue d’ensemble** de votre application.

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Si le déploiement Git est configuré, la page présente un **Nom d’utilisateur Git/Déploiement** ; sinon, un **Nom d’utilisateur FTP/Déploiement** est affiché.

> [!NOTE]
> Azure n’affiche pas votre mot de passe du déploiement au niveau de l’utilisateur. Si vous oubliez le mot de passe, vous pouvez réinitialiser vos informations d’identification en suivant les étapes décrites dans cette section.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Utiliser les informations d’identification de niveau utilisateur avec FTP/FTPS

L’authentification auprès d’un point de terminaison FTP/FTPS à l’aide d’informations d’identification de niveau utilisateur nécessite un nom d’utilisateur au format suivant : `<app-name>\<user-name>`

Dans la mesure où les informations d’identification au niveau utilisateur sont liées à l’utilisateur et non à une ressource spécifique, le nom d’utilisateur doit respecter ce format pour que l’action de connexion soit dirigée vers le point de terminaison d’application approprié.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Obtenir et réinitialiser les informations d’identification au niveau de l’application
Pour obtenir les informations d’identification au niveau de l’application :

1. Sur le [portail Azure](https://portal.azure.com), dans le menu de gauche, sélectionnez **App Services** >  **&lt;une_application>**  > **Centre de déploiement** > **FTP/Informations d’identification**.

2. Sélectionnez **Informations d’identification de l’application**, puis cliquez sur le lien **Copier** pour copier le nom d’utilisateur ou le mot de passe.

Pour réinitialiser les informations d’identification de niveau application, sélectionnez **Réinitialiser les informations d’identification** dans la même boîte de dialogue.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser ces informations d’identification pour déployer votre application à partir de [Git local](deploy-local-git.md) ou à l’aide de [FTP/S](deploy-ftp.md).
