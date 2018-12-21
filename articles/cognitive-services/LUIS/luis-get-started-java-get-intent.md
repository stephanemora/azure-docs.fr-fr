---
title: Reconnaître l'intention, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dans ce démarrage rapide Java, utilisez une application LUIS publique disponible pour déterminer l'intention d'un utilisateur à partir du texte conversationnel.
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c55721e803b85c536067a90019f75dd5aa0f7845
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085346"
---
# <a name="quickstart-get-intent-using-java"></a>Démarrage rapide : Reconnaître l'intention à l'aide de Java

Dans ce démarrage rapide, vous allez transmettre des énoncés à un point de terminaison LUIS et obtenir en retour une intention et des entités.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prérequis

* [JDK SE](https://aka.ms/azure-jdks) (Kit de développement Java, Édition Standard)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Reconnaître une intention avec le navigateur

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation 

Vous pouvez utiliser Java pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente. 

1. Copiez le code suivant pour créer une classe dans un fichier nommé `LuisGetRequest.java` :

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Remplacez la valeur de la variable `YOUR-KEY` par votre clé LUIS.

3. Compilez le programme java avec `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Exécutez l’application avec `java -cp ":lib/*" LuisGetRequest.java`. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

    ![Fenêtre de console affichant le résultat JSON de l’application LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Supprimez le fichier Java. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-java-add-utterance.md)