---
title: 'Démarrage rapide : Création de votre premier site statique avec le service Azure Static Web Apps'
description: Apprenez à déployer un site statique sur Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483840"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Démarrage rapide : Création de votre premier site statique avec Azure Static Web Apps

Azure Static Web Apps publie un site web en créant des applications à partir d'un référentiel de code. Dans ce guide de démarrage rapide, vous allez déployer une application dans Azure Static Web Apps à l'aide de l'extension Visual Studio Code.

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prérequis

- [GitHub](https://github.com)
- Compte [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extension Azure Static Web Apps pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Installez Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ensuite, ouvrez Visual Studio Code et accédez à **Fichier > Ouvrir le dossier** pour ouvrir le référentiel que vous avez cloné sur votre machine dans l'éditeur.

## <a name="create-a-static-web-app"></a>Créer une application web statique

1. Dans Visual Studio Code, sélectionnez le logo Azure dans la barre d’activités pour ouvrir la fenêtre des extensions Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo Azure":::

    > [!NOTE]
    > Les connexions à Azure et GitHub sont nécessaires. Si vous n’êtes pas déjà connecté à Azure et à GitHub depuis Visual Studio Code, l’extension vous invite à le faire au cours du processus de création.

1. Sous l'étiquette _Static Web Apps_, sélectionnez le **signe plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nom de l’application":::

1. La palette de commandes s’ouvre en haut de l’éditeur et vous invite à nommer votre application.

    Tapez **my-first-static-web-app** et appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Création d’application web statique":::

1. Sélectionnez les présélections correspondant à votre type d'application.

    # <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Présélections d'application : Pas de framework":::

    Entrez **./** comme emplacement des fichiers d'application.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Emplacement des fichiers d'application":::

    Sélectionnez **Ignorer pour le moment** pour l'emplacement de l'API Azure Functions.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Emplacement de l’API":::

    Entrez **./** pour l'emplacement de sortie de la build.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Emplacement de sortie de la build d'application":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Même s’il y a une présélection Angular, sélectionnez l’option **Personnalisé** afin de fournir un emplacement de sortie qui soit adapté à cette application.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Présélections d'application : Angular":::

    Entrez **./** comme emplacement des fichiers d'application.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Emplacement des fichiers d'application : Angular":::

    Sélectionnez **Ignorer pour le moment** pour l'emplacement de l'API Azure Functions.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Emplacement de l’API : Angular":::

    Entrez **dist/angular-basic** pour l’emplacement de sortie de la build.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Emplacement de sortie de la build d'application : Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Présélections d'application : React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Présélections d'application : Vue":::

    ---

1. Sélectionnez l’emplacement le plus proche de vous et appuyez sur **Entrée**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Emplacement de la ressource":::

1. Une fois l’application créée, une notification de confirmation s’affiche dans Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Confirmation créée":::

    Cliquez ensuite sur le bouton **Ouvrir les actions dans GitHub**. Cette page affiche l'état de la build de l'application.

    Une fois l'action GitHub terminée, vous pouvez accéder au site web publié.

1. Pour afficher le site web dans le navigateur, cliquez avec le bouton droit sur le projet dans l'extension Static Web Apps, puis sélectionnez **Parcourir le site**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Parcourir le site":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Static Web Apps par le biais de l’extension.

Dans la fenêtre de l’Explorateur Visual Studio Code, revenez à la section _Static Web Apps_ et cliquez avec le bouton droit sur **my-first-static-web-app**, puis sélectionnez **Supprimer**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Supprimer l’application":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
