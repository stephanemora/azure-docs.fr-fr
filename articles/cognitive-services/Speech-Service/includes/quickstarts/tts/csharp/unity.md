---
title: 'Démarrage rapide : Synthétiser la voix, C# (Unity) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application de conversion de texte par synthèse vocale avec Unity et le SDK Speech pour Unity. Lorsque vous avez terminé, vous pouvez synthétiser de la voix à partir de texte en temps réel sur les haut-parleurs de votre appareil.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 0934738c557ac6d26867546783797cf5d2b75056
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274878"
---
> [!NOTE]
> Unity prend en charge Windows Desktop (x86 et x64), ou la plateforme Windows universelle (x86, x64, ARM/ARM64), Android (x86, ARM32/64) et iOS (simulateur x64, ARM32 et ARM64).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement et créer un projet vide](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)

## <a name="add-a-ui"></a>Ajouter une interface utilisateur

Nous ajoutons une interface utilisateur minimale à notre scène qui se compose d’un champ d’entrée pour entrer le texte de la synthèse, d’un bouton pour déclencher la synthèse vocale et d’un champ de texte pour afficher le résultat.

* La fenêtre [Hierarchy](https://docs.unity3d.com/Manual/Hierarchy.html) (Hiérarchie) affichée par défaut sur la gauche présente un exemple de scène créée par Unity avec le nouveau projet.
* Sélectionnez le bouton **Create** (Créer) en haut de la fenêtre **Hierarchy** (Hiérarchie), puis sélectionnez **UI (Interface utilisateur)**  > **Input Field** (Champ d’entrée).
* Cette option crée trois objets de jeu que vous pouvez voir dans la fenêtre **Hierarchy** (Hiérarchie) : un objet **Input Field** imbriqué dans un objet **Canvas**, et un objet **EventSystem**.
* [Naviguez dans la vue Scene](https://docs.unity3d.com/Manual/SceneViewNavigation.html) (Scène) afin d’avoir une bonne vue du canevas et du champ d’entrée de la [vue Scene](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Sélectionnez l’objet **Input Field** dans la fenêtre **Hierarchy** (Hiérarchie) pour afficher ses paramètres dans la fenêtre [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspecteur) affichée par défaut à droite.
* Définissez les propriétés **Pos X** et **Pos Y** avec la valeur **0** pour centrer le champ d’entrée au milieu du canevas.
* Resélectionnez le bouton **Create** (Créer) en haut de la fenêtre **Hierarchy** (hiérarchie). Sélectionnez **UI** (Interface utilisateur) > **Button** (Bouton) pour créer un bouton.
* Sélectionnez l’objet **Button** dans la fenêtre **Hierarchy** (Hiérarchie) pour afficher ses paramètres dans la fenêtre [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspecteur) affichée par défaut à droite.
* Définissez les propriétés **Pos X** et **Pos Y** sur **0** et **-48**. Définissez les propriétés **Width** et **Height** sur **160** et **30** pour vous assurer que le bouton et le champ d’entrée ne se chevauchent pas.
* Resélectionnez le bouton **Create** (Créer) en haut de la fenêtre **Hierarchy** (hiérarchie). Sélectionnez **UI** > **Text** pour créer un champ de texte.
* Sélectionnez l’objet **Text** dans la fenêtre **Hierarchy** (Hiérarchie) pour afficher ses paramètres dans la fenêtre [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspecteur) affichée par défaut à droite.
* Définissez les propriétés **Pos X** et **Pos Y** sur **0** et **-80**. Définissez les propriétés **Width** et **Height** sur **320** et **80** pour vous assurer que le champ de texte et le champ d’entrée ne se chevauchent pas.
* Resélectionnez le bouton **Create** (Créer) en haut de la fenêtre **Hierarchy** (hiérarchie). Sélectionnez **Audio** > **Audio Source** pour créer une source audio.

Quand vous avez terminé, l’interface utilisateur doit ressembler à cette capture d’écran :

[![Capture d’écran de l’interface utilisateur du guide de démarrage rapide dans l’éditeur Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Dans la fenêtre [Project](https://docs.unity3d.com/Manual/ProjectView.html) (Projet) affichée par défaut dans le coin inférieur gauche, sélectionnez **Create** (Créer), puis sélectionnez **C# script** (Script C#). Nommez le script `HelloWorld`.

1. Double-cliquez sur le script pour le modifier.

   > [!NOTE]
   > Vous pouvez configurer l’éditeur de code à lancer en sélectionnant **Edit** (Modifier) > **Preferences**. Pour plus d’informations, consultez le [manuel de l’utilisateur Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Remplacez tout le code par le suivant :

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Recherchez et remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement au service Speech.

1. Recherchez et remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement. Par exemple, la région est `westus` si vous utilisez l’évaluation gratuite.

1. Enregistrez les changements apportés au script.

1. De retour dans l’éditeur Unity, ajoutez le script en tant que composant à l’un de vos objets de jeu.

   * Sélectionnez l’objet **Canvas** dans la fenêtre **Hierarchy** (Hiérarchie) pour ouvrir le paramètre dans la fenêtre [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspecteur) affichée par défaut à droite.
   * Sélectionnez le bouton **Add Component** (Ajouter un composant) dans la fenêtre **Inspector**. Recherchez ensuite le script `HelloWorld` que nous avons créé et ajoutez-le.
   * Le composant Hello World a quatre propriétés non initialisées, **Output Text**, **Input Field**, **Speak Button** et **Audio Source**, qui correspondent aux propriétés publiques de la classe `HelloWorld`.
     Pour les initialiser, sélectionnez le sélecteur d’objets (le petit cercle situé à droite de la propriété). Sélectionnez les objets de texte et de bouton que vous avez créés.

     > [!NOTE]
     > Le champ d’entrée et le bouton comportent également un objet Text imbriqué. Veillez à ne pas le sélectionner par inadvertance pour la sortie de texte. Ou bien, vous pouvez renommer les objets de texte qui utilisent le champ **Name** dans la fenêtre **Inspector** pour éviter cette confusion.

## <a name="run-the-application-in-the-unity-editor"></a>Exécuter l’application dans l’éditeur Unity

* Sélectionnez le bouton **Play** (Lire) dans la barre d’outils de l’éditeur Unity sous la barre de menus.
* Une fois que l’application est lancée, entrez du texte dans le champ d’entrée et sélectionnez le bouton. Votre texte est transmis au service Speech et synthétisé en paroles, qui sont lues sur votre haut-parleur.

  [![Capture d’écran du guide de démarrage rapide en cours d’exécution dans la fenêtre Game (Jeu) Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Consultez la [fenêtre Console](https://docs.unity3d.com/Manual/Console.html) pour voir les messages de débogage.

## <a name="additional-options-to-run-this-application"></a>Options supplémentaires pour exécuter cette application

Vous pouvez également déployer cette application sur Android en tant qu’application Windows autonome ou qu’application UWP.
Consultez l’[exemple de dépôt](https://aka.ms/csspeech/samples) dans le dossier quickstart/csharp-unity qui décrit la configuration de ces cibles supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Voir aussi

- [Créer une voix personnalisée Custom Voice](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux personnalisés](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
