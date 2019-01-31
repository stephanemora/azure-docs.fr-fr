---
title: Bien démarrer avec Project Acoustics
titlesuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment intégrer le plug-in dans votre projet Unity, effectuer le baking de votre scène et appliquer l’acoustique aux sources sonores.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 0aad231b4a57c2d9927a553a43394681282b5494
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155950"
---
# <a name="getting-started-with-project-acoustics"></a>Bien démarrer avec Project Acoustics
Ce guide de démarrage rapide montre comment intégrer le plug-in dans votre projet Unity, effectuer le baking de votre scène et appliquer l’acoustique aux sources sonores. Pour ce guide de démarrage rapide, vous devez d’abord créer un [compte Azure batch](create-azure-account.md). Ce guide part du principe que vous connaissez Unity.

## <a name="download-the-plugin"></a>Télécharger le plug-in
Inscrivez-vous [ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) pour participer à la préversion Concepteur.

## <a name="supported-platforms-for-quickstart"></a>Plateformes prises en charge pour le guide de démarrage rapide
* [Unity 2018.2+](http://www.unity3d.com)
  * Nécessite la définition de votre projet sur la version du runtime de script **.NET 4.x Équivalent** 
  * Nécessite l’éditeur Unity basé sur Windows

## <a name="import-the-plugin"></a>Importer le plug-in
Importez le package Unity d’acoustique dans votre projet. 
* Dans Unity, accédez à **Assets > Import Package > Custom Package**.

![Importer un package](media/ImportPackage.png)  

* Choisissez **MicrosoftAcoustics.unitypackage**.

Si vous importez le plug-in dans un projet existant, il est possible que votre projet ait déjà un fichier **mcs.rsp** à la racine du projet, qui spécifie les options du compilateur C#. Vous devrez fusionner le contenu de ce fichier avec le fichier mcs.rsp fourni avec le plug-in Project Acoustics.

## <a name="enable-the-plugin"></a>Activer le plug-in
La partie baking de la boîte à outils Acoustics nécessite la version du runtime de script .NET 4.x. L’importation de package met à jour les paramètres du lecteur Unity. Redémarrez Unity pour que ce paramètre prenne effet.

![Paramètres du lecteur](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>Créer un maillage de navigation
Utilisez le [flux de travail Unity](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) ordinaire pour créer un maillage de navigation pour votre projet. Pour plus d’informations sur la façon d’utiliser vos propres maillages de navigation, consultez la [procédure pas à pas dans l’interface utilisateur de baking](bake-ui-walkthrough.md).

## <a name="mark-static-meshes-for-acoustics"></a>Marquer des maillages statiques pour Acoustics
Affichez la fenêtre Acoustics en sélectionnant **Window > Acoustics** dans Unity. Cette fenêtre peut être ancrée en regard de l’inspecteur.

![Ouvrir la fenêtre Acoustics](media/WindowAcoustics.png)

Dans la fenêtre de hiérarchie de Unity, désélectionnez tous les éléments sélectionnés. Sous l’onglet **Object** d’Acoustics, cochez la case « Acoustics Geometry » pour marquer tous les maillages et terrains dans votre scène en tant que géométrie d’acoustique.

Sous l’onglet **Materials**, affectez les matières acoustiques aux matières utilisées dans votre scène. La matière **par défaut** a une absorption équivalente au béton. Pour plus d’informations sur la spécification de vos propres propriétés de matières, consultez la [page sur le processus de conception](design-process.md).

![Onglet Materials](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>Afficher un aperçu des sondes
Sous l’onglet **Probes**, cliquez sur **Calculate**. Ce calcul peut prendre quelques minutes, en fonction de la taille de la scène. Une fois le calcul terminé, vous verrez des sphères flottantes dans la vue de la scène, qui marquent les emplacements de simulation acoustique, appelés « points de sonde ». Si vous vous rapprochez suffisamment d’un objet dans la fenêtre de la scène, vous pouvez également voir la voxelisation de scène. Les voxels verts doivent s’aligner avec les objets que vous avez marqués comme géométrie. Les points de sonde et affichages de voxels peuvent être activés ou désactivés dans le menu Gizmos dans le coin supérieur droit de la vue de la scène.

![Aperçu Gizmos](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>Effectuer le baking de la scène
Sous l’onglet **Bake**, entrez vos informations d’identification Azure et cliquez sur **Bake**. Si vous n’avez pas de compte Azure Batch, consultez [cette procédure pas à pas pour en savoir plus sur notre configuration de compte recommandée](create-azure-account.md).
Une fois le baking terminé, le fichier de données est téléchargé automatiquement vers le répertoire **Assets/AcousticsData** dans votre projet.

## <a name="set-up-audio-runtime-dsp"></a>Configurer le DSP de runtime audio
Nous incorporons le DSP de runtime audio pour l’acoustique dans le framework spatialiseur de Unity et nous l’intégrons à la spatialisation basée sur HRTF. Pour activer le traitement acoustique, basculez vers le spatialiseur **Microsoft Acoustics** en accédant à **Edit > Project Settings > Audio**, puis sélectionnez **Microsoft Acoustics** comme **Spatializer Plugin** pour votre projet. Vérifiez aussi que la taille de mémoire tampon de DSP (**DSP Buffer Size**) est défini sur Best Performance.

![Paramètres du projet](media/ProjectSettings.png)  

![Spatialiseur Project Acoustics](media/ChooseSpatializer.png)

Ouvrez le mélangeur audio (**Window > Audio Mixer**). Vérifiez que vous disposez d’au moins un mélangeur, avec un groupe. Si ce n’est pas le cas, cliquez sur le bouton « + » à droite de **Mixers**. Cliquez avec le bouton droit sur la partie inférieure de la bande de canal dans la section d’effets et ajoutez l’effet **Microsoft Acoustics Mixer**. Notez qu’un seul mélangeur acoustique de projet est pris en charge à la fois.

![Mélangeur audio](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>Configurer la table de recherche acoustique
Faites glisser et déposez la préfabrication **Microsoft Acoustics** du panneau de projet vers votre scène :

![Préfabrication Acoustics](media/AcousticsPrefab.png)

Cliquez sur le Game Object **ProjectAcoustics** et accédez à son panneau Inspecteur. Spécifiez l’emplacement de votre résultat de baking (le fichier .ACE, dans **Assets/AcousticsData**) en le faisant glisser-déposer dans le script Acoustics Manager, ou en cliquant sur le bouton de cercle en regard de la zone de texte.

![Acoustics Manager](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>Appliquer l’acoustique aux sources sonores
Créez une source sonore. Cochez la case en bas du panneau d’inspecteur d’AudioSource indiquant **Spatialize**. Vérifiez que **Spatial Blend** est défini sur la 3D complète.  

![Source sonore](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>Appliquer la conception post-baking
Vous pouvez joindre le script **AcousticsAdjust** à une source sonore de votre scène pour activer des paramètres de conception de source supplémentaires, en cliquant sur **Add Component** et en choisissant **Scripts > Acoustics Adjust** :

![AcousticsAdjust](media/AcousticsAdjust.png)

Il existe également des paramètres sur le **Microsoft Acoustics Mixer**. Pour plus d’informations sur la conception post-baking, consultez [Paramètres de conception](design-process.md).

## <a name="next-steps"></a>Étapes suivantes
* Essayez l’[exemple de scène](sample-walkthrough.md)
* Découvrez l’ensemble complet de [fonctionnalités de baking](bake-ui-walkthrough.md)
* Explorez les [paramètres de conception](design-process.md) plus détaillés

