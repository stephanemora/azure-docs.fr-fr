---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485024"
---
1. Lancez Android Studio, puis choisissez **Start a new Android Studio project** (Démarrer un nouveau projet Android Studio) dans la fenêtre d’accueil.

    ![Capture d’écran de la fenêtre d’accueil d’Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. L’Assistant **Choix de votre projet** apparaît : sélectionnez **Téléphone et tablette** et **Activité vide** dans la zone de sélection des activités. Sélectionnez **Suivant**.

   ![Capture d’écran de l’Assistant Choix de votre projet](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Dans l’écran **Configurer votre projet**, entrez **Quickstart** pour **Nom** et **samples.speech.cognitiveservices.microsoft.com** pour **Nom du package**, puis choisissez un répertoire de projet. Pour **Niveau d’API minimal** choisissez **API 23 : Android 6.0 (Marshmallow)** , laissez toutes les autres cases décochées, puis sélectionnez **Terminer**.

   ![Capture d’écran de l’Assistant Configuration de votre projet](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Quelques minutes sont nécessaires à Android Studio pour préparer votre nouveau projet Android. Configurez ensuite le projet pour découvrir le kit SDK Speech et utiliser Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.6.0`.

Le SDK Speech pour Android est empaqueté au format [AAR (bibliothèque Android)](https://developer.android.com/studio/projects/android-library), qui inclut les bibliothèques nécessaires et les autorisations Android requises.
Il est hébergé dans un référentiel Maven sur https:\//csspeechstorage.blob.core.windows.net/maven/.

Configurez votre projet pour utiliser le kit SDK Speech. Ouvrez la fenêtre de structure du projet en choisissant **File (Fichier)**  > **Project Structure (Structure de projet)** dans la barre de menus Android Studio. Dans la fenêtre de structure du projet, apportez les modifications suivantes :

1. Dans la liste située sur le côté gauche de la fenêtre, sélectionnez **Project** (Projet). Modifiez les paramètres **Default Library Repository** (Référentiel de bibliothèque par défaut) en ajoutant une virgule et l’URL du référentiel Maven, entourée de guillemets simples. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Capture d’écran de la fenêtre de structure d’un projet](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Dans le même écran, sur le côté gauche, sélectionnez **app** (application). Ensuite, sélectionnez l’onglet **Dependencies** (Dépendances) en haut de la fenêtre. Sélectionnez le signe plus vert (+), puis choisissez **Library dependency** (Dépendance de bibliothèque) dans le menu déroulant.

   ![Capture d’écran de la fenêtre de structure d’un projet](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Dans la fenêtre qui s’affiche, entrez le nom et la version du kit SDK Speech pour Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`. Sélectionnez ensuite **OK**.
   Le Kit de développement logiciel (SDK) Speech devrait à présent apparaître dans la liste des dépendances, comme indiqué ci-dessous :

   ![Capture d’écran de la fenêtre de structure d’un projet](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Sélectionnez l’onglet **Properties** (Propriétés). À la fois pour **Source Compatibility** (Compatibilité source) et pour **Target Compatibility** (Compatibilité cible), sélectionnez **1.8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Sélectionnez **OK** pour fermer la fenêtre de structure du projet et appliquer vos modifications au projet.
