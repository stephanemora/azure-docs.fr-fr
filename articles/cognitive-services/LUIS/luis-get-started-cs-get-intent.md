---
title: Démarrage rapide pour C# - Prédire une intention - LUIS
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir du texte conversationnel. À l’aide de C#, envoyez l’intention de l’utilisateur sous forme de texte au point de terminaison de prédiction HTTP de l’application publique. Au niveau du point de terminaison, LUIS applique le modèle de l’application publique pour analyser le sens du texte en langage naturel, déterminer l’intention globale et extraire les données pertinentes pour le domaine de l’application.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 51c23029cc771db5351575ce329944a9f06dd286
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035842"
---
# <a name="quickstart-get-intent-using-c"></a>Démarrage rapide : Reconnaître une intention à l’aide de C#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prérequis

* [Visual Studio Community Edition 2017](https://visualstudio.microsoft.com/vs/community/)
* Langage de programmation C# (inclus avec VS Community 2017)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Reconnaître une intention avec le navigateur

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Utilisez C# pour interroger l’[API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET du point de terminaison de prédiction pour obtenir les mêmes résultats vus dans la fenêtre du navigateur dans la section précédente. 

1. Créez une application console dans Visual Studio. 

    ![Accès au menu des paramètres utilisateur LUIS](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Dans le projet Visual Studio, dans l’Explorateur de Solutions, sélectionnez **Ajouter une référence**, puis sélectionnez **System.Web** depuis l’onglet Assemblys.

    ![Accès au menu des paramètres utilisateur LUIS](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Remplacez le contenu de Program.cs par le code suivant :
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Remplacez la valeur de `YOUR_KEY` par votre clé LUIS.

5. Créez et exécutez l’application console. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

    ![Fenêtre de console affichant le résultat JSON de l’application LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez terminé ce démarrage rapide, fermez le projet Visual Studio et supprimez le répertoire du projet dans le système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés et effectuer l’apprentissage avec C#](luis-get-started-cs-add-utterance.md)