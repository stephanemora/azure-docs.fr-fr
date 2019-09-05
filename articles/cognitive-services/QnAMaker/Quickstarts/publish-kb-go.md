---
title: 'Démarrage rapide : Publier une base de connaissances REST, Go – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur REST Go vous aide à publier votre base de connaissances qui envoie (push) la dernière version de la base de connaissances testée à un index de Recherche Azure dédié représentant la base de connaissances publiée. Il crée également un point de terminaison qui peut être appelé dans votre application ou bot conversationnel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: a1e53b21a3168e9745d36e191cc86ba9d20192b5
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308160"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Démarrage rapide : Publier une base de connaissances dans QnA Maker à l’aide de Go

Ce guide de démarrage rapide basé sur REST vous aide à publier votre base de connaissances par programmation. La publication envoie la dernière version de la base de connaissances à un index de Recherche Azure dédié et crée un point de terminaison pouvant être appelé dans votre application ou chatbot.

Ce démarrage rapide fait appel aux API QnA Maker :
* [Publier](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) : cette API ne nécessite aucune information dans le corps de la requête.

## <a name="prerequisites"></a>Prérequis

* [Go 1.10.1](https://golang.org/dl/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord. 

* ID de la base de connaissances QnA Maker se trouvant dans l’URL du paramètre de chaîne de requête kbid, comme indiqué ci-dessous.

    ![ID de base de connaissances QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Si vous n’avez pas encore de base de connaissances, vous pouvez créer un exemple à utiliser pour ce démarrage rapide : [Créer une base de connaissances](create-new-kb-csharp.md).

> [!NOTE] 
> Les fichiers solution complets sont disponibles dans le référentiel GitHub [**Azure-Samples/cognitive-services-qnamaker-go**](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Créer un fichier Go

Ouvrez VSCode et créez un fichier nommé `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `publish-kb.go`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Créer la fonction main

Après les dépendances nécessaires, ajoutez la classe suivante :

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Ajouter les constantes nécessaires

À l’intérieur de la fonction**main**,


 ajoutez les constantes nécessaires pour accéder à QnA Maker. Remplacez les valeurs par les vôtres.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Ajouter une requête POST pour publier la base de connaissances

Après les constantes nécessaires, ajoutez le code suivant, qui adresse une requête HTTPS à l’API QnA Maker afin de publier une base de connaissances et reçoit la réponse :

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

L’appel API renvoie un état 204 si la publication réussit sans aucun contenu dans le corps de la réponse. Le code ajoute du contenu pour les réponses 204.

Toutes les autres réponses sont renvoyées inchangées.

## <a name="build-and-run-the-program"></a>Créez et exécutez le projet.

Exécutez la commande suivante pour compiler le fichier. L’invite de commande ne retourne pas d’informations pour une génération réussie.

```bash
go build publish-kb.go
```

Entrez la commande suivante sur une ligne de commande pour exécuter le programme. Elle envoie la requête à l’API QnA Maker pour publier la base de connaissances, puis affiche 204 en cas de succès ou des erreurs.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

Une fois la base de connaissances publiée, il faut que l’[URL du point de terminaison génère une réponse](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
