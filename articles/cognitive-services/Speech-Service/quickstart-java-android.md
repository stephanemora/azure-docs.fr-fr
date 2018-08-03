---
title: 'Démarrage rapide : reconnaissance vocale dans Java sur Android à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Reconnaissance vocale dans Java sur Android à l’aide du kit de développement logiciel (SDK) Cognitive Services Speech
services: cognitive-services
author: fmegen
manager: wolfma
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: fb52bc3fa398cbe5ba4cbea7c1908ee1fece876e
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114700"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>Démarrage rapide : reconnaissance vocale dans Java sur Android à l’aide du kit de développement logiciel (SDK) Speech

Dans cet article, vous allez apprendre à créer une application Java pour Android à l’aide du Kit de développement logiciel (SDK) Cognitive Services Speech pour transcrire de la parole en texte.
L’application est basée sur le package Maven du kit SDK Microsoft Cognitive Services version 0.5.0 et Android Studio 3.1.

> [!NOTE]
> Pour le Kit de développement logiciel (SDK) Speech et l’appareil Roobo, consultez la page [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement pour le service Speech. Consultez l’article [Try the speech service for free](get-started.md) (Essayer le service Speech gratuitement).
* Un PC (Windows, Linux, Mac) capable d’exécuter Android Studio.
* La version 3.1 d’[Android Studio](https://developer.android.com/studio/).
* Un appareil Android ARM (API 23 : Android Marshmallow 6.0 ou version ultérieure) [activé pour le développement](https://developer.android.com/studio/debug/dev-options) et avec un microphone opérationnel.

## <a name="create-an-android-studio-project"></a>Créer un projet Android Studio

Lancez Android Studio, puis sélectionnez **Démarrer un nouveau projet Android Studio**.

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

Dans l’Assistant **Créer un projet** qui apparaît, choisissez les options suivantes :

1. Sur l’écran **Créer un projet Android**, entrez **Démarrage rapide** comme **nom de l’application**, **samples.speech.cognitiveservices.microsoft.com** comme **domaine d’entreprise**, puis choisissez l’emplacement du projet. Laissez les cases à cocher non sélectionnées, puis cliquez sur **Suivant**.

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. Sur l’écran **Cibler des appareils Android**, sélectionnez l’option **Téléphone et tablette**, choisissez **API 23 : Android 6.0 (Marshmallow)** dans la liste déroulante, puis cliquez sur **Suivant**.

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. Sur l’écran **Ajouter une activité à un mobile**, sélectionnez **Activité vide**, puis cliquez sur **Suivant**.

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. Sur l’écran **Configurer une activité**, utilisez **MainActivity** comme nom de l’activité et **activité\_principale** comme nom de la disposition. Cochez les deux cases, puis cliquez sur **Terminer**.

   ![](media/sdk/qs-java-android-05-configure-activity.png)

Après quelques instants, votre nouveau projet Android Studio devrait s’afficher.

## <a name="configure-your-project-for-the-speech-sdk"></a>Configurer votre projet pour le Kit de développement logiciel (SDK) Speech

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `0.5.0`.

Le Kit de développement logiciel (SDK) Speech pour Android est empaqueté sous forme de package [AAR (bibliothèque Android)](https://developer.android.com/studio/projects/android-library), qui inclut les bibliothèques nécessaires ainsi que les autorisations Android requises pour son utilisation.
Il est hébergé dans un référentiel Maven dans https://csspeechstorage.blob.core.windows.net/maven/.

Nous expliquons ci-dessous comment configurer votre projet pour utiliser le Kit de développement logiciel (SDK) Speech.

Ouvrez la fenêtre de la structure du projet sous **Fichier** \> **Structure de projet**.
Dans la fenêtre qui s’affiche, effectuez les modifications suivantes (cliquez sur **OK** uniquement après avoir terminé toutes les étapes) :

1. Sélectionnez **Projet**, puis modifier les paramètres du **référentiel de bibliothèque par défaut** en ajoutant une virgule et l’URL de notre référentiel Maven, entourée de guillemets simples `'https://csspeechstorage.blob.core.windows.net/maven/'` :

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Toujours sur cet écran, sur le côté gauche, sélectionnez le module **App** puis l’onglet **Dépendances** dans la partie supérieure. Cliquez ensuite sur le signe plus vert dans le coin supérieur droit, puis sélectionnez **Dépendance de bibliothèque**.

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Dans la fenêtre qui s’affiche, entrez le nom et la version de notre Kit de développement logiciel (SDK) Speech pour Android, `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`, puis cliquez sur **OK**.
   Le Kit de développement logiciel (SDK) Speech devrait à présent apparaître dans la liste des dépendances, comme indiqué ci-dessous :

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. Dans la partie supérieure, sélectionnez l’onglet **Propriétés**. Sélectionnez **1.8** à la fois pour **Compatibilité source** et **Compatibilité cible**.

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Pour finir, cliquez sur **OK** pour fermer la fenêtre **Structure de projet** et appliquer toutes les mises à jour.

## <a name="create-a-minimal-ui"></a>Créer une interface utilisateur minimale

Modifiez la disposition de votre activité principale, `activity_main.xml`.
Par défaut, elle doit s’afficher avec une barre de titre contenant le nom de votre l’application et un TextView indiquant « Hello World! ».

* Cliquez sur le TextView. Remplacez son attribut ID dans le coin supérieur droit par `hello`.

* Dans la Palette en haut à gauche de votre fenêtre `activity_main.xml`, faites glisser un bouton dans l’espace vide au-dessus du texte.

* Dans les attributs du bouton à droite, dans la valeur de l’attribut `onClick`, entrez `onSpeechButtonClicked`, qui sera le nom de notre gestionnaire de bouton.
  Remplacez son attribut ID dans le coin supérieur droit par `button`.

* Utilisez l’icône de baguette magique en haut du concepteur si vous souhaitez que le programme calcule les contraintes de disposition pour vous.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Le texte et la version graphique de votre interface utilisateur devraient maintenant ressembler à ceci :

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Modifiez le fichier source `MainActivity.java` et remplacez son code par le suivant (sous votre instruction package) :

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * La méthode `onCreate` inclut du code qui demande une autorisation pour accéder au microphone et à Internet, et établit la liaison de la plateforme native. La configuration des liaisons de la plateforme native est nécessaire une seule fois, autrement dit, elle doit être effectuée au début de l’initialisation de l’application.
   
   * La méthode `onSpeechButtonClicked` était précédemment associée comme gestionnaire du clic de bouton. Une pression sur le bouton déclenche la reconnaissance vocale réelle.

1. Remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. Remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

* Pour créer l’exemple, appuyez sur Ctrl+F9 ou sélectionnez **Build** \> **Créer le projet**.

* Connectez votre appareil Android à votre PC de développement. Vérifiez que le [mode de développement et le débogage USB sont activés](https://developer.android.com/studio/debug/dev-options).

* Pour lancer l’application, appuyez sur MAJ+F10 ou sélectionnez **Exécuter** \> **Exécuter « application »**.

* Dans les fenêtres de la cible de déploiement qui s’affiche, choisissez votre appareil Android.

  ![Lancer l’application en débogage](media/sdk/qs-java-android-12-deploy.png)

* L’application devrait démarrer sur votre appareil.
  Lorsque vous appuyez sur le bouton, les 15 secondes suivantes seront reconnues et affichées dans l’interface utilisateur (vous devriez également voir la réponse dans votre fenêtre logcat dans Android Studio) :

  ![Interface utilisateur après une reconnaissance réussie](media/sdk/qs-java-android-13-gui-on-device.png)

Cette capture d’écran termine le Démarrage rapide Android. L’exemple de code de projet complet peut être téléchargé à partir du référentiel d’exemples.

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez cet exemple dans le dossier `quickstart/java-android`.

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [page d’exemples](samples.md) pour obtenir des exemples supplémentaires.
