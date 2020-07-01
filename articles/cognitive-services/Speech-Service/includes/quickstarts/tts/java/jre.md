---
title: 'Démarrage rapide : Synthétiser la voix, Java (Windows, Linux, macOS) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez découvrir comment créer une application Java simple qui capture et synthétise de la voix à partir de texte et la lit avec le haut-parleur par défaut.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yulili
ms.openlocfilehash: 31bfe77c85723d6c24872f1e52ede60936f7804e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275534"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement et créer un projet vide](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Pour ajouter une nouvelle classe vide dans votre projet Java, sélectionnez **File (Fichier)**  > **New (Nouvelle)**  > **Classe (Classe)** .

1. Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez **speechsdk.quickstart** dans le champ **Package**, et **Main** dans le champ **Name** (Nom).

   ![Capture d’écran de la fenêtre de nouvelle classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Remplacez tout le code `Main.java` par l’extrait de code suivant :

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/text-to-speech/src/speechsdk/quickstart/Main.java#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les changements apportés au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Appuyez sur F11 ou sélectionnez **Run (Exécuter)**  > **Debug (Déboguer)** .
Entrez un texte quand vous y êtes invité ; l’audio synthétisé sera alors lu par le haut-parleur par défaut.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Voir aussi

- [Créer une voix personnalisée Custom Voice](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux personnalisés](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
