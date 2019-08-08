---
title: Démarrage rapide de Project Acoustics avec Unreal
titlesuffix: Azure Cognitive Services
description: En utilisant des exemples de contenu, faites des expériences avec des contrôles de conception Project Acoustics dans Unreal et Wwise, et déployez sur Windows Desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 06023b2758d09fe8ebe7c1301ef1a03d9c54aa41
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704769"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Démarrage rapide de Project Acoustics Unreal/Wwise
Dans ce guide de démarrage rapide, vous expérimentez des contrôles de conception de Project Acoustics en utilisant l’exemple de contenu fourni pour le moteur Unreal et Wwise.

Configuration logicielle requise :
* [Unreal Engine](https://www.unrealengine.com/) 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6

## <a name="download-the-sample-package"></a>Télécharger l’exemple de package
Téléchargez l’[exemple de package Project Acoustics Unreal + Wwise](https://www.microsoft.com/download/details.aspx?id=58090). L’exemple de package contient un projet Unreal Engine, le projet Wwise pour ce projet Unreal et le plug-in Project Acoustics Wwise.

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurer l’exemple de projet Project Acoustics
Pour configurer l’exemple de projet Project Acoustics Unreal/Wwise, vous devez d’abord installer le plug-in Project Acoustics dans Wwise. Ensuite, déployez les fichiers binaires de Wwise sur le projet Unreal, puis ajustez le plug-in Unreal de Wwise pour prendre en charge Project Acoustics.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Installer le plug-in Project Acoustics Wwise
Ouvrez Wwise Launcher puis, dans l’onglet **Plugins** sous **Install New Plugins**, sélectionnez **Add From Directory**. Choisissez le répertoire `AcousticsWwisePlugin\ProjectAcoustics` inclus dans le package que vous avez téléchargé.

![Capture d’écran de Wwise Launcher montrant l’option d’installation du plug-in Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Ajouter les fichiers binaires de Wwise à l’exemple de projet Project Acoustics Unreal
Depuis Wwise Launcher, cliquez sur l’onglet **Unreal Engine**, puis cliquez sur le menu hamburger en regard de **Recent Unreal Engine Projects** et sélectionnez **Browse for project**. Ouvrez le fichier `.uproject` de l’exemple de projet Unreal dans le package `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Capture d’écran de l’onglet Unreal dans Wwise Launcher](media/wwise-unreal-tab.png)

Ensuite, en regard de l’exemple de projet Project Acoustics, cliquez sur **Integrate Wwise in Project**.

![Capture d’écran de Wwise Launcher montrant le projet Unreal Acoustics Game](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Étendre les fonctionnalités du plug-in Unreal de Wwise
Le plug-in Project Acoustics Unreal nécessite l’exposition d’un comportement supplémentaire à partir de l’API du plug-in Unreal Wwise. Exécutez le fichier de commandes par lots fourni avec le plug-in Project Acoustics Unreal pour automatiser ces modifications :
* Dans `AcousticsGame\Plugins\ProjectAcoustics\Resources`, exécutez `PatchWwise.bat`.

    ![Capture d’écran de l’Explorateur Windows montrant le script permettant de corriger le projet Wwise](media/patch-wwise-script.png)

* Si le SDK DirectX n’est pas installé, selon la version de Wwise que vous utilisez, vous devrez peut-être ajouter un commentaire sur la ligne qui contient `DXSDK_DIR` dans `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` :

    ![Capture d’écran de l’éditeur de code montrant DXSDK avec des commentaires](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Ouvrez le projet Unreal. 
Il vous est demandé de regénérer les modules : cliquez sur Yes.

>Si l’ouverture du projet échoue sur des échecs de génération, vérifiez que vous avez installé le plug-in Project Acoustics Wwise pour la même version de Wwise que celle utilisée dans l’exemple de projet Project Acoustics.

>Si vous n’utilisez pas [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) version 2018.1.6, vous devrez regénérer les banques de sons avant la lecture de l’audio dans l’exemple de projet.

## <a name="experiment-with-project-acoustics-design-controls"></a>Faire des essais avec les contrôles de conception de Project Acoustics
Écoutez le son de la scène en cliquant sur le bouton Play dans l’éditeur Unreal. Sur le poste de travail, utilisez W, A, S, D et la souris pour vous déplacer. Pour afficher les raccourcis clavier des autres contrôles, appuyez sur **F1**. Voici quelques activités de conception à essayer :

### <a name="modify-occlusion-and-transmission"></a>Modifier l’occlusion et la transmission
Il existe des contrôles de conception Project Acoustics par source sur chaque acteur de son Unreal :

![Capture d’écran des contrôles de conception Unreal Editor Acoustics](media/demo-scene-sound-source-design-controls.png)

Si le multiplicateur **Occlusion** est supérieur à 1 (la valeur par défaut est 1), l’occlusion sera exagérée. Sa configuration à une valeur inférieure à 1 rend l’effet d’occlusion plus subtil.

Pour activer la transmission à travers les murs, déplacez le curseur **Transmission (dB)** à son niveau le plus bas. 

### <a name="modify-wetness-for-a-source"></a>Modifier l’humidité pour une source
Pour modifier la rapidité avec laquelle l’humidité change avec la distance, utilisez la **déformation de distance de perception**. Project Acoustics calcule les niveaux d’humidité dans l’espace provenant de la simulation, qui varient de façon régulière en fonction de la distance et fournissent des repères pour la perception de la distance. L’augmentation de la déformation de distance exagère cet effet en augmentant les niveaux d’humidité liés à la distance. Des valeurs de déformation inférieures à 1 rendent plus subtiles les modifications de réverbération en fonction de la distance. Cet effet peut également être ajusté de façon plus fine en ajustant les **Wetness (dB)** .

Augmentez le temps d’atténuation dans l’espace en ajustant **Decay Time Scale**. Prenons le cas où le résultat de la simulation est un temps d’atténuation de 1,5s. La définition de **Decay Time Scale** sur 2 entraîne un temps d’atténuation appliqué à la source de 3s.

### <a name="modify-distance-based-attenuation"></a>Modifier l’atténuation basée sur la distance
Le plug-in Mixer Project Acoustics Wwise respecte l’atténuation par source basée sur la distance intégrée dans Wwise. La modification de cette courbe change le niveau le niveau du chemin de sécheresse. Le plug-in Project Acoustics ajuste le niveau d’humidité de façon à conserver la combinaison humide-sec spécifiée par la simulation et les contrôles de conception.

![Capture d’écran du panneau des courbes d’atténuation courbe de Wwise avec l’atténuation qui atteint zéro avant la limite de simulation](media/demo-sounds-attenuation.png)

Project Acoustics effectue un calcul dans une zone « région de simulation » centrée autour de chaque emplacement d’un auditeur simulé. Les ressources acoustiques dans l’exemple de package ont fait l’objet d’un baking avec un rayon de région de simulation de 45m, et les atténuations ont été conçues pour tomber à 0 avant 45m. Bien que cette diminution ne soit pas strictement nécessaire, elle présente l’inconvénient que seule la géométrie dans les 45m de l’auditeur va bloquer les sons.

## <a name="next-steps"></a>Étapes suivantes
* [Intégrer le plug-in Project Acoustics](unreal-integration.md) dans votre projet Unreal
* [Créer un compte Azure](create-azure-account.md) pour vos propres bakes


