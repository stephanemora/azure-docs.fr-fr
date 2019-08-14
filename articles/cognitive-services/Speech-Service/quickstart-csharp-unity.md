---
title: 'Démarrage rapide : Reconnaissance vocale, Unity – Speech Service'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application de reconnaissance vocale avec Unity et le SDK Speech pour Unity (bêta). Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: jhakulin
ms.openlocfilehash: 1b6e60edd86cff2d657b562f05351e20571c0909
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815384"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Démarrage rapide : Reconnaissance vocale avec le SDK Speech pour Unity (bêta).

Des guides de démarrage rapide sont également disponibles pour la [synthèse vocale](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Utilisez ce guide pour créer une application de reconnaissance vocale avec [Unity](https://unity3d.com/) et le SDK Speech pour Unity (bêta).
Quand vous avez terminé, vous pouvez parler dans votre appareil pour une transcription vocale en temps réel.
Si vous débutez avec Unity, nous vous suggérons de lire le [manuel de l’utilisateur Unity](https://docs.unity3d.com/Manual/UnityManual.html) avant de développer votre application.

> [!NOTE]
> Le SDK Speech pour Unity est actuellement en version bêta.
> Il prend en charge Windows Desktop (x86 et x64), ou la plateforme Windows universelle (x86, x64, ARM/ARM64), et Android (x86, ARM32/64).

## <a name="prerequisites"></a>Prérequis

Pour mener à bien ce projet, vous aurez besoin des éléments suivants :

- [Unity version 2018.3 ou ultérieure](https://store.unity.com/) avec [Unity 2019.1 pour la prise en charge d’UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). La version 15.9 (ou une version ultérieure) de Visual Studio 2017 peut également être utilisée.
  - Pour la prise en charge d’ARM64, installez les [outils de génération facultatifs pour ARM64 et le SDK Windows 10 pour ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Clé d’abonnement pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
- Accès au microphone de l’ordinateur.

## <a name="create-a-unity-project"></a>Créer un projet Unity

1. Ouvrez Unity. Si vous utilisez Unity pour la première fois, la fenêtre **Unity Hub** *<version number>* s’affiche. (Vous pouvez également ouvrir Unity Hub directement pour accéder à cette fenêtre.)

   [![Fenêtre Unity Hub](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Sélectionnez **Nouveau**. La fenêtre **Create a new project with Unity** *<version number>* s’affiche.

   [![Créer un projet dans Unity Hub](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Sous **Project Name**, entrez **csharp-unity**.
1. Sous **Templates**, sélectionnez **3D** si ce n’est déjà fait.
1. Sous **Location**, sélectionnez ou créez un dossier dans lequel le projet sera enregistré.
1. Sélectionnez **Créer**.

Après quelques instants, la fenêtre de l’éditeur Unity s’affiche.

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Pour installer le SDK Speech pour Unity, procédez comme suit :

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Téléchargez et ouvrez le [SDK Speech pour Unity (bêta)](https://aka.ms/csspeech/unitypackage), fourni sous la forme d’un package de ressources Unity (.unitypackage). Une fois le package de ressources ouvert, la boîte de dialogue **Import Unity Package** s’affiche.

   [![Boîte de dialogue Import Unity Package de l’éditeur Unity](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Vérifiez que tous les fichiers sont sélectionnés, puis cliquez sur **Import**. Après quelques instants, le package de ressources Unity est importé dans votre projet.

Pour plus d’informations sur l’importation de packages de ressources dans Unity, consultez la [documentation Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Ajouter une interface utilisateur

À présent, nous allons ajouter une interface utilisateur minimale à notre scène. Cette interface utilisateur comprend un bouton pour déclencher la reconnaissance vocale et un champ de texte pour afficher le résultat. La [fenêtre **Hierarchy**](https://docs.unity3d.com/Manual/Hierarchy.html) présente un exemple de scène créée par Unity avec le nouveau projet.

1. En haut de la fenêtre **Hierarchy**, sélectionnez **Create** > **UI** > **Button**.

   Cette action crée trois objets de jeu que vous pouvez voir dans la fenêtre **Hierarchy** : un objet **Button**, un objet **Canvas** contenant le bouton et un objet **EventSystem**.

   [![Environnement de l’éditeur Unity](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Naviguez dans la vue **Scene**](https://docs.unity3d.com/Manual/SceneViewNavigation.html) pour avoir une bonne vue du canevas et du bouton de la [vue **Scene**](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Dans la [fenêtre **Inspector**](https://docs.unity3d.com/Manual/UsingTheInspector.html) (à droite par défaut), définissez les propriétés **Pos X** et **Pos Y** avec la valeur **0** pour centrer le bouton au milieu du canevas.

1. Dans la fenêtre **Hierarchy**, sélectionnez **Create** > **UI** > **Text** pour créer un objet **Text**.

1. Dans la fenêtre **Inspector**, définissez les propriétés **Pos X** et **Pos Y** avec les valeurs **0** et **120**, puis définissez les propriétés **Width** et **Height** avec les valeurs **240** et **120**. Ces valeurs garantissent que le champ de texte et le bouton ne se chevauchent pas.

Quand vous avez terminé, la vue **Scene** doit ressembler à cette capture d’écran :

[![Vue Scene dans l’éditeur Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Ajouter l’exemple de code

Pour ajouter l’exemple de code de script au projet Unity, procédez comme suit :

1. Dans la [fenêtre Project](https://docs.unity3d.com/Manual/ProjectView.html), sélectionnez **Create** > **C# script** pour ajouter un nouveau script C#.

   [![Fenêtre Project dans l’éditeur Unity](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nommez le script `HelloWorld`.

1. Double-cliquez sur `HelloWorld` pour modifier le script nouvellement créé.

   > [!NOTE]
   > Pour configurer l’éditeur de code qui sera utilisé par Unity pour la modification, sélectionnez**Edit** > **Preferences**, puis accédez aux préférences **External Tools**. Pour plus d’informations, consultez le [manuel de l’utilisateur Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Remplacez le script existant par le code suivant :

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Recherchez la chaîne `YourSubscriptionKey` et remplacez-la par votre clé d’abonnement aux services Speech.

1. Recherchez la chaîne `YourServiceRegion` et remplacez-la par la [région](regions.md) associée à votre abonnement. Par exemple, si vous utilisez la version d’évaluation gratuite, la région est `westus`.

1. Enregistrez les changements apportés au script.

À présent, revenez dans l’éditeur Unity et ajoutez le script en tant que composant à l’un de vos objets de jeu :

1. Dans la fenêtre **Hierarchy**, sélectionnez l’objet **Canvas**.

1. Dans la fenêtre **Inspector**, sélectionnez le bouton **Add Component**.

   [![Fenêtre Inspector dans l’éditeur Unity](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Dans la liste déroulante, recherchez le script `HelloWorld` que nous avons créé précédemment, puis ajoutez-le. Une section **Hello World (Script)** s’affiche dans la fenêtre **Inspector**. Elle présente deux propriétés non initialisées : **Output Text** et **Start Reco Button**. Ces propriétés de composant Unity correspondent aux propriétés publiques de la classe `HelloWorld`.

1. Cliquez sur le sélecteur d’objet de la propriété **Start Reco Button** (petite icône en forme de cercle à droite de la propriété), puis choisissez l’objet **Button** que vous avez créé précédemment.

1. Cliquez sur le sélecteur d’objet de la propriété **Output Text**, puis choisissez l’objet **Text** que vous avez créé précédemment.

   > [!NOTE]
   > Le bouton comporte également un objet Text imbriqué. Veillez à ne pas le choisir accidentellement pour la sortie de texte (ou renommez l’un des objets de texte à l’aide du champ **Name** dans la fenêtre **Inspector** pour éviter toute confusion).

## <a name="run-the-application-in-the-unity-editor"></a>Exécuter l’application dans l’éditeur Unity

Vous êtes maintenant prêt à exécuter l’application dans l’éditeur Unity.

1. Dans la barre d’outils de l’éditeur Unity (sous la barre de menus), sélectionnez le bouton **Play** (triangle pointant vers la droite) .

1. Accédez à la [vue **Game**](https://docs.unity3d.com/Manual/GameView.html) et attendez que l’objet **Text** affiche **Click button to recognize speech**. (Il affiche **New Text** si l’application n’a pas démarré ou n’est pas prête à répondre.)

1. Cliquez sur le bouton et prononcez une phrase en anglais dans le microphone de votre ordinateur. Vos paroles sont transmises aux services Speech et transcrites en texte, qui apparaît dans la vue **Game**.

   [![Vue Game dans l’éditeur Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Consultez la [fenêtre **Console**](https://docs.unity3d.com/Manual/Console.html) pour voir les messages de débogage. Si la fenêtre **Console** ne s’affiche pas, accédez à la barre de menus et sélectionnez **Window** > **General** > **Console** pour l’afficher.

1. Une fois la reconnaissance vocale terminée, cliquez sur le bouton **Play** dans la barre d’outils de l’éditeur Unity pour arrêter l’application.

## <a name="additional-options-to-run-this-application"></a>Options supplémentaires pour exécuter cette application

Vous pouvez également déployer cette application en tant qu’application Android, Windows autonome ou UWP.
Pour plus d’informations, consultez le [référentiel d’exemples](https://aka.ms/csspeech/samples). Le dossier `quickstart/csharp-unity` décrit la configuration de ces cibles supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples C# sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Entraîner un modèle pour Custom Speech](how-to-custom-speech-train-model.md)
