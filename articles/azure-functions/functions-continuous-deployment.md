---
title: Déploiement continu pour Azure Functions | Microsoft Docs
description: Utilisez les fonctionnalités de déploiement continu d’Azure App Service pour publier vos fonctions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 2dc5fab0966c2ead0276cd8b23ea764bd4f9ef59
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190472"
---
# <a name="continuous-deployment-for-azure-functions"></a>Déploiement continu pour Azure Functions

Azure Functions vous permet de déployer votre code en continu via l’[intégration du contrôle de code source](functions-deployment-technologies.md#source-control). Cela permet d’exploiter un flux de travail où les mises à jour du code déclenchent un déploiement sur Azure. Si vous ne connaissez pas Azure Functions, commencez par consulter l’article [Vue d’ensemble d’Azure Functions](functions-overview.md).

Le déploiement continu est une option intéressante pour les projets auxquels vous intégrez plusieurs contributions fréquentes. Il vous permet également de conserver une seule source de vérité pour votre code de fonction. Vous pouvez configurer un déploiement continu dans Azure Functions à partir des emplacements de code source suivants :

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

L’unité de déploiement pour Azure Functions est la Function App. Cela signifie que toutes les fonctions d’une Function App sont déployées simultanément. Une fois le déploiement continu activé, l’accès au code de fonction dans le portail Azure est configuré sur *lecture seule*, car la source de vérité est définie sur un autre emplacement.

## <a name="requirements-for-continuous-deployment"></a>Exigences pour le déploiement continu

Pour effectuer le déploiement continu avec succès, votre structure de répertoires doit être compatible avec la structure de dossiers de base suivante qui est attendue par Azure Functions :

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Configurer un déploiement continu

Utilisez cette procédure pour configurer le déploiement continu d’une Function App existante. Les étapes suivantes présentent l’intégration à un référentiel GitHub, mais des étapes similaires s’appliquent à Azure Repos ou à d’autres référentiels de code source.

1. Dans votre Function App, dans le [portail Azure](https://portal.azure.com), sélectionnez **Fonctionnalités de la plateforme** > **Centre de déploiement**.

    ![Ouvrir le centre de déploiement](./media/functions-continuous-deployment/platform-features.png)

2. Dans le **Centre de déploiement**, sélectionnez **GitHub**, puis sélectionnez **Autoriser**. Le cas échéant, si vous avez déjà autorisé GitHub, sélectionnez **Continuer**. 

    ![Centre de déploiement](./media/functions-continuous-deployment/github.png)

3. Dans GitHub, sélectionnez **Autoriser AzureAppService**. 

    ![Autorisation](./media/functions-continuous-deployment/authorize.png)
    
    Dans le **Centre de déploiement** du portail Azure, sélectionnez **Continuer**.

4. Sélectionnez l’un des fournisseurs de build suivants :

    * **Service de build App Service** : recommandé lorsque vous n’avez pas besoin d’une build ou si vous avez besoin d’une build générique.
    * **Pipelines Azure (préversion)** : recommandé lorsque vous avez besoin de bénéficier d’un meilleur contrôle sur la build. Ce fournisseur est actuellement en préversion.

    ![Sélectionner un fournisseur de build](./media/functions-continuous-deployment/build.png)

5. Configurez les informations spécifiques à l’option de contrôle de code source que vous avez spécifiée. Pour GitHub, vous devez fournir l’**organisation**, le **référentiel**, et la **branche** où se trouve votre code. Sélectionnez ensuite **Continuer**.

    ![Configurer GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Enfin, vérifiez tous les détails et sélectionnez **Terminer** pour terminer la configuration de votre déploiement.

    ![Résumé](./media/functions-continuous-deployment/summary.png)

Une fois le processus terminé, tout le code de la source spécifiée est déployé sur votre application. À ce stade, les modifications apportées à la source de déploiement déclenchent un déploiement de ces modifications sur votre Function App dans Azure.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Déplacer des fonctions existantes pour un déploiement continu

Si vous avez déjà écrit des fonctions dans le [portail Azure](https://portal.azure.com) et que vous souhaitez télécharger le contenu de votre application avant de basculer sur un déploiement continu, vous devez naviguer jusqu’à l’onglet **Vue d’ensemble** de votre Function App, puis cliquer sur le bouton **Télécharger le contenu de l’application**.

![Télécharger le contenu de l’application](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Après avoir configuré l’intégration continue, vous ne pouvez plus modifier vos fichiers sources dans le portail Functions.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
