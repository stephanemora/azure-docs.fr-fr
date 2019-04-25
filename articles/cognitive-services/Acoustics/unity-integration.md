---
title: Intégration de Unity acoustique et le déploiement de projet
titlesuffix: Azure Cognitive Services
description: Cette procédure explique l’intégration du plug-in de projet acoustique Unity à votre projet Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: aa7c5c513d65310bf9bffab29c1d18e7e7a85b49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60389490"
---
# <a name="project-acoustics-unity-integration"></a>Intégration de Project acoustique Unity
Cette procédure explique l’intégration du plug-in de projet acoustique Unity à votre projet Unity.

Configuration logicielle requise :
* [Unity 2018.2+](http://unity3d.com) pour Windows
* [Package de projet Unity d’acoustique](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importer le plug-in
Importez le package Unity d’acoustique dans votre projet. 
* Dans Unity, accédez à **Assets > Import Package > Custom Package**.

    ![Menu de la capture d’écran de Unity importer un Package](media/import-package.png)  

* Choisissez **ProjectAcoustics.unitypackage**

Si vous importez le plug-in dans un projet existant, il est possible que votre projet ait déjà un fichier **mcs.rsp** à la racine du projet, qui spécifie les options du compilateur C#. Vous devrez fusionner le contenu de ce fichier avec le fichier mcs.rsp fourni avec le plug-in Project Acoustics.

## <a name="enable-the-plugin"></a>Activer le plug-in
La partie baking de la boîte à outils Acoustics nécessite la version du runtime de script .NET 4.x. L’importation de package met à jour les paramètres du lecteur Unity. Redémarrez Unity pour que ce paramètre prenne effet.

![Capture d’écran du panneau de paramètres du lecteur Unity](media/player-settings.png)

![Capture d’écran du panneau de paramètres du lecteur Unity avec .NET 4.5 sélectionné](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurer audio DSP
Projet acoustique inclut runtime audio DSP intègre le moteur audio spatial d’Unity. Il inclut les HRTF et panoramique spatialisation. Activer DSP acoustique de projet en ouvrant les paramètres audio de Unity à l’aide de **Modifier > Paramètres du projet > Audio**, puis en sélectionnant **projet acoustique** comme le **plug-in spatial** pour votre projet. Assurez-vous que le **taille de mémoire tampon DSP** est défini sur les meilleures performances.

![Panneau de la capture d’écran des paramètres du projet Unity](media/project-settings.png)  

![Panneau des paramètres de capture d’écran de Unity spatial avec spatial acoustique du projet sélectionné](media/choose-spatializer.png)

Ouvrez ensuite le Mixer Audio (**fenêtre > Audio Mixer**). Vérifiez que vous disposez d’au moins un mélangeur, avec un groupe. Si ce n’est pas le cas, cliquez sur le bouton « + » à droite de **Mixers**. Avec le bouton droit de la partie inférieure de la bande de canal dans la section Effets et ajoutez le **projet acoustique Mixer** effet. Notez qu’un seul mélangeur acoustique de projet est pris en charge à la fois.

![Capture d’écran de Mixer de Audio Unity mixer de projet acoustiques d’hébergement](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Activer acoustique sur sources audio
Créez une source sonore. Cochez la case en bas du panneau d’inspecteur d’AudioSource indiquant **Spatialize**. Vérifiez que **Spatial Blend** est défini sur la 3D complète.  

![Panneau de la capture d’écran de Unity Audio Source](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Activer la conception acoustique
Attacher le script **AcousticsAdjust** à une source audio dans votre scène pour activer les paramètres de création de sources supplémentaires, en cliquant sur **ajouter un composant** et en choisissant **Scripts > Ajuster acoustique** :

![Capture d’écran du script AcousticsAdjust Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Étapes suivantes
* [Intégrer votre scène avec acoustique de projet pour Unity](unity-baking.md)
* [Créer un compte Azure Batch](create-azure-account.md) pour intégrer votre scène dans le cloud
* Explorez le [les processus de conception de projet acoustique Unity](unity-workflow.md).

