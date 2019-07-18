---
title: Reconnaître l'intention, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dans ce démarrage rapide Java, utilisez une application LUIS publique disponible pour déterminer l'intention d'un utilisateur à partir du texte conversationnel.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 42240c7b45029684e51c25419eab7f4378785a4d
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276152"
---
# <a name="quickstart-get-intent-using-java"></a>Démarrage rapide : Reconnaître l'intention à l'aide de Java

Dans ce démarrage rapide, vous allez transmettre des énoncés à un point de terminaison LUIS et obtenir en retour une intention et des entités.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prérequis

* [JDK SE](https://aka.ms/azure-jdks) (Kit de développement Java, Édition Standard)
* [Visual Studio Code](https://code.visualstudio.com/) ou l’IDE de votre choix.
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Reconnaître une intention avec le navigateur

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Vous pouvez utiliser Java pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente. Veillez à ajouter les bibliothèques Apache à votre projet.

1. Copiez le code suivant pour créer une classe dans un fichier nommé `LuisGetRequest.java` :

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Remplacez la valeur de la variable `YOUR-KEY` par votre clé LUIS.

3. Remplacez le chemin par le chemin du fichier, puis compilez le programme Java à partir de la ligne de commande : `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Remplacez le chemin par le chemin du fichier, puis exécutez l’application à partir d’une ligne de commande : `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

    ![Fenêtre de console affichant le résultat JSON de l’application LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Supprimez le fichier Java ou le dossier du projet.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-java-add-utterance.md)
