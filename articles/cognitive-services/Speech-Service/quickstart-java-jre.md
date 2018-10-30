---
title: 'Démarrage rapide : Reconnaissance vocale dans Java (Windows ou Linux)'
titleSuffix: Azure Cognitive Services
description: Découvrir comment fonctionne la reconnaissance vocale dans Java (Windows ou Linux)
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: fmegen
ms.openlocfilehash: 80ddef79392acb677555ed795bf429f5ec0266a0
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467257"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-service-sdk"></a>Démarrage rapide : Reconnaissance vocale dans Java sur Windows ou Linux avec le kit SDK du service Speech

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dans cet article, vous créez une application console Java au moyen du [kit SDK du service Speech](speech-sdk.md). Vous transcrivez la reconnaissance vocale en temps réel à partir du microphone de votre PC. L’application est créée avec le package Maven du kit SDK Speech, et l’IDE Eclipse Java (v4.8) sur Windows 64 bits ou Ubuntu Linux 16.04. Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement au service Speech pour suivre ce guide de démarrage rapide. Vous pouvez en obtenir une gratuitement. Consultez [Essayer le service Speech gratuitement](get-started.md) pour plus d’informations.


## <a name="create-and-configure-project"></a>Créer et configurer un projet

Si vous utilisez Ubuntu 16.04, avant de démarrer Eclipse, exécutez les commandes suivantes pour vous assurer que les packages nécessaires sont installés.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Démarrez Eclipse.

1. Dans le champ **Workspace** (Espace de travail) de l’utilitaire Eclipse Launcher, entrez le nom d’un nouveau répertoire d’espace de travail. Sélectionnez ensuite **Launch** (Lancer).

   ![Capture d’écran d’Eclipse Launcher](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. La fenêtre principale de l’IDE Eclipse apparaît au bout d’un instant. Fermez l’écran d’accueil s’il en existe un.

1. Dans la barre de menus Eclipse, créez un projet en choisissant **File (Fichier)** > **New (Nouveau)** > **Project (Projet)**.

1. La boîte de dialogue **Nouveau projet** apparaît. Sélectionnez **Java Project** (Projet Java), puis **Next** (Suivant).

   ![Capture d’écran de la boîte de dialogue du nouveau projet, avec le projet Java sélectionné](media/sdk/qs-java-jre-02-select-wizard.png)

1. L’Assistant New Java Project (Nouveau projet Java) démarre. Dans le champ **Project name** (Nom de projet), entrez **quickstart** (démarrage rapide) et choisissez **JavaSE-1.8** en tant qu’environnement d’exécution. Sélectionnez **Terminer**.

   ![Capture d’écran de l’Assistant New Java Project (Nouveau projet Java)](media/sdk/qs-java-jre-03-create-java-project.png)

1. Si une fenêtre **Open Associated Perspective?** (Ouvrir la perspective associée ?) s’affiche, sélectionnez **Open Perspective** (Ouvrir la perspective).

1. Dans **Package explorer** (l’Explorateur de package), cliquez avec le bouton droit sur le projet **quickstart** (démarrage rapide). Choisissez **Configure (Configurer)** > **Convert to Maven Project (Convertir en projet Maven)** dans le menu contextuel.

   ![Capture d’écran de l’explorateur de package](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. La fenêtre **Create new POM** (Créer un POM) s’affiche. Dans le champ **Group Id** (ID de groupe), entrez **com.microsoft.cognitiveservices.speech.samples**, et dans le champ **Artifact Id** (Id d’artefact), indiquez **quickstart** (démarrage rapide). Sélectionnez ensuite **Terminer**.

   ![Capture d’écran de la fenêtre de création d’un POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Ouvrez le fichier **pom.xml** et modifiez-le.

   * À la fin du fichier, avant la balise de fermeture `</project>`, créez un élément `repositories` avec une référence au référentiel Maven pour le Kit de développement logiciel (SDK) Speech, comme indiqué ici :

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Ajoutez également un élément `dependencies` avec le Kit de développement logiciel (SDK) Speech version 1.0.1 en tant que dépendance :

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Enregistrez les modifications.

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Pour ajouter une nouvelle classe vide dans votre projet Java, sélectionnez **File (Fichier)** > **New (Nouvelle)** > **Classe (Classe)**.

1. Dans la fenêtre **New Java Class** (Nouvelle classe Java), entrez **speechsdk.quickstart** dans le champ **Package**, et **Main** dans le champ **Name** (Nom).

   ![Capture d’écran de la fenêtre de nouvelle classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Remplacez tout le code `Main.java` par l’extrait de code suivant :

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Appuyez sur F11 ou sélectionnez **Run (Exécuter)** > **Debug (Déboguer)**.
Les 15 secondes suivantes de saisie vocale provenant de votre microphone seront reconnues et enregistrées dans la fenêtre console.

![Capture d’écran de la sortie de la console après une reconnaissance réussie](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/java-jre`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Effectuer une reconnaissance des intentions vocales à l’aide du kit SDK Speech pour Java](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Voir aussi

- [Traduction vocale](how-to-translate-speech-csharp.md)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
