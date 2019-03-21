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
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7d8edd7b092ee1ed4f953d753b2bbe864e075378
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137729"
---
# <a name="project-acoustics-unity-integration"></a>Intégration de Project acoustique Unity
Cette procédure explique l’intégration du plug-in de projet acoustique Unity à votre projet Unity.

Configuration logicielle requise :
* [Unity 2018.2 +](http://unity3d.com) pour Windows
* [Package de projet Unity d’acoustique](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importer le plug-in
Importez le package Unity d’acoustique dans votre projet. 
* Dans Unity, accédez à **Assets > Import Package > Custom Package**.

    ![Importer un package](media/import-package.png)  

* Choisissez **ProjectAcoustics.unitypackage**

Si vous importez le plug-in dans un projet existant, il est possible que votre projet ait déjà un fichier **mcs.rsp** à la racine du projet, qui spécifie les options du compilateur C#. Vous devrez fusionner le contenu de ce fichier avec le fichier mcs.rsp fourni avec le plug-in Project Acoustics.

## <a name="enable-the-plugin"></a>Activer le plug-in
La partie baking de la boîte à outils Acoustics nécessite la version du runtime de script .NET 4.x. L’importation de package met à jour les paramètres du lecteur Unity. Redémarrez Unity pour que ce paramètre prenne effet.

![Paramètres du lecteur](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurer audio DSP
Projet acoustique inclut runtime audio DSP intègre le moteur audio spatial d’Unity. Il inclut les HRTF et panoramique spatialisation. Activer DSP acoustique de projet en ouvrant les paramètres audio de Unity à l’aide de **Modifier > Paramètres du projet > Audio**, puis en sélectionnant **projet acoustique** comme le **plug-in spatial** pour votre projet. Assurez-vous que le **taille de mémoire tampon DSP** est défini sur les meilleures performances.

![Paramètres du projet](media/project-settings.png)  

![Spatialiseur Project Acoustics](media/choose-spatializer.png)

Ouvrez ensuite le Mixer Audio (**fenêtre > Audio Mixer**). Vérifiez que vous disposez d’au moins un mélangeur, avec un groupe. Si ce n’est pas le cas, cliquez sur le bouton « + » à droite de **Mixers**. Avec le bouton droit de la partie inférieure de la bande de canal dans la section Effets et ajoutez le **projet acoustique Mixer** effet. Notez qu’un seul mélangeur acoustique de projet est pris en charge à la fois.

![Mélangeur audio](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Activer acoustique sur sources audio
Créez une source sonore. Cochez la case en bas du panneau d’inspecteur d’AudioSource indiquant **Spatialize**. Vérifiez que **Spatial Blend** est défini sur la 3D complète.  

![Source sonore](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Activer la conception acoustique
Attacher le script **AcousticsAdjust** à une source audio dans votre scène pour activer les paramètres de création de sources supplémentaires, en cliquant sur **ajouter un composant** et en choisissant **Scripts > Ajuster acoustique** :

![AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Étapes suivantes
* [Intégrer votre scène avec acoustique de projet pour Unity](unity-baking.md)
* [Créer un compte Azure Batch](create-azure-account.md) pour intégrer votre scène dans le cloud
* Explorez le [les processus de conception de projet acoustique Unity](unity-workflow.md).

