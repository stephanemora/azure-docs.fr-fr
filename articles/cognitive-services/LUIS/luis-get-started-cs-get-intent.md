---
title: Reconnaître l’intention, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dans ce démarrage rapide C#, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir du texte conversationnel.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 81a05ed3e981c2a35221830a16b5859ee5c1bd4f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225514"
---
# <a name="quickstart-get-intent-using-c"></a>Démarrage rapide : Reconnaître l’intention à l’aide de C#

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

    ![Créer une application console dans Visual Studio](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Dans le projet Visual Studio, dans l’Explorateur de Solutions, sélectionnez **Ajouter une référence**, puis sélectionnez **System.Web** depuis l’onglet Assemblys.

    ![sélectionner Ajouter une référence, puis System.Web à partir de l’onglet Assemblys](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

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
