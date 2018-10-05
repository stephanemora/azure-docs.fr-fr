---
title: Démarrage rapide pour Javascript - Prédire une intention - LUIS
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir du texte conversationnel. À l’aide de Javascript, envoyez l’intention de l’utilisateur sous forme de texte au point de terminaison de prédiction HTTP de l’application publique. Au niveau du point de terminaison, LUIS applique le modèle de l’application publique pour analyser le sens du texte en langage naturel, déterminer l’intention globale et extraire les données pertinentes pour le domaine de l’application.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: cbbf0d2388f83f4e38f0a63b6bc181d4a24e0aa0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039478"
---
# <a name="quickstart-get-intent-using-javascript"></a>Démarrage rapide : Reconnaître l’intention à l’aide de Javascript

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Prérequis

* [Visual Studio Code](https://code.visualstudio.com/)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Reconnaître une intention avec le navigateur

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Vous pouvez utiliser Javascript pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente. 

1. Copiez le code qui suit et enregistrez-le dans un fichier HTML :

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Ouvrez le fichier dans un navigateur. Saisissez votre clé de point de terminaison LUIS dans le formulaire, puis sélectionnez **Envoyer**.

    ![Exemple HTML affiché dans le navigateur avec les résultats LUIS pour l’application de domotique](./media/luis-get-started-js-get-intent/html-results.png)

    Les résultats s’affichent en dessous du formulaire. 

## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Supprimez le fichier Javascript.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-javascript-add-utterance.md)
