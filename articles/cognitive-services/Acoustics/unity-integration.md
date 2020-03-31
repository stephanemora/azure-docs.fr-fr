---
title: Intégration et déploiement de Project Acoustics Unity
titlesuffix: Azure Cognitive Services
description: Cet article explique comment intégrer le plug-in Project Acoustics Unity dans votre projet Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243010"
---
# <a name="project-acoustics-unity-integration"></a>Intégration de Project Acoustics Unity
Cet article explique comment intégrer le plug-in Project Acoustics Unity dans votre projet Unity.

Configuration logicielle requise :
* [Unity 2018.2+](https://unity3d.com) pour Windows
* [Package Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importer le plug-in
1. Importez le package Acoustics Unity dans votre projet. 
 Dans Unity, accédez à **Assets** > **Import Package** > **Custom Package**.

    ![Menu d’importation de package dans Unity](media/import-package.png)  

1. Choisissez **ProjectAcoustics.unitypackage**.

1. Sélectionnez le bouton **Import** pour intégrer le package Unity dans votre projet.

    ![Boîte de dialogue d’importation de package dans Unity](media/import-dialog.png)  

Si vous importez le plug-in dans un projet existant, il est possible que votre projet ait déjà un fichier *mcs.rsp* à la racine du projet. Ce fichier spécifie les options pour le compilateur C#. Fusionnez le contenu de ce fichier avec le fichier mcs.rsp fourni avec le plug-in Project Acoustics.

## <a name="enable-the-plug-in"></a>Activer le plug-in
La partie bake de la boîte à outils Acoustics nécessite la version du runtime de script .NET 4.*x*. L’importation de package met à jour les paramètres du lecteur Unity. Redémarrez Unity pour que ce paramètre prenne effet.

![Panneau de paramètres du lecteur Unity](media/player-settings.png)

![Panneau de paramètres du lecteur Unity avec .NET 4.5 sélectionné](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurer le DSP audio
Project Acoustics inclut un DSP de runtime audio qui s'intègre à l’infrastructure du spatialisateur du moteur audio Unity. Il inclut à la fois une spatialisation basée sur HRTF et une spatialisation panoramique. Pour activer le DSP Project Acoustics, accédez à **Edit** > **Project Settings** > **Audio** pour ouvrir les paramètres audio Unity. Sélectionnez **Project Acoustics** comme **plug-in spatialiseur** pour votre projet. Vérifiez que la **taille de mémoire tampon DSP** a pour valeur *Best performance*.

![Menu de paramètres du projet Unity](media/project-settings.png)  

![Panneau de paramètres du spatialiseur Unity avec le spatialisateur Project Acoustics sélectionné](media/choose-spatializer.png)

Ouvrez ensuite le mélangeur audio (**Window** > **Audio Mixer**). Veillez à avoir au moins un mélangeur, avec un groupe. Si ce n’est pas le cas, sélectionnez le bouton **+** à droite de **Mixers**. Cliquez avec le bouton droit sur la partie inférieure de la bande de canal dans la section d’effets et ajoutez l’effet **Microsoft Acoustics Mixer**. Un seul mélangeur Project Acoustics est pris en charge à la fois.

![Mélangeur audio Unity hébergeant le mélangeur Project Acoustics](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Activer l’acoustique sur les sources sonores
Créez une source audio : Cochez la case **Spatialize** en bas du panneau d’inspecteur AudioSource. Vérifiez que **Spatial Blend** est défini sur la *3D* complète.  

![Panneau de la source audio Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Activer la conception acoustique
Joignez le script *AcousticsAdjust* à une source sonore de votre scène pour activer des paramètres de conception de source supplémentaires : Sélectionnez **Add Component**, puis **Scripts** > **Acoustics Adjust**.

![Script AcousticsAdjust Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Étapes suivantes
* [Effectuer un bake de votre scène avec Project Acoustics pour Unity](unity-baking.md).
* [Créer un compte Azure Batch](create-azure-account.md) pour effectuer le bake de votre scène dans le cloud.
* Explorez le [processus de conception Project Acoustics Unity](unity-workflow.md).
