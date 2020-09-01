---
title: 'Démarrage rapide : Création de votre première application web statique avec Azure Static Web Apps à l’aide du portail Azure'
description: Apprenez à créer une instance Azure Static Web Apps avec le portail Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: e0b78c5e053c5668fbebd8ebaac91a90aa2b364f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752821"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>Démarrage rapide : Création de votre première application web statique dans le portail Azure

Azure Static Web Apps publie un site web dans un environnement de production en compilant les applications à partir d’un dépôt GitHub. Dans ce guide de démarrage rapide, vous déployez une application web dans Azure Static Web Apps à l’aide du portail.

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prérequis

- [GitHub](https://github.com)
- Compte [Azure](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Créer une application web statique

Maintenant que le référentiel est créé, vous pouvez créer une application web statique à partir du Portail Azure.

1. Accéder au [portail Azure](https://portal.azure.com)
1. Sélectionnez **Créer une ressource**
1. Recherchez **Applications web statiques**
1. Sélectionnez **Static Web Apps (Préversion)**
1. Sélectionnez **Créer**

Sous l’onglet _De base_, commencez par configurer votre nouvelle application et liez-la à un dépôt GitHub.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Onglet Informations de base":::

1. Sélectionnez votre _abonnement Azure_.
1. Sélectionnez ou créez un _groupe de ressources_.
1. Nommez l’application **my-first-static-web-app**.
      1. Les caractères valides sont `a-z` (insensible à la casse), `0-9`et `-`.
1. Sélectionnez la _région_ la plus proche de vous.
1. Sélectionnez **SKU** _gratuite_.
1. Sélectionnez le bouton **Se connecter à GitHub** et authentifiez-vous auprès de GitHub.

Après vous être connecté à GitHub, entrez les informations relatives au dépôt.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Détails du référentiel":::

1. Sélectionnez votre _organisation_ préférée.
1. Sélectionnez **my-first-web-static-app** dans la liste déroulante _Référentiel_.
1. Sélectionner **maître** dans la liste déroulante _Branche_.
1. Sélectionnez le bouton **Suivant : Build >** (Suivant : générer >) pour modifier la configuration de la génération.

:::image type="content" source="media/getting-started-portal/next-build-button.png" alt-text="Bouton Compilation suivante":::

> [!NOTE]
> En l’absence de dépôt, il peut être nécessaire d’autoriser Azure Static Web Apps dans GitHub. Accédez à votre dépôt GitHub, puis à **Paramètres > Applications > Applications OAuth autorisées**, sélectionnez **Azure Static Web Apps**, puis sélectionnez **Octroyer**. Pour les dépôts de l’organisation, vous devez être propriétaire de l’organisation pour accorder des autorisations.

1. Sous l’onglet _Générer_, ajoutez les détails de configuration spécifiques à votre framework front-end favori.

    # <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

    - Désactivez la valeur par défaut dans la zone _Emplacement de l’application_.
    - Désactivez la valeur par défaut de la zone _Emplacement de l’API_.
    - Désactivez la valeur par défaut de la zone _Emplacement de l’artefact d’application_.

    # <a name="angular"></a>[Angular](#tab/angular)

    - Désactivez la valeur par défaut dans la zone _Emplacement de l’application_.
    - Désactivez la valeur par défaut de la zone _Emplacement de l’API_.
    - Tapez **dist/angular-basic** dans la zone _Emplacement de l’artefact de l’application_.

    # <a name="react"></a>[React](#tab/react)

    - Désactivez la valeur par défaut dans la zone _Emplacement de l’application_.
    - Désactivez la valeur par défaut de la zone _Emplacement de l’API_.
    - Tapez **build** dans la zone _Emplacement de l’artefact de l’application_.

    # <a name="vue"></a>[Vue](#tab/vue)

    - Désactivez la valeur par défaut dans la zone _Emplacement de l’application_.
    - Désactivez la valeur par défaut de la zone _Emplacement de l’API_.
    - Tapez **dist** dans la zone _Emplacement de l’artefact de l’application_.

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

1. Ouvrez le [portail Azure](https://portal.azure.com)
1. Recherchez **my-first-web-static-app** dans la barre de recherche supérieure.
1. Sélectionnez le nom de l’application.
1. Sélectionnez le bouton **Supprimer**.
1. Sélectionnez **Oui** pour confirmer la suppression.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
