---
title: 'Démarrage rapide : Générer une miniature - REST, cURL'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une miniature d’une image en utilisant l’API Vision par ordinateur avec cURL.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f4f9468a06ca497ba4317edbaada26c878b8e2ca
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212343"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-curl-in-computer-vision"></a>Démarrage rapide : Générer une miniature à l’aide de l’API REST et cURL dans Vision par ordinateur

Dans ce guide de démarrage rapide, vous générez une miniature d’une image en utilisant l’API REST de Vision par ordinateur. Avec la méthode [Obtenir une miniature](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), vous pouvez générer une miniature d’une image. Vous spécifiez la hauteur et la largeur, qui peuvent différer des proportions de l’image d’entrée. L’API Vision par ordinateur utilise le rognage intelligent pour identifier la zone d’intérêt et générer des coordonnées de rognage en fonction de cette région.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Requête d’obtention de miniature

Avec la [méthode Obtenir une miniature](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), vous pouvez générer une miniature d’une image. Vous spécifiez la hauteur et la largeur, qui peuvent différer des proportions de l’image d’entrée. L’API Vision par ordinateur utilise le rognage intelligent pour identifier la zone d’intérêt et générer des coordonnées de rognage en fonction de cette région.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez `<File>` par le chemin d’accès et le nom de fichier pour enregistrer la miniature.
1. Remplacez l’URL de requête (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) par l’emplacement où vous avez obtenu vos clés d’abonnement, si nécessaire.
1. Modifiez éventuellement l’image (`{\"url\":\"...`) à analyser.
1. Ouvrez une fenêtre de commandes sur un ordinateur sur lequel cURL est installé.
1. Collez le code dans la fenêtre et exécutez la commande.

>[!NOTE]
>Dans votre appel REST, vous devez utiliser le même emplacement que celui à partir duquel vous avez obtenu vos clés d’abonnement. Par exemple, si vous avez obtenu vos clés d’abonnement à l’emplacement westus, remplacez « westcentralus » par « westus » dans l’URL ci-après.

## <a name="prerequisites"></a>Prérequis

- Vous devez avoir [cURL](https://curl.haxx.se/windows).
- Vous devez avoir une clé d’abonnement pour Vision par ordinateur. Pour obtenir une clé d’abonnement, consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-command"></a>Créer et exécuter l’exemple de commande

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez la commande ci-après dans un éditeur de texte.
1. Modifiez la commande comme ci-dessous :
    1. Remplacez la valeur de `<subscriptionKey>` par votre clé d’abonnement.
    1. Remplacez la valeur de `<thumbnailFile>` par le chemin et le nom du fichier dans lequel enregistrer la miniature.
    1. Si nécessaire, remplacez l’URL de requête (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail`) par l’URL du point de terminaison de la méthode [Obtenir la miniature](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) dans la région Azure où vous avez obtenu vos clés d’abonnement.
    1. Éventuellement, changez l’URL d’image dans le corps de la requête (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) par l’URL d’une autre image à partir de laquelle générer une miniature.
1. Ouvrir une fenêtre d’invite de commandes.
1. Collez la commande à partir de l’éditeur de texte dans la fenêtre d’invite de commandes, puis exécutez la commande.

```console
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse réussie ajoute l’image miniature dans le fichier spécifié dans `<thumbnailFile>`. Si la requête échoue, la réponse contient un code d’erreur et un message pour vous aider à déterminer la cause du problème.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, fermez la fenêtre d’invite de commandes et l’éditeur de texte.

## <a name="next-steps"></a>Étapes suivantes

Explorez l’API Vision par ordinateur utilisée pour analyser une image, détecter des célébrités et des monuments, créer une miniature et extraire le texte imprimé ou manuscrit. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorer l’API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
