---
title: Obtenir l’intention avec un appel REST en Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838510"
---
## <a name="prerequisites"></a>Prérequis

* [JDK SE](https://aka.ms/azure-jdks) (Kit de développement Java, Édition Standard)
* [Visual Studio Code](https://code.visualstudio.com/) ou l’IDE de votre choix.
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Vous pouvez utiliser Java pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente. Veillez à ajouter les bibliothèques Apache à votre projet.

1. Copiez le code suivant pour créer une classe dans un fichier nommé `LuisGetRequest.java` :

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Remplacez la valeur de la variable `YOUR-KEY` par votre clé LUIS.

3. Remplacez le chemin par le chemin du fichier, puis compilez le programme Java à partir de la ligne de commande : `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Remplacez le chemin par le chemin du fichier, puis exécutez l’application à partir d’une ligne de commande : `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

    ![Fenêtre de console affichant le résultat JSON de l’application LUIS](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez terminé ce démarrage rapide, fermez le projet Visual Studio et supprimez le répertoire du projet dans le système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés et entraîner avec Java](../luis-get-started-java-add-utterance.md)