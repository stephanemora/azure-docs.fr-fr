---
title: Démarrage rapide de Project Acoustics avec Unity
titlesuffix: Azure Cognitive Services
description: En utilisant des exemples de contenu, faites des expériences avec des contrôles de conception Project Acoustics dans Unity et déployez sur Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 0ea020ca76381a4ae5d6b6e480c94e63f9aa2dab
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933116"
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
Importez le package d’exemples dans un projet nouveau Unity. 
* Dans Unity, accédez à **Assets > Import Package > Custom Package**.

    ![Capture d’écran des options d’importation de package dans Unity](media/import-package.png)  

* Choisissez **ProjectAcoustics.unitypackage**

* Cliquez sur le bouton **Import** pour intégrer le package Unity dans votre projet.  
  
    ![Capture d’écran de la boîte de dialogue Import Package d’Unity](media/import-dialog.png)  

Si vous importez le package dans un projet existant, consultez [Intégration d’Unity](unity-integration.md) pour des étapes et des remarques supplémentaires.

>[!NOTE] 
>Plusieurs erreurs s’affichent dans le journal de la console une fois l’importation terminée.  Passez à l’étape suivante et redémarrez Unity.

## <a name="restart-unity"></a>Redémarrer Unity
La partie baking de la boîte à outils Acoustics nécessite la version du runtime de script .NET 4.x. L’importation de package met à jour les paramètres du lecteur Unity. Redémarrez Unity pour que ce paramètre prenne effet.

Vous pouvez vérifier la prise d’effet de ce paramètre en ouvrant les **Player Settings** :

![Capture d’écran du panneau de paramètres du lecteur Unity](media/player-settings.png)  

![Capture d’écran du panneau de paramètres du lecteur Unity avec .NET 4.5 sélectionné](media/net45.png)  

>[!NOTE]
>La capture d’écran est effectuée à partir d’Unity 2018.x. Les versions plus récentes d’Unity peuvent varier.

## <a name="open-the-project-acoustics-bake-window"></a>Ouvrir la fenêtre de baking de Project Acoustics
Choisissez **Window > Acoustics** dans le menu Unity :

![Capture d’écran de l’éditeur Unity avec l’option de menu Acoustics mise en surbrillance](media/window-acoustics.png)

Une nouvelle fenêtre flottante nommée **Acoustics** s’affiche.  C’est là que les propriétés de la simulation acoustique sont définies.

![Capture d’écran de l’éditeur Unity avec la fenêtre Acoustics ouverte](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-design-controls"></a>Expérimenter avec des contrôles de conception
Ouvrez l’exemple de scène dans le dossier **ProjectAcousticsSample**, puis cliquez sur le bouton Play dans l’éditeur Unity. Utilisez W, A, S, D et la souris pour vous déplacer. Pour comparer l’audio de la scène avec et sans acoustique, appuyez sur le bouton **R** jusqu’à ce que le texte en superposition s’affiche en rouge et indique « Acoustics: Disabled » (Acoustique : désactivé). Pour afficher les raccourcis clavier des autres contrôles, appuyez sur **F1**. Les contrôles sont également utilisables à l’aide de la souris : cliquez avec le bouton droit sur l’action à effectuer pour la sélectionner, puis cliquez avec le bouton gauche pour effectuer l’action.

Le script **AcousticsAdjust** est attaché aux sources sonores dans l’exemple de scène, ce qui permet d’avoir des paramètres de conception par source. 

![Capture d’écran du script AcousticsAdjust Unity](media/acoustics-adjust.png)

Ce qui suit explore certains effets qui peuvent être produits avec les contrôles fournis. Pour obtenir des informations détaillées sur chaque contrôle, consultez le [tutoriel de conception Project Acoustics dans Unity](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modifier l’atténuation basée sur la distance
Le DSP audio fourni par le plug-in spatialiseur **Project Acoustics** Unity respecte l’atténuation basée sur la distance par source intégrée à l’éditeur Unity. Les contrôles pour l’atténuation basée sur la distance sont dans le composant **Audio Source** qui se trouve dans le panneau **Inspector** des sources sonores, sous **3D Sound Settings** :

![Capture d’écran du panneau des options d’atténuation de la distance dans Unity](media/distance-attenuation.png)

Project Acoustics effectue un calcul dans une zone « région de simulation » centrée autour de l’emplacement du joueur. Dans la mesure où les ressources acoustiques dans le package d’exemples ont fait l’objet d’un baking avec une taille de région de simulation de 45m autour du joueur, l’atténuation du son doit être conçue pour tomber à 0 à environ 45m.

### <a name="modify-occlusion-and-transmission"></a>Modifier l’occlusion et la transmission
* Si le multiplicateur **Occlusion** est supérieur à 1 (la valeur par défaut est 1), l’occlusion sera exagérée. Sa configuration à une valeur inférieure à 1 rend l’effet d’occlusion plus subtil.

* Pour activer la transmission à travers les murs, déplacez le curseur **Transmission (dB)** à son niveau le plus bas. 

### <a name="modify-wetness-for-a-source"></a>Modifier l’humidité pour une source
* Pour modifier la rapidité avec laquelle l’humidité change avec la distance, utilisez la **déformation de distance de perception**. **Project Acoustics** calcule les niveaux d’humidité dans l’espace provenant de la simulation, qui varient de façon régulière en fonction de la distance et fournissent des repères pour la perception de la distance. L’augmentation de la déformation de distance exagère cet effet en augmentant les niveaux d’humidité liés à la distance. Des valeurs de déformation inférieures à 1 rendent plus subtiles les modifications de réverbération en fonction de la distance. Cet effet peut également être ajusté de façon plus fine en ajustant les **Wetness (dB)** .

* Augmentez le temps d’atténuation dans l’espace en ajustant **Decay Time Scale**. Si le résultat de la simulation pour une paire particulière source/emplacement de l’auditeur a un temps d’atténuation de 1,5s et que **Decay Time Scale** est défini sur 2, le temps d’atténuation appliqué à la source est de 3s.

## <a name="next-steps"></a>Étapes suivantes
* Lire des informations détaillées complètes sur les [contrôles de conception de Project Acoustics basés sur Unity](unity-workflow.md)
* Explorer plus avant les concepts qui sous-tendent le [processus de conception](design-process.md)
* [Créer un compte Azure](create-azure-account.md) pour explorer les processus de pré-bake et de bake

