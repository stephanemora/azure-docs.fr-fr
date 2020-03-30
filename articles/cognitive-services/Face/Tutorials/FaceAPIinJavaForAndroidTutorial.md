---
title: 'Didacticiel : Détecter et encadrer des visages sur une image à l’aide d’Android SDK'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer une application Android simple qui utilise le service Visage pour détecter et encadrer des visages dans une image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 8d5bef141f83eedaa996bb63c1fb814aeb6af197
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165966"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Didacticiel : créer une application Android pour détecter et encadrer des visages humains dans une image

Dans ce tutoriel, vous allez créer une application Android qui utilise le service Visage Azure via le SDK Java pour détecter des visages humains dans une image. L’application affiche une image sélectionnée et trace un cadre autour de chaque visage détecté.

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> - Créer une application Android
> - Installer la bibliothèque de client du service Visage
> - Utiliser la bibliothèque de client pour détecter les visages dans une image
> - Tracer un cadre autour de chaque visage détecté

![Capture d’écran Android d’une photo avec des visages encadrés par un rectangle rouge](../Images/android_getstarted2.1.PNG)

L’exemple de code complet est disponible dans le dépôt [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) sur GitHub.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. 

## <a name="prerequisites"></a>Conditions préalables requises

- Clé d’abonnement au service Visage. Vous pouvez obtenir une clé d’abonnement d’essai gratuit à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Vous pouvez également suivre les instructions fournies dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service Visage et obtenir votre clé. Ensuite, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la chaîne de point de terminaison de la clé et du service, nommées respectivement `FACE_SUBSCRIPTION_KEY` et `FACE_ENDPOINT`.
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
- [Android Studio](https://developer.android.com/studio/) avec l’API niveau 22 ou ultérieur (conformément aux exigences de la bibliothèque de client Visage).

## <a name="create-the-android-studio-project"></a>Créer le projet Android Studio

Suivez ces étapes pour créer un projet d’application Android.

1. Dans Android Studio, sélectionnez **Start a new Android Studio project** (Commencer un nouveau projet Android Studio).
1. Sur l’écran **Créer un projet Android**, modifiez les champs par défaut, si nécessaire, puis cliquez sur **Suivant**.
1. Sur l’écran **Target Android Devices** (Cibler des appareils Android), utilisez le sélecteur de menu déroulant pour choisir l’**API 22** ou ultérieure, puis cliquez sur **Next** (Suivant).
1. Sélectionnez **Activité vide**, puis cliquez sur **Suivant**.
1. Décochez la case **Compatibilité descendante**, puis cliquez sur **Terminer**.

## <a name="add-the-initial-code"></a>Ajouter le code initial

### <a name="create-the-ui"></a>Créer l’interface utilisateur

Ouvrez *activity_main.xml*. Dans Layout Editor (Éditeur de disposition), sélectionnez l’onglet **Text** (Texte), puis remplacez le contenu par le code suivant.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Créer la classe principale

Ouvrez *MainActivity.java* et remplacez les instructions `import` existantes par le code suivant.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Remplacez ensuite le contenu de la classe **MainActivity** par le code suivant. Ce code crée un gestionnaire d’événements sur le **Button** qui démarre une nouvelle activité pour permettre à l’utilisateur de sélectionner une image. Il affiche l’image dans **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Essayer l’application

Commentez l’appel à **detectAndFrame** dans la méthode **onActivityResult**. Appuyez ensuite sur **Run** (Exécuter) dans le menu pour tester l’application. Quand l’application s’ouvre dans un émulateur ou un appareil connecté, cliquez sur **Browse** (Parcourir) dans la partie inférieure. La boîte de dialogue de sélection de fichier de l’appareil doit apparaître. Choisissez une image et vérifiez qu’elle apparaît dans la fenêtre. Fermez ensuite l’application, puis passez à l’étape suivante.

![Capture d’écran Android d’une photo avec des visages](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Ajouter le SDK Visage

### <a name="add-the-gradle-dependency"></a>Ajouter la dépendance Gradle

Dans le volet **Projet**, utilisez le sélecteur du menu déroulant pour sélectionner **Android**. Développez **Scripts Gradle**, puis ouvrez *build.gradle (Module: app)* . Ajoutez une dépendance de la bibliothèque de client de l’API Visage, `com.microsoft.projectoxford:face:1.4.3`, comme indiqué dans la capture d’écran ci-dessous, puis cliquez sur **Synchroniser maintenant**.

![Capture d’écran Android Studio du fichier d’application build.gradle](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Ajoutez le code de projet relatif à Visage

Revenez à **MainActivity.java** et ajoutez les instructions `import` suivantes :

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Insérez ensuite le code suivant dans la classe **MainActivity**, au-dessus de la méthode **onCreate** :

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

Dans le volet **Projet**, développez **Application**, puis **Manifestes**, et ouvrez le fichier *AndroidManifest.xml*. Insérez l’élément suivant en tant qu’enfant direct de l’élément `manifest` :

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Charger l’image et détecter des visages

Votre application détecte les visages en appelant la méthode **faceClient.Face.DetectWithStreamAsync**, laquelle inclut dans un wrapper l’API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) et retourne une liste d’instances de **Visage**.

Chaque **visage** retourné inclut un rectangle pour indiquer son emplacement, combiné avec une série d’attributs de visage facultatifs. Dans cet exemple, seuls les rectangles de visage sont demandés.

Insérez les deux méthodes suivantes dans la classe **MainActivity**. Une fois la détection de visage terminée, l’application appelle la méthode **drawFaceRectanglesOnBitmap** pour modifier **ImageView**. Vous allez définir cette méthode dans la procédure suivante.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Tracer des rectangles autour des visages

Insérez la méthode d’assistance suivante dans la classe **MainActivity**. Cette méthode trace un rectangle autour de chaque visage détecté à l’aide des coordonnées du rectangle de chaque instance **Visage**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Enfin, décommentez l’appel à la méthode **detectAndFrame** dans **onActivityResult**.

## <a name="run-the-app"></a>Exécuter l’application

Exécutez l’application et recherchez une image contenant un visage. Patientez pendant quelques secondes pour laisser à l’API Visage le temps de répondre. Un rectangle rouge doit apparaître autour de chaque visage de l’image.

![Capture d’écran Android de visages avec des rectangles rouges tracés autour de chaque visage](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez vu l’utilisation de base du SDK Java Visage, et vous avez créé une application pour détecter et encadrer des visages dans une image. Explorez à présent les détails de la détection des visages.

> [!div class="nextstepaction"]
> [Comment détecter des visages dans une image](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
