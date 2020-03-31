---
title: Déploiement continu pour Azure Functions
description: Utilisez les fonctionnalités de déploiement continu d’Azure App Service pour publier vos fonctions.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79235021"
---
# <a name="continuous-deployment-for-azure-functions"></a>Déploiement continu pour Azure Functions

Azure Functions vous permet de déployer votre code en continu via une [intégration du contrôle de code source](functions-deployment-technologies.md#source-control). L’intégration du contrôle de code source active un flux de travail dans lequel une mise à jour de code déclenche un déploiement sur Azure. Si vous ne connaissez pas encore Azure Functions, commencez par consulter l’article [Vue d’ensemble d’Azure Functions](functions-overview.md).

Le déploiement continu est une option intéressante pour des projets dans le cadre desquels vous intégrez des contributions multiples et fréquentes. Avec un déploiement continu, vous maintenez une source fidèle unique de votre code, qui permet aux équipes de collaborer facilement. Vous pouvez configurer un déploiement continu dans Azure Functions à partir des emplacements de code source suivants :

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

L’unité de déploiement de fonctions dans Azure est la Function App. Toutes les fonctions incorporées dans une Function App sont déployées simultanément. Une fois le déploiement continu activé, l’accès au code de fonction sur le portail Azure est configuré en *lecture seule*, car l’emplacement défini pour la source fidèle est situé ailleurs.

## <a name="requirements-for-continuous-deployment"></a>Exigences pour le déploiement continu

Pour que le déploiement continu fonctionne correctement, votre structure de répertoires doit être compatible avec la structure de dossiers de base attendue par Azure Functions.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Le déploiement continu n’est pas encore pris en charge pour les applications Linux exécutées sur un plan Consommation. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Configurer un déploiement continu

Pour configurer le déploiement continu d’une Function App existante, procédez comme suit. Ces étapes suivantes présentent l’intégration avec un dépôt GitHub, mais des étapes similaires s’appliquent aux Azure Repos ou à d’autres référentiels de code source.

1. Dans votre Function App sur le [portail Azure](https://portal.azure.com), sélectionnez **Fonctionnalités de la plateforme** > **Centre de déploiement**.

    ![Ouvrir le centre de déploiement](./media/functions-continuous-deployment/platform-features.png)

2. Dans le **Centre de déploiement**, sélectionnez **GitHub**, puis **Autoriser**. Si vous avez déjà autorisé GitHub, sélectionnez **Continuer**. 

    ![Centre de déploiement Azure App Service](./media/functions-continuous-deployment/github.png)

3. Dans GitHub, sélectionnez le bouton **Autoriser Azure App Service**. 

    ![Autoriser Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    Dans le **Centre de déploiement** sur le portail Azure, sélectionnez **Continuer**.

4. Sélectionnez l’un des fournisseurs de build suivants :

    * **Service de build App Service** : idéal si vous n’avez pas besoin d’une build ou si vous avez besoin d’une build générique.
    * **Azure Pipelines (Préversion)**  : idéal si vous avez besoin de davantage de contrôle sur la build. Ce fournisseur est actuellement en préversion.

    ![Sélectionner un fournisseur de build](./media/functions-continuous-deployment/build.png)

5. Configurez les informations spécifiques à l’option de contrôle de code source que vous avez spécifiée. Pour GitHub, vous devez entrer ou sélectionner des valeurs pour **Organisation**, **Dépôt** et **Branche**. Les valeurs sont basées sur l’emplacement de votre code. Sélectionnez ensuite **Continuer**.

    ![Configurer GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Vérifiez tous les détails, puis sélectionnez **Terminer** pour achever la configuration de votre déploiement.

    ![Résumé](./media/functions-continuous-deployment/summary.png)

Une fois le processus terminé, tout le code de la source spécifiée est déployé sur votre application. À ce stade, les modifications apportées à la source de déploiement déclenchent un déploiement de ces modifications sur votre Function App dans Azure.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Déplacer des fonctions existantes pour un déploiement continu

Si vous avez déjà écrit des fonctions sur le [portail Azure](https://portal.azure.com) et souhaitez télécharger le contenu de votre application avant de passer au déploiement continu, accédez à l’onglet **Vue d’ensemble** de votre Function App. Sélectionnez le bouton **Télécharger le contenu de l’application**.

![Télécharger le contenu de l’application](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Après avoir configuré l’intégration continue, vous ne pouvez plus modifier vos fichiers sources dans le portail Functions.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
