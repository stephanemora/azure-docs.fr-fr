---
title: 'Démarrage rapide : Reconnaissance vocale, Unity - Services Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application de reconnaissance vocale avec Unity et le SDK Speech pour Unity (bêta). Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: dd8e760e658715b89bf45718ac571ccaeb5ade96
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465562"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Démarrage rapide : Reconnaissance vocale avec le SDK Speech pour Unity (bêta).

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Utilisez ce guide pour créer une application de reconnaissance vocale avec [Unity](https://unity3d.com/) et le SDK Speech pour Unity (bêta).
Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.
Si vous ne connaissez pas Unity, nous vous recommandons d’étudier le [manuel de l’utilisateur Unity](https://docs.unity3d.com/Manual/UnityManual.html) avant de commencer le développement de votre application.

> [!NOTE]
> Le SDK Speech pour Unity est actuellement en version bêta.
> Il prend en charge Windows Desktop (x86 et x64), ou la plateforme Windows universelle (x86, x64, ARM/ARM64), et Android (x86, ARM32/64).

## <a name="prerequisites"></a>Prérequis

Pour mener à bien ce projet, vous aurez besoin des éléments suivants :

* [Unity version 2018.3 ou ultérieure](https://store.unity.com/) avec [Unity 2019.1 pour la prise en charge d’UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Pour la prise en charge d’ARM64, installez les [outils de génération facultatifs pour ARM64 et le SDK Windows 10 pour ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/). 
* Clé d’abonnement pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Accès au microphone de l’ordinateur.

## <a name="create-a-unity-project"></a>Créer un projet Unity

* Démarrez Unity et, sous l’onglet **Projects** (Projets), sélectionnez **New** (Nouveau).
* Indiquez **csharp-unity** sous **Project name** (Nom du projet), **3D** sous **Template** (Modèle), puis choisissez un emplacement.
  Sélectionnez ensuite **Create project** (Créer le projet).
* Après quelques instants, la fenêtre de l’éditeur Unity doit s’afficher.

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Le SDK Speech pour Unity (bêta) est fourni sous la forme d’un package de composants Unity (.unitypackage).
  Téléchargez-la [ici](https://aka.ms/csspeech/unitypackage).
* Importez le SDK Speech. Pour cela, sélectionnez **Assets** (Composants) > **Import Package** (Importer un package) > **Custom Package** (Package personnalisé).
  Pour plus de détails, consultez la [documentation Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* Dans le sélecteur de fichiers, sélectionnez le fichier .unitypackage du SDK Speech téléchargé ci-dessus.
* Vérifiez que tous les fichiers sont sélectionnés, puis cliquez sur le bouton **Import** (Importer).

  ![Capture d’écran de l’éditeur Unity lors de l’importation du SDK Speech sous la forme d’un package de composants Unity](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Ajouter une interface utilisateur

Ajoutons une interface utilisateur minimale à notre scène : un bouton pour déclencher la reconnaissance vocale et un champ de texte pour afficher le résultat.

* La fenêtre [Hierarchy](https://docs.unity3d.com/Manual/Hierarchy.html) (Hiérarchie) affichée par défaut sur la gauche présente un exemple de scène créée par Unity avec le nouveau projet.
* Cliquez sur le bouton **Create** (Créer) en haut de la fenêtre de hiérarchie, puis sélectionnez **UI** (Interface utilisateur) > **Button** (Bouton).
* Cette opération crée trois objets de jeu que vous pouvez voir dans la fenêtre de hiérarchie : un objet **Button** imbriqué dans un objet **Canvas**, et un objet **EventSystem**.
* [Naviguez dans la vue Scene](https://docs.unity3d.com/Manual/SceneViewNavigation.html) (Scène) pour avoir une bonne vue du canevas et du bouton de la [vue Scene](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Cliquez sur l’objet **Button** dans la fenêtre de hiérarchie pour afficher ses paramètres dans la fenêtre [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspecteur) affichée par défaut à droite.
* Définissez les propriétés **Pos X** et **Pos Y** avec la valeur **0** pour centrer le bouton au milieu du canevas.
* Cliquez à nouveau sur le bouton **Create** (Créer) en haut de la fenêtre de hiérarchie, puis sélectionnez **UI** (Interface utilisateur) > **Text** (Texte) pour créer un champ de texte.
* Cliquez sur l’objet **Text** dans la fenêtre de hiérarchie pour afficher ses paramètres dans la fenêtre [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspecteur) affichée par défaut à droite.
* Définissez les propriétés **Pos X** et **Pos Y** avec les valeurs **0** et **120**, puis définissez les propriétés **Width** (Largeur) et **Height** (Hauteur) avec les valeurs **240** et **120** pour vérifier que le champ de texte et le bouton ne se chevauchent pas.

Quand vous avez terminé, l’interface utilisateur doit ressembler à cette capture d’écran :

[![Capture d’écran de l’interface utilisateur du guide de démarrage rapide dans l’éditeur Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

1. Dans la fenêtre [Project](https://docs.unity3d.com/Manual/ProjectView.html) (Projet) affichée par défaut dans le coin inférieur gauche, cliquez sur **Create** (Créer), puis sélectionnez **C# script** (Script C#). Nommez le script `HelloWorld`.

1. Double-cliquez sur le script pour le modifier.

   > [!NOTE]
   > Vous pouvez configurer l’éditeur de code qui sera lancé sous **Edit** (Edition) > **Preferences** (Préférences). Pour plus d’informations, consultez le [manuel de l’utilisateur Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Remplacez tout le code par le suivant :

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Recherchez et remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement Speech Services.

1. Recherchez et remplacez la chaîne `YourServiceRegion` par la [région](regions.md) associée à votre abonnement. Par exemple, si vous utilisez la version d’évaluation gratuite, la région est `westus`.

1. Enregistrez les changements apportés au script.

1. Revenez dans l’éditeur Unity pour ajouter le script en tant que composant à l’un de vos objets de jeu.

   * Cliquez sur l’objet **Canvas** dans la fenêtre de hiérarchie. Cette opération ouvre le paramètre dans la fenêtre [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspecteur) affichée par défaut à droite.
   * Cliquez sur le bouton**Add Component** (Ajouter un composant) dans la fenêtre Inspector, puis recherchez le script HelloWorld créé précédemment et ajoutez-le.
   * Notez que le composant Hello World a deux propriétés non initialisées, **Output Text** et **Start Reco Button**, qui correspondent aux propriétés publiques de la classe `HelloWorld`.
     Pour les associer, cliquez sur le sélecteur d’objets (icône en forme de petit cercle à droite de la propriété), puis choisissez les objets Text et Button que vous avez créés précédemment.

     > [!NOTE]
     > Le bouton comporte également un objet Text imbriqué. Veillez à ne pas le choisir accidentellement pour la sortie de texte (ou renommez un des objets Text à l’aide du champ Name dans la fenêtre Inspector pour éviter toute confusion).

## <a name="run-the-application-in-the-unity-editor"></a>Exécuter l’application dans l’éditeur Unity

* Appuyez sur le bouton **Play** (Lire) dans la barre d’outils de l’éditeur Unity sous la barre de menus.

* Une fois l’application lancée, cliquez sur le bouton et prononcez une phrase en anglais dans le microphone de votre ordinateur. Votre production orale est transmise aux services vocaux, et transcrite en texte qui apparaît dans la fenêtre.

  [![Capture d’écran du guide de démarrage rapide en cours d’exécution dans la fenêtre Game (Jeu) Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Examinez la [fenêtre de console](https://docs.unity3d.com/Manual/Console.html) à la recherche de messages de débogage.

* Une fois la reconnaissance vocale terminée, cliquez sur le bouton **Play** (Lire) dans la barre d’outils de l’éditeur Unity pour arrêter l’application.

## <a name="additional-options-to-run-this-application"></a>Options supplémentaires pour exécuter cette application

Vous pouvez également déployer cette application sur Android, en tant qu’application Windows autonome ou application UWP.
Reportez-vous à notre [exemple de dépôt](https://aka.ms/csspeech/samples) dans le dossier quickstart/csharp-unity qui décrit la configuration de ces cibles supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
