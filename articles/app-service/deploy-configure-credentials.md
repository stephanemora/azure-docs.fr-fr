---
title: Configurer les informations d’identification de déploiement - Azure App Service | Microsoft Docs
description: Apprenez à utiliser les informations d’identification du déploiement d’Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/22/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a92440e97f47f0778eb73b81b239b45476d4e733
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550874"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurer les informations d’identification de déploiement pour Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) prend en charge deux types d’informations d’identification pour le [déploiement Git local](deploy-local-git.md) et le [déploiement FTP/S](deploy-ftp.md). Ils ne sont pas les mêmes que vos informations d’identification Azure Active Directory.

* **Informations d’identification au niveau de l’utilisateur** : un seul ensemble d’informations d’identification pour l’intégralité du compte Azure. Il peut être utilisé pour déployer sur App Service pour n’importe quelle application et dans n’importe quel abonnement auxquels le compte Azure est autorisé à accéder. C’est l’ensemble par défaut qui est présenté dans l’interface utilisateur graphique du portail, comme la **vue d’ensemble** et les **propriétés** de la [page Ressources](../azure-resource-manager/resource-group-portal.md#manage-resources) de l’application. Quand un utilisateur est autorisé à accéder à l’application via le contrôle d’accès en fonction du rôle (RBAC) ou des autorisations de coadmin, il peut se servir de ses propres informations d’identification tant que l’accès n’est pas révoqué. Ne partagez pas ces informations d’identification avec d’autres utilisateurs d’Azure.

* **Informations d’identification au niveau de l’application** : un seul ensemble d’informations d’identification pour chaque application. Celui-ci peut être utilisé pour déployer sur cette application uniquement. Les informations d’identification de chaque application sont générées automatiquement à la création de l’application. Elles ne peuvent pas être configurées manuellement, mais peuvent être réinitialisées à tout moment. Pour qu’un utilisateur puisse accéder aux informations d’identification de niveau application via RBAC, il doit avoir un rôle de contributeur ou supérieur sur l’application. Les lecteurs ne sont pas autorisés à publier et n’ont pas accès à ces informations d’identification.

## <a name="userscope"></a>Définir et réinitialiser les informations d’identification au niveau de l’utilisateur

Vous pouvez configurer vos informations d’identification au niveau de l’utilisateur dans la [page Ressources](../azure-resource-manager/resource-group-portal.md#manage-resources) d’une application. Quelle que soit l’application dans laquelle vous configurez ces informations d’identification, ces dernières s’appliquent à toutes les applications et à tous les abonnements de votre compte Azure. 

Pour configurer les informations d’identification au niveau de l’utilisateur :

1. Dans le [portail Azure](https://portal.azure.com), dans le menu de gauche, cliquez sur **App Services** > **&lt;une_application>** > **Centre de déploiement** > **Informations d’identification de déploiement**.

    Dans le portail, vous devez disposer d’au moins une application avant de pouvoir accéder à la page Informations d’identification de déploiement. Toutefois, avec l’[interface de ligne de commande Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), vous pouvez configurer les informations d’identification au niveau de l’utilisateur sans application existante.

2. Cliquez sur **Informations d’identification de l’utilisateur**, configurez le nom d’utilisateur et le mot de passe, puis cliquez sur **Enregistrer les informations d’identification**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Une fois que vous avez défini vos informations d’identification de déploiement, vous pouvez trouver le nom d’utilisateur du déploiement *Git* dans la **vue d’ensemble** de votre application

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

et le nom d’utilisateur de déploiement *FTP* dans les **propriétés** de votre application.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure n’affiche pas votre mot de passe du déploiement au niveau de l’utilisateur. Si vous oubliez le mot de passe, vous pouvez réinitialiser vos informations d’identification en suivant les étapes décrites dans cette section.
>
>  

## <a name="appscope"></a>Obtenir et réinitialiser les informations d’identification au niveau de l’application
Pour obtenir les informations d’identification au niveau de l’application :

1. Dans le [portail Azure](https://portal.azure.com), dans le menu de gauche, cliquez sur **App Services** > **&lt;une_application>** > **Centre de déploiement** > **Informations d’identification de déploiement**.

2. Cliquez sur **Informations d’identification de l’application**, puis sur le lien **Copier** pour copier le nom d’utilisateur ou le mot de passe.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Pour réinitialiser les informations d’identification de niveau application, cliquez sur **Réinitialiser les informations d’identification** dans la même boîte de dialogue.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser ces informations d’identification pour déployer votre application à partir de [Git local](deploy-local-git.md) ou à l’aide de [FTP/S](deploy-ftp.md).
