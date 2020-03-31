---
title: Démarrage rapide de Project Acoustics avec Unity
titlesuffix: Azure Cognitive Services
description: Utilisez des exemples de contenu pour faire des expériences avec des contrôles de conception Project Acoustics dans Unity, et déployez sur Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243016"
---
# <a name="project-acoustics-unity-quickstart"></a>Démarrage rapide de Project Acoustics avec Unity
Utilisez l’exemple de contenu Project Acoustics pour Unity pour faire une expérience avec des contrôles de conception s’appuyant sur la simulation.

Configuration logicielle requise :
* [Unity 2018.2+](https://unity3d.com) pour Windows
* [Package d’exemples de contenu Project Acoustics](https://www.microsoft.com/download/details.aspx?id=57346)

Qu’est-ce qui est inclus dans le package d’exemples ?
* Scène Unity avec géométrie, sources sonores et commandes de jeu
* Plug-in Project Acoustics
* Ressources acoustiques avec baking pour la scène

## <a name="import-the-sample-package"></a>Importer le package d’exemples
Importez le package d’exemples dans un nouveau projet Unity.
1. Dans Unity, accédez à **Assets** > **Import Package** > **Custom Package**.

    ![Options d’importation de package dans Unity](media/import-package.png)  

1. Choisissez **ProjectAcoustics.unitypackage**.

1. Sélectionnez le bouton **Import** pour intégrer le package Unity dans votre projet.  
  
    ![Boîte de dialogue d’importation de package dans Unity](media/import-dialog.png)  

Pour importer le package dans un projet existant, consultez [Intégration d’Unity](unity-integration.md) pour des étapes et des remarques supplémentaires.

>[!NOTE]
>Plusieurs messages d’erreur s’affichent dans le journal de la console une fois l’importation terminée. Passez à l’étape suivante et redémarrez Unity.

## <a name="restart-unity"></a>Redémarrer Unity
La partie bake de la boîte à outils Acoustics nécessite la version du runtime de script .NET 4.*x*. L’importation de package met à jour les paramètres du lecteur Unity. Redémarrez Unity pour que ce paramètre prenne effet. Pour vérifier que le paramètre a pris effet, ouvrez les paramètres **Player** :

![Menu de paramètres du projet Unity](media/player-settings.png)  

![Panneau Unity Player Settings avec .NET 4.x sélectionné](media/net45.png)  

>[!NOTE]
>Cette capture d’écran a été effectuée à partir de Unity 2018.*x*. L’image peut différer dans les versions plus récentes. d’Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Ouvrir la fenêtre de baking de Project Acoustics
Dans Unity, sélectionnez **Acoustics** dans le menu **Window**.

![Éditeur Unity avec l’option Acoustics mise en surbrillance dans le menu Window](media/window-acoustics.png)

Une fenêtre **Acoustics** flottante s’ouvre. C’est là que vous définissez les propriétés de la simulation acoustique.

![Éditeur Unity avec la fenêtre Acoustics ouverte](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Expérimenter avec les contrôles de conception
Ouvrez l’exemple de scène dans le dossier *ProjectAcousticsSample*, puis sélectionnez le bouton Play dans l’éditeur Unity. Utilisez les touches W, A, S et D et la souris pour vous déplacer. Pour comparer l’audio de la scène avec et sans acoustique, appuyez sur la touche R jusqu’à ce que le texte en superposition s’affiche en rouge et indique « Acoustics: Disabled » (Acoustique : désactivé). Pour afficher les raccourcis clavier des autres contrôles, sélectionnez F1. Vous pouvez également cliquer avec le bouton droit pour sélectionner une action, puis cliquer avec le bouton gauche pour effectuer cette action.

Le script *AcousticsAdjust* est attaché aux sources sonores dans l’exemple de scène. Il active les paramètres de conception par source.

![Script AcousticsAdjust Unity](media/acoustics-adjust.png)

Les sections suivantes explorent certains des effets que vous pouvez créer à l’aide des contrôles disponibles. Pour obtenir des informations détaillées sur chaque contrôle, consultez le [tutoriel de conception Project Acoustics dans Unity](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modifier l’atténuation basée sur la distance
Le traitement de signal numérique audio fourni par le plug-in spatialiseur **Project Acoustics** Unity respecte l’atténuation basée sur la distance par source intégrée à l’éditeur Unity. Les contrôles pour l’atténuation basée sur la distance sont dans le composant **Audio Source**, qui se trouve dans le panneau **Inspector** des sources sonores, sous **3D Sound Settings** :

![Panneau des options d’atténuation de la distance dans Unity](media/distance-attenuation.png)

Project Acoustics calcule dans une zone « région de simulation » centrée autour de l’emplacement du joueur. Les ressources acoustiques dans l’exemple de package ont fait l’objet d’un bake avec une taille de région de simulation de 45 mètres autour du joueur. Par conséquent, l’atténuation du son doit être conçue pour atteindre 0 à environ 45 mètres.

### <a name="modify-occlusion-and-transmission"></a>Modifier l’occlusion et la transmission
* Si le multiplicateur **Occlusion** est supérieur à 1 (la valeur par défaut est 1), l’occlusion est exagérée. Pour rendre l’effet d’occlusion plus subtil, affectez-lui une valeur inférieure à 1.

* Pour activer la transmission à travers les murs, éloignez le curseur **Transmission (dB)** du paramètre le plus bas.

### <a name="modify-wetness-for-a-source"></a>Modifier l’humidité pour une source
* Pour modifier la rapidité avec laquelle l’humidité change avec la distance, utilisez la déformation de distance de perception (**Perceptual Distance Warp**). Grâce à la simulation, Project Acoustics calcule les niveaux d’humidité, qui fournissent des repères pour la perception de la distance et varient de manière graduelle en fonction de la distance. L’augmentation de la déformation de distance exagère cet effet en augmentant les niveaux d’humidité liés à la distance. Des valeurs de déformation inférieures à 1 rendent plus subtiles les modifications de réverbération en fonction de la distance.

   Pour ajuster plus précisément cet effet, modifiez le paramètre **Wetness (dB)** .

* Pour augmenter le temps d’atténuation dans l’espace, ajustez **Decay Time Scale**. Si le résultat de la simulation pour une paire particulière source/emplacement de l’auditeur a un temps d’atténuation de 1,5 seconde et que **Decay Time Scale** est défini sur 2, le temps d’atténuation appliqué à la source est de 3 secondes.

## <a name="next-steps"></a>Étapes suivantes
* Lire des informations détaillées sur les [contrôles de conception de Project Acoustics basés sur Unity](unity-workflow.md).
* Explorer davantage les concepts qui sous-tendent le [processus de conception](design-process.md).
* [Créer un compte Azure](create-azure-account.md) pour explorer les processus de pré-bake et de bake.
