---
title: Modifier, effectuer l'apprentissage d'une application, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dans ce démarrage rapide de Java, vous allez ajouter des exemples d’énoncés à une application de domotique et effectuer l’apprentissage de l’application.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 206b345fedb033a6b98e350fec8c66a3496f5236
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080824"
---
# <a name="quickstart-change-model-using-java"></a>Démarrage rapide : Modifier un modèle à l'aide de Java 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks) (Kit de développement Java, Édition Standard)
* [Bibliothèque JSON GSON de Google](https://github.com/google/gson).

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Créer un code de démarrage rapide

1. Ajoutez les dépendances Java au fichier nommé `AddUtterances.java`.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. Créez la classe `AddUtterances`. Cette classe contient tous les extraits de code qui suivent.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. Ajoutez les constantes LUIS à la classe. Copiez le code suivant et remplacez les valeurs par votre clé de création, ID d’application et ID de version.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. Ajoutez la méthode d’appel de l’API LUIS à la classe AddUtterances. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. Ajoutez la méthode de réponse HTTP de l’API LUIS à la classe AddUtterances.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. Ajoutez la méthode de gestion des exceptions à la classe AddUtterances. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. Ajoutez la fonction principale à la classe AddUtterances.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>Générer le code

Compiler AddUtterance avec les dépendances

```console
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>Exécuter le code
Appeler `AddUtterance` sans argument permet d’ajouter les énoncés LUIS à l’application, sans apprentissage.

```console
> java -classpath .;gson-2.8.2.jar AddUtterances
```

Cette ligne de commande affiche les résultats de l’appel de l’API d’ajout d’énoncés. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources
Une fois le démarrage rapide terminé, supprimez tous les fichiers créés pour ce démarrage rapide. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Créer une application LUIS par programme](luis-tutorial-node-import-utterances-csv.md) 