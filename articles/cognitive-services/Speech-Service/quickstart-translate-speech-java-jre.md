---
title: 'Démarrage rapide : Traduction vocale, Java (Windows, Linux) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez créer une application Java simple pour capturer les paroles de l’utilisateur, les traduire dans une autre langue et générer le texte à la ligne de commande. Ce guide est conçu pour les utilisateurs de Windows et Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 5dc1852a57970c2994d9f36cbd7242a18b580a61
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020993"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Démarrage rapide : Traduire une entrée vocale à l’aide du kit de développement logiciel (SDK) Speech pour Java

Dans ce guide de démarrage rapide, vous allez créer une application Java simple qui capture les paroles de l’utilisateur à partir du microphone de votre ordinateur, les traduit, puis transcrit le texte traduit à la ligne de commande en temps réel. Cette application est conçue pour s’exécuter sur Windows 64 bits ou Linux 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9) ou sur macOS version 10.13 ou ultérieure. Elle est générée avec le package Maven du SDK Speech et l’IDE Eclipse Java.

Pour obtenir la liste complète des langues disponibles pour la traduction vocale, consultez [Prise en charge linguistique](language-support.md).

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

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Créer et configurer un projet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Pour ajouter une nouvelle classe vide dans votre projet Java, sélectionnez **File (Fichier)** > **New (Nouvelle)** > **Classe (Classe)**.

1. Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez **speechsdk.quickstart** dans le champ **Package**, et **Main** dans le champ **Name** (Nom).

   ![Capture d’écran de la fenêtre de nouvelle classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Remplacez tout le code `Main.java` par l’extrait de code suivant :

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Appuyez sur F11 ou sélectionnez **Run (Exécuter)** > **Debug (Déboguer)**.

La saisie vocale provenant de votre microphone sera retranscrit en allemand et enregistrée dans la fenêtre console. Appuyez sur « Entrée » pour arrêter la capture vocale.

![Capture d’écran de la sortie de la console après une reconnaissance réussie](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires, qui montrent notamment comment lire une entrée orale à partir d’un fichier audio et générer un texte traduit en parole de synthèse, sont disponibles sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Démarrage rapide : reconnaître une entrée vocale, Java (Windows, Linux)](quickstart-java-jre.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
