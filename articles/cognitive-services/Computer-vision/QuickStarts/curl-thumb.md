---
title: 'Démarrage rapide : Générer une miniature - REST, cURL'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une miniature d’une image en utilisant l’API Vision par ordinateur avec cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d6b55a7f3df7657e2b6b0b23b797d36788658f32
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745165"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Démarrage rapide : Générer une miniature à l’aide de l’API REST Vision par ordinateur et de cURL

Dans ce guide de démarrage rapide, vous générez une miniature à partir d’une image en utilisant l’API REST de Vision par ordinateur. Vous spécifiez la hauteur et la largeur désirées, dont les proportions peuvent différer de l’image d’entrée. L’API Vision par ordinateur utilise le rognage intelligent pour identifier la zone d’intérêt et générer des coordonnées de rognage autour de cette région.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Vous devez avoir [cURL](https://curl.haxx.se/windows).
- Vous devez disposer d’une clé d’abonnement pour la Vision par ordinateur. Vous pouvez obtenir une clé d’essai gratuit auprès de [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Vous pouvez également suivre les instructions mentionnées dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Vision par ordinateur et obtenir votre clé.

## <a name="create-and-run-the-sample-command"></a>Créer et exécuter l’exemple de commande

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez la commande ci-après dans un éditeur de texte.
1. Modifiez la commande comme ci-dessous :
    1. Remplacez la valeur de `<subscriptionKey>` par votre clé d’abonnement.
    1. Remplacez la valeur de `<thumbnailFile>` par le chemin et le nom du fichier dans lequel enregistrer la miniature.
    1. Remplacez la première partie de l’URL de la demande (`westcentralus`) par le texte de votre propre URL de point de terminaison.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Éventuellement, changez l’URL d’image dans le corps de la requête (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) par l’URL d’une autre image à partir de laquelle générer une miniature.
1. Ouvrir une fenêtre d’invite de commandes.
1. Collez la commande à partir de l’éditeur de texte dans la fenêtre d’invite de commandes.
1. Appuyez sur Entrée pour exécuter le programme.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse réussie ajoute l’image miniature dans le fichier spécifié dans `<thumbnailFile>`. Si la requête échoue, la réponse contient un code d’erreur et un message pour vous aider à déterminer la cause du problème. Si la requête semble réussir, mais que la miniature créée n’est pas un fichier image valide, il est possible que votre clé d’abonnement ne soit pas valide.

## <a name="next-steps"></a>Étapes suivantes

Explorez l’API Vision par ordinateur utilisée pour analyser une image, détecter des célébrités et des monuments, créer une miniature et extraire le texte imprimé ou manuscrit. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).

> [!div class="nextstepaction"]
> [Explorer l’API Vision par ordinateur](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
