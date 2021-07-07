---
title: 'Démarrage rapide : Création de votre première application web statique avec Azure Static Web Apps à l’aide du portail Azure'
description: Apprenez à déployer un site statique sur Azure Static Web Apps avec le portail Azure.
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 05/07/2021
ms.topic: quickstart
ms.service: static-web-apps
ms.custom:
- mode-portal
ms.openlocfilehash: ce4c37091fc56398c9986396d56e2cc66f9b1d8a
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109813927"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Démarrage rapide : Création de votre premier site statique dans le portail Azure

Azure Static Web Apps publie un site web dans un environnement de production en compilant les applications à partir d’un dépôt GitHub. Dans ce démarrage rapide, vous allez déployer une application web dans Azure Static Web Apps à partir du portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prérequis

- [GitHub](https://github.com)
- Compte [Azure](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Créer une application web statique

Maintenant que le référentiel est créé, vous pouvez créer une application web statique à partir du Portail Azure.

1. Accédez au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Créer une ressource**.
1. Recherchez **Static Web Apps**.
1. Sélectionnez **Static Web Apps**.
1. Sélectionnez **Create** (Créer).

Dans la section _De base_, commencez par configurer votre nouvelle application et liez-la à un référentiel GitHub.

:::image type="content" source="media/getting-started-portal/quickstart-portal-basics.png" alt-text="Section De base":::

1. Sélectionnez votre _abonnement Azure_.
1. En regard de _Groupe de ressources_, sélectionnez le lien **Créer**.
1. Entrez **static-web-apps-test** dans la zone de texte.
1. Sous _Détails de l’application web statique_, entrez **my-first-static-web-app** dans la zone de texte.
1. Sous _Azure Functions et détails de la gestion intermédiaire_, sélectionnez une région proche de vous.
1. Sous _Détails du déploiement_, sélectionnez **GitHub**.
1. Sélectionnez le bouton **Se connecter avec GitHub** et authentifiez-vous auprès de GitHub.

Après vous être connecté à GitHub, entrez les informations relatives au dépôt.

:::image type="content" source="media/getting-started-portal/quickstart-portal-source-control.png" alt-text="Détails du référentiel":::

1. Sélectionnez le nom de l’_Organisation_ de votre choix.
1. Sélectionnez **my-first-web-static-app** dans la liste déroulante _Référentiel_.
1. Sélectionnez **principale** dans la liste déroulante _Branche_.

   > [!NOTE]
   > En l’absence de dépôt, il peut être nécessaire d’autoriser Azure Static Web Apps dans GitHub. Accédez à votre dépôt GitHub, puis à **Paramètres > Applications > Applications OAuth autorisées**, sélectionnez **Azure Static Web Apps**, puis sélectionnez **Octroyer**. Pour les dépôts de l’organisation, vous devez être propriétaire de l’organisation pour accorder des autorisations.

1. Dans la section _Détails de build_, ajoutez les détails de configuration propres à votre framework front-end favori.

    # <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

    1. Sélectionnez **Personnalisé** dans la liste déroulante _Build Presets_ (Présélections de build).
    1. Conservez la valeur par défaut dans la zone _Emplacement de l’application_.
    1. Laissez la zone _Emplacement de l’API_ vide.
    1. Laissez la zone _Emplacement de l’artefact de l’application_ vide.

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Sélectionnez **Angular** dans la liste déroulante _Build Presets_ (Présélections de build).
    1. Conservez la valeur par défaut dans la zone _Emplacement de l’application_.
    1. Laissez la zone _Emplacement de l’API_ vide.
    1. Tapez **dist/angular-basic** dans la zone _Emplacement de l’artefact de l’application_.

    # <a name="react"></a>[React](#tab/react)

    1. Sélectionnez **React** dans la liste déroulante _Build Presets_ (Présélections de build).
    1. Conservez la valeur par défaut dans la zone _Emplacement de l’application_.
    1. Laissez la zone _Emplacement de l’API_ vide.
    1. Tapez **build** dans la zone _Emplacement de l’artefact de l’application_.

    # <a name="vue"></a>[Vue](#tab/vue)

    1. Sélectionnez **Vue.js** dans la liste déroulante _Présélections de build_.
    1. Conservez la valeur par défaut dans la zone _Emplacement de l’application_.
    1. Laissez la zone _Emplacement de l’API_ vide.
    1. Conservez la valeur par défaut dans la zone _Emplacement de l’artefact de l’application_.

    ---

1. Sélectionnez **Revoir + créer**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Bouton Vérifier + créer":::

    > [!NOTE]
    > Vous pouvez modifier le [fichier de workflow](github-actions-workflow.md) pour changer ces valeurs après avoir créé l’application.

1. Sélectionnez **Create** (Créer).

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Bouton Créer":::

1. Sélectionnez **Accéder à la ressource**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Accédez au bouton de ressources":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Static Web Apps en effectuant les étapes suivantes :

1. Ouvrez le [Portail Azure](https://portal.azure.com).
1. Recherchez **my-first-web-static-app** dans la barre de recherche supérieure.
1. Sélectionnez le nom de l’application.
1. Sélectionnez le bouton **Supprimer**.
1. Sélectionnez **Oui** pour confirmer l’action de suppression (cette action peut prendre quelques minutes).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
