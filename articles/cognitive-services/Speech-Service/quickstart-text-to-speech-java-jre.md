---
title: 'Démarrage rapide : Synthèse vocale, Java (Windows, Linux, macOS) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez découvrir comment créer une application Java simple qui capture et synthétise de la voix à partir de texte et la lit avec le haut-parleur par défaut.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: c66e321618b46d52f791f95dab570e3721e806a9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299226"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-java"></a>Démarrage rapide : Synthétiser la voix avec le SDK Speech pour Java

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-java-jre.md), la [traduction vocale](quickstart-translate-speech-java-jre.md) et l’[assistant virtuel « voice-first »](quickstart-virtual-assistant-java-jre.md).

Dans cet article, vous créez une application console Java au moyen du [kit SDK Speech](speech-sdk.md). Vous synthétisez de la voix à partir de texte et vous la lisez avec le haut-parleur par défaut de votre PC. L’application est générée avec le package Maven du SDK Speech et l’IDE Eclipse Java (v4.8) sur Windows 64 bits, Linux 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9) ou sur macOS version 10.13 ou ultérieure. Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Système d’exploitation : Windows 64 bits, Linux 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9) ou macOS version 10.13 ou ultérieure
* [IDE Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

Si vous exécutez Linux, vérifiez que ces dépendances sont installées avant de démarrer Eclipse.

* Sur Ubuntu :

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Sur Debian 9 :

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Si vous exécutez Windows (64 bits), vérifiez que vous avez installé Microsoft Visual C++ Redistributable pour votre plateforme.
* [Télécharger Microsoft Visual C++ Redistributable pour Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Créer et configurer un projet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Pour ajouter une nouvelle classe vide dans votre projet Java, sélectionnez **File (Fichier)**  > **New (Nouvelle)**  > **Classe (Classe)** .

1. Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez **speechsdk.quickstart** dans le champ **Package**, et **Main** dans le champ **Name** (Nom).

   ![Capture d’écran de la fenêtre de nouvelle classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Remplacez tout le code `Main.java` par l’extrait de code suivant :

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Appuyez sur F11 ou sélectionnez **Run (Exécuter)**  > **Debug (Déboguer)** .
Entrez un texte quand vous y êtes invité ; l’audio synthétisé sera alors lu par le haut-parleur par défaut.

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Démarrage rapide : reconnaître une entrée vocale, Java (Windows, Linux, macOS)](quickstart-java-jre.md)
- [Démarrage rapide : Traduire une entrée vocale, Java (Windows, Linux, macOS)](quickstart-translate-speech-java-jre.md)
- [Personnaliser les polices de la voix](how-to-customize-voice-font.md)
- [Enregistrer des échantillons vocaux](record-custom-voice-samples.md)
