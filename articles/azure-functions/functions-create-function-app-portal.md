---
title: Créer votre première fonction à l’aide du Portail Azure
description: Apprenez à créer votre première fonction Azure pour une exécution sans serveur à l’aide du portail Azure.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 6ca187181d68d7924f97ebfac08e81dec77251e9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492112"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Créer votre première fonction à l’aide du Portail Azure

Azure Functions vous permet d’exécuter votre code dans un environnement serverless, et sans avoir à créer une machine virtuelle ou à publier une application web au préalable. Dans cet article, vous allez découvrir comment utiliser Azure Functions pour créer une fonction de déclencheur via HTTP, « Hello world », dans le Portail Azure.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

Nous vous recommandons plutôt de [développer vos fonctions localement](functions-develop-local.md) et de les publier dans une application de fonction dans Azure.  
Utilisez l’un des liens suivants pour commencer à utiliser l’environnement de développement et le langage de votre choix :

| Visual Studio Code | Terminal/invite de commandes | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Prise en main de C#](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Prise en main de Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Prise en main de JavaScript](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Bien démarrer avec PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Bien démarrer avec Python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Prise en main de C#](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Prise en main de Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Prise en main de JavaScript](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Bien démarrer avec PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Bien démarrer avec Python](./create-first-function-cli-python.md) | [Prise en main de C#](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. Une application de fonctions vous permet de regrouper des fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Créez ensuite une fonction dans la nouvelle application de fonction.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Créer une fonction de déclencheur HTTP

1. Dans le menu de gauche de la fenêtre **Fonctions**, sélectionnez **Fonctions**, puis **Ajouter** dans le menu supérieur. 
 
1. Dans la fenêtre **Ajouter une fonction**, sélectionnez le modèle **Déclencheur HTTP**.

    ![Choisir une fonction de déclencheur HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Sous **Détails du modèle**, utilisez `HttpExample` pour **Nouvelle fonction**, choisissez **Anonyme** dans la liste déroulante **[Niveau d’autorisation](functions-bindings-http-webhook-trigger.md#authorization-keys)** , puis sélectionnez **Ajouter**.

    Azure crée la fonction de déclencheur HTTP. Vous pouvez maintenant exécuter la nouvelle fonction en envoyant une requête HTTP.

## <a name="test-the-function"></a>Tester la fonction

1. Dans votre nouvelle fonction de déclencheur HTTP, sélectionnez **Code + test** dans le menu de gauche, puis sélectionnez **Obtenir l’URL de la fonction** dans le menu supérieur.

    ![Sélectionner Obtenir l’URL de la fonction](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. Dans la boîte de dialogue **Obtenir l’URL de la fonction**, sélectionnez **Par défaut** dans la liste déroulante, puis choisissez l’icône **Copier dans le Presse-papiers.** . 

    ![Copier l’URL de fonction à partir du portail Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Collez l’URL de fonction dans la barre d’adresse de votre navigateur. Ajoutez la valeur de la chaîne de requête `?name=<your_name>` à la fin de cette URL, puis appuyez sur la touche Entrée pour exécuter la requête. 

    L’exemple suivant montre la réponse dans le navigateur :

    ![Réponse de la fonction dans le navigateur.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Si l’URL de la demande incluait une [clé d’accès](functions-bindings-http-webhook-trigger.md#authorization-keys) (`?code=...`), cela signifie que vous avez choisi le niveau d’accès **Fonction** au lieu de l’option **Anonyme** lors de la création de la fonction. Dans ce cas, vous devez ajouter `&name=<your_name>`.

1. Lorsque votre fonction s’exécute, des informations de suivi sont écrites dans les journaux d’activité. Pour afficher la sortie de suivi, revenez à la page **Code + test** dans le portail, puis développez la flèche **Journaux** en bas de la page.

   ![Affichage des journaux de fonction dans le portail Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
