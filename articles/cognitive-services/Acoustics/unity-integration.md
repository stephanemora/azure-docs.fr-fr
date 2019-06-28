---
title: Intégration et déploiement de Project Acoustics Unity
titlesuffix: Azure Cognitive Services
description: Cette section décrit l'intégration du plug-in Project Acoustics Unity à votre projet Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 8605fe2ea86f875b5c5fd8ea451a287442dcd51b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922488"
---
# <a name="project-acoustics-unity-integration"></a>Intégration de Project Acoustics Unity
Cette section décrit l'intégration du plug-in Project Acoustics Unity à votre projet Unity.

Configuration logicielle requise :
* [Unity 2018.2+](https://unity3d.com) pour Windows
* [Package Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importer le plug-in
Importez le package Unity d’acoustique dans votre projet. 
* Dans Unity, accédez à **Assets > Import Package > Custom Package**.

    ![Capture d’écran du menu Import Package d’Unity](media/import-package.png)  

* Choisissez **ProjectAcoustics.unitypackage**

Si vous importez le plug-in dans un projet existant, il est possible que votre projet ait déjà un fichier **mcs.rsp** à la racine du projet, qui spécifie les options du compilateur C#. Vous devrez fusionner le contenu de ce fichier avec le fichier mcs.rsp fourni avec le plug-in Project Acoustics.

## <a name="enable-the-plugin"></a>Activer le plug-in
La partie baking de la boîte à outils Acoustics nécessite la version du runtime de script .NET 4.x. L’importation de package met à jour les paramètres du lecteur Unity. Redémarrez Unity pour que ce paramètre prenne effet.

![Capture d’écran du panneau de paramètres du lecteur Unity](media/player-settings.png)

![Capture d’écran du panneau de paramètres du lecteur Unity avec .NET 4.5 sélectionné](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurer le DSP audio
Project Acoustics inclut un DSP de runtime audio qui s'intègre à l’infrastructure du spatialisateur du moteur audio Unity. Il inclut à la fois une spatialisation basée sur HRTF et une spatialisation panoramique. Activez le DSP Project Acoustics en ouvrant les paramètres audio Unity dans le menu **Edit > Project Settings > Audio**, puis en sélectionnant **Project Acoustics** comme **Spatializer Plugin** pour votre projet. Vérifiez que la taille de mémoire tampon de DSP (**DSP Buffer Size**) est défini sur Best Performance.

![Capture d’écran du panneau des paramètres d’Unity Project](media/project-settings.png)  

![Capture d'écran du panneau des paramètres du spatialiseur Unity avec le spatialisateur Project Acoustics sélectionné](media/choose-spatializer.png)

Puis ouvrez le mélangeur audio (**Window > Audio Mixer**). Vérifiez que vous disposez d’au moins un mélangeur, avec un groupe. Si ce n’est pas le cas, cliquez sur le bouton « + » à droite de **Mixers**. Cliquez avec le bouton droit sur la partie inférieure de la bande de canal dans la section d’effets et ajoutez l’effet **Project Acoustics Mixer**. Notez qu’un seul mélangeur acoustique de projet est pris en charge à la fois.

![Capture d'écran du mixer audio Unity hébergeant un mixer Project Acoustics](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Activer l’acoustique sur les sources sonores
Créez une source sonore. Cochez la case en bas du panneau d’inspecteur d’AudioSource indiquant **Spatialize**. Vérifiez que **Spatial Blend** est défini sur la 3D complète.  

![Capture d’écran du panneau Source audio Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Activer la conception acoustique
Joignez le script **AcousticsAdjust** à une source sonore de votre scène pour activer des paramètres de conception de source supplémentaires, en cliquant sur **Add Component** et en choisissant **Scripts > Acoustics Adjust** :

![Capture d’écran du script AcousticsAdjust Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Étapes suivantes
* [Effectuer un bake de votre scène avec Project Acoustics pour Unity](unity-baking.md)
* [Créer un compte Azure Batch](create-azure-account.md) pour effectuer le bake de votre scène dans le cloud
* Explorez le [processus de conception Project Acoustics Unity](unity-workflow.md).

