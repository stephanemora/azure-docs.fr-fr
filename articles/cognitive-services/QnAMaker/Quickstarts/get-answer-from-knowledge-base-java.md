---
title: 'Démarrage rapide : Obtenir des réponses d’une base de connaissances - REST, Java - QnA Maker'
description: Ce guide de démarrage rapide basé sur REST Java vous aide à obtenir programmatiquement une réponse à partir d’une base de connaissances.
ms.topic: quickstart
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCHANGE-20200128
ms.openlocfilehash: 96b8b870dc82bfb08aca2a1d1acb2958ff7eb2f3
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109884"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Démarrage rapide : Obtenir des réponses à une question à partir d’une base de connaissance avec Java

Ce guide de démarrage rapide vous aide à obtenir programmatiquement une réponse à partir d’une base de connaissances QnA Maker publiée. La base de connaissances contient des questions et réponses de [sources de données](../Concepts/knowledge-base.md) telles que des FAQ. La [question](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) est envoyée au service QnA Maker. La [réponse](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclut la réponse la plus prévisible.

[Documentation de référence](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Exemple](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Conditions préalables requises

* [JDK SE](https://aka.ms/azure-jdks) (Kit de développement Java, Édition Standard)
* Cet exemple utilise le [client HTTP](https://hc.apache.org/httpcomponents-client-ga/) Apache à partir de composants HTTP. Vous devez ajouter les bibliothèques clientes HTTP Apache suivantes à votre projet :
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord Azure pour votre ressource QnA Maker.
* Paramètres de la page **Publier**. Si vous ne disposez pas d’une base de connaissances publiée, créez une base de connaissances vide, importez une base de connaissances sur la page **Paramètres**, puis publiez. Vous pouvez télécharger et utiliser [cette base de connaissances](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    Les paramètres de la page de publication incluent la valeur de route POST, la valeur d’hôte et la valeur EndpointKey.

    ![Paramètres de publication](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Créer un fichier Java

Ouvrez VSCode et créez un fichier nommé `GetAnswer.java`, puis ajoutez la classe suivante :

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

Ce guide de démarrage rapide utilise des classes Apache pour les requêtes HTTP. Au-dessus de la classe GetAnswer, en haut de la fonction `GetAnswer.java`, ajoutez les dépendances nécessaires au projet :

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires

En haut de la classe `GetAnswer.java`, ajoutez les constantes nécessaires pour accéder à QnA Maker. Ces valeurs se trouvent dans la page **Publier** une fois la base de connaissances publiée.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Ajouter une requête POST pour envoyer une question

Le code suivant adresse une requête HTTPS à l’API QnA Maker afin d’envoyer la question à la base de connaissances et reçoit la réponse :

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

La valeur de l’en-tête `Authorization` inclut la chaîne `EndpointKey`.

En savoir plus sur la [requête](../how-to/metadata-generateanswer-usage.md#generateanswer-request) et la [réponse](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Créez et exécutez le projet.

Générez et exécutez le programme à partir de la ligne de commande. Il envoie automatiquement la requête à l’API QnA Maker, puis affiche la réponse dans la fenêtre de console.

1. Générez le fichier :

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Exécutez le fichier :

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
