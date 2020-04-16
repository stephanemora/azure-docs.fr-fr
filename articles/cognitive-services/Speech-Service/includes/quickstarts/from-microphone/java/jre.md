---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: c2b844b3b1aa7a848e2668b3dca5a1cac5b5baaf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400726"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer :

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Créer une ressource Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurer votre environnement de développement et créer un projet vide](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * Veiller à avoir accès à un microphone pour la capture audio

## <a name="source-code"></a>Code source

Pour ajouter une nouvelle classe vide dans votre projet Java, sélectionnez **File (Fichier)**  > **New (Nouvelle)**  > **Classe (Classe)** . Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez **speechsdk.quickstart** dans le champ **Package**, et **Main** dans le champ **Name** (Nom).

![Capture d’écran de la fenêtre de nouvelle classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

Remplacez le contenu du fichier *Main.java* par l’extrait de code suivant :

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explication du code

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Générer et exécuter l’application

Appuyez sur <kbd>F11</kbd> ou sélectionnez **Run** (Exécuter) > **Debug** (Déboguer).
Les 15 secondes suivantes de saisie vocale provenant de votre microphone seront reconnues et enregistrées dans la fenêtre console.

![Capture d’écran de la sortie de la console après une reconnaissance réussie](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

