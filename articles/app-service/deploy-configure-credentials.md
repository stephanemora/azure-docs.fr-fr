---
title: configurer les informations d’identification de déploiement
description: Découvrez les types d’informations d’identification de déploiement dans Azure App Service et comment les configurer et les utiliser.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 15b556781f8ba620ab61c502b6f55e55e98df83c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430529"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurer les informations d’identification de déploiement pour Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) prend en charge deux types d’informations d’identification pour le [déploiement Git local](deploy-local-git.md) et le [déploiement FTP/S](deploy-ftp.md). Ces informations d’identification ne sont pas les mêmes que les informations d’identification de votre abonnement Azure.

* **Informations d’identification au niveau de l’utilisateur** : un seul ensemble d’informations d’identification pour l’intégralité du compte Azure. Il peut être utilisé pour déployer sur App Service pour n’importe quelle application et dans n’importe quel abonnement auxquels le compte Azure est autorisé à accéder. C’est l’ensemble par défaut qui est présenté dans l’interface utilisateur graphique du portail, comme la **vue d’ensemble** et les **propriétés** de la [page Ressources](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) de l’application. Lorsqu'un utilisateur est autorisé à accéder à l’application via le contrôle d’accès en fonction du rôle (RBAC) ou des autorisations de coadmin, il peut se servir de ses propres informations d’identification tant que l’accès n’est pas révoqué. Ne partagez pas ces informations d’identification avec d’autres utilisateurs d’Azure.

* **Informations d’identification au niveau de l’application** : un seul ensemble d’informations d’identification pour chaque application. Celui-ci peut être utilisé pour déployer sur cette application uniquement. Les informations d’identification de chaque application sont générées automatiquement à la création de l’application. Elles ne peuvent pas être configurées manuellement, mais peuvent être réinitialisées à tout moment. Pour qu’un utilisateur puisse accéder aux informations d’identification de niveau application via RBAC, il doit avoir un rôle de contributeur ou supérieur sur l’application. Les lecteurs ne sont pas autorisés à publier et n’ont pas accès à ces informations d’identification.

## <a name="userscope"></a>Configurer des informations d’identification au niveau de l’utilisateur

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

## <a name="appscope"></a>Obtenir et réinitialiser les informations d’identification au niveau de l’application
Pour obtenir les informations d’identification au niveau de l’application :

1. Sur le [portail Azure](https://portal.azure.com), dans le menu de gauche, sélectionnez **App Services** >  **&lt;une_application>**  > **Centre de déploiement** > **FTP/Informations d’identification**.

2. Sélectionnez **Informations d’identification de l’application**, puis cliquez sur le lien **Copier** pour copier le nom d’utilisateur ou le mot de passe.

Pour réinitialiser les informations d’identification de niveau application, sélectionnez **Réinitialiser les informations d’identification** dans la même boîte de dialogue.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser ces informations d’identification pour déployer votre application à partir de [Git local](deploy-local-git.md) ou à l’aide de [FTP/S](deploy-ftp.md).
