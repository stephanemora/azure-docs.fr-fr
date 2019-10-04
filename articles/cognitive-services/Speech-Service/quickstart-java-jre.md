---
title: 'Démarrage rapide : Reconnaissance vocale, Java (Windows, Linux) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez découvrir comment créer une application Java simple qui capture et transcrit les paroles d’un utilisateur à partir du microphone de votre ordinateur.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 498e41b08133113be9789ef49291b8e2bb0f3705
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554115"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Démarrage rapide : reconnaissance vocale à l’aide du SDK Speech pour Java

Des guides de démarrage rapide sont également disponibles pour la [traduction vocale](quickstart-translate-speech-java-jre.md) et l’[assistant virtuel « voice-first »](quickstart-virtual-assistant-java-jre.md).

Si vous le souhaitez, choisissez un autre langage de programmation et/ou environnement :<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application console Java au moyen du [kit SDK Speech](speech-sdk.md). Vous transcrivez la reconnaissance vocale en temps réel à partir du microphone de votre PC. L’application est générée avec le package Maven du SDK Speech et l’IDE Eclipse Java (v4.8) sur Windows 64 bits, Linux 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9) ou sur macOS version 10.13 ou ultérieure. Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

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

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Appuyez sur F11 ou sélectionnez **Run (Exécuter)**  > **Debug (Déboguer)** .
Les 15 secondes suivantes de saisie vocale provenant de votre microphone seront reconnues et enregistrées dans la fenêtre console.

![Capture d’écran de la sortie de la console après une reconnaissance réussie](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Démarrage rapide : traduire une entrée vocale, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
