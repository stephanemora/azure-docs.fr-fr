---
title: Obtenir l’intention avec un appel REST en C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e6ae9590cee3a2ddc3b8e121161fcf84815da28a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838564"
---
## <a name="prerequisites"></a>Prérequis

* [Visual Studio Community Edition 2017](https://visualstudio.microsoft.com/vs/community/)
* Langage de programmation C# (inclus avec VS Community 2017)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Utilisez C# pour interroger l’[API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET du point de terminaison de prédiction pour obtenir les mêmes résultats vus dans la fenêtre du navigateur dans la section précédente. 

1. Créez une application console dans Visual Studio. 

    ![Créer une application console dans Visual Studio](../media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Dans le projet Visual Studio, dans l’Explorateur de Solutions, sélectionnez **Ajouter une référence**, puis sélectionnez **System.Web** depuis l’onglet Assemblys.

    ![sélectionner Ajouter une référence, puis System.Web à partir de l’onglet Assemblys](../media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Remplacez le contenu de Program.cs par le code suivant :
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Remplacez la valeur de `YOUR_KEY` par votre clé LUIS.

5. Créez et exécutez l’application console. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

    ![Fenêtre de console affichant le résultat JSON de l’application LUIS](../media/luis-get-started-cs-get-intent/console-turn-on.png)



## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez terminé ce démarrage rapide, fermez le projet Visual Studio et supprimez le répertoire du projet dans le système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés et effectuer l’apprentissage avec C#](../luis-get-started-cs-add-utterance.md)