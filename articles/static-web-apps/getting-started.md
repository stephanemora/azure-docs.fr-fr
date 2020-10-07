---
title: 'Démarrage rapide : Création de votre première application web statique avec le service Azure Static Web Apps'
description: Apprenez à créer un site web Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88752356"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Démarrage rapide : Création de votre première application web statique

Azure Static Web Apps publie un site web dans un environnement de production en compilant les applications à partir d’un dépôt GitHub. Dans ce guide de démarrage rapide, vous déployez une application web dans Azure Static Web Apps à l’aide de l’extension Visual Studio Code.

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prérequis

- [GitHub](https://github.com)
- Compte [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extension Azure Static Web Apps pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ensuite, ouvrez Visual Studio Code et accédez à **Fichier > Ouvrir le dossier** pour ouvrir le dépôt que vous venez de cloner sur votre machine dans l’éditeur.

## <a name="create-a-static-web-app"></a>Créer une application web statique

1. Dans Visual Studio Code, sélectionnez le logo Azure dans la barre d’activités pour ouvrir la fenêtre des extensions Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo Azure":::

    > [!NOTE]
    > Les connexions à Azure et GitHub sont nécessaires. Si vous n’êtes pas déjà connecté à Azure et à GitHub depuis Visual Studio Code, l’extension vous invite à le faire au cours du processus de création.

1. Placez le pointeur de la souris sur l’étiquette _Static Web Apps_ et sélectionnez le **signe plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Logo Azure":::

1. La palette de commandes s’ouvre en haut de l’éditeur et vous invite à nommer votre application.

    Tapez **my-first-static-web-app** et appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Logo Azure":::

1. Sélectionnez la branche **master** (principale), puis appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Logo Azure":::

1. Sélectionnez **/** comme emplacement pour le code de l’application, puis appuyez sur **entrer**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Logo Azure":::

1. L’extension recherche l’emplacement de l’API dans votre application. Cet article n’implémente pas d’API.

    Sélectionnez **Skip for now** (Ignorer pour le moment) et appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Logo Azure":::

1. Sélectionnez l’emplacement où les fichiers sont générés pour la production dans votre application.

    # <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

    Décochez la case et appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Logo Azure":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Tapez **dist/angular-basic** et appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Logo Azure":::

    # <a name="react"></a>[React](#tab/react)

    Tapez **build**, puis appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Logo Azure":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Tapez **dist**, puis appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Logo Azure":::

    ---

1. Sélectionnez l’emplacement le plus proche de vous et appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Logo Azure":::

1. Une fois l’application créée, une notification de confirmation s’affiche dans Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Logo Azure":::

1. Dans la fenêtre de l’Explorateur Visual Studio Code, revenez à la section _Static Web Apps_ et cliquez avec le bouton droit sur **Production**, puis sélectionnez **Ouvrir dans le portail** pour afficher l’application dans le portail Azure.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Logo Azure":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Static Web Apps par le biais de l’extension.

Dans la fenêtre de l’Explorateur Visual Studio Code, revenez à la section _Static Web Apps_ et cliquez avec le bouton droit sur **my-first-static-web-app**, puis sélectionnez **Supprimer**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Logo Azure":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
