---
title: Démarrage rapide de Project Acoustics avec Unreal
titlesuffix: Azure Cognitive Services
description: Utilisez des exemples de contenu pour faire des expériences avec des contrôles de conception Project Acoustics dans Unreal et Wwise, et déployez sur Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242917"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Démarrage rapide de Project Acoustics Unreal/Wwise
Dans ce guide de démarrage rapide, vous allez expérimenter des contrôles de conception de Project Acoustics en utilisant des exemples de contenu pour le moteur Unreal et Wwise.

Configuration logicielle requise pour utiliser les exemples de contenu :
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Télécharger l’exemple de package
Téléchargez l’[exemple de package Project Acoustics Unreal et Wwise](https://www.microsoft.com/download/details.aspx?id=58090). L’exemple de package contient les éléments suivants :
- Projet de moteur Unreal
- Projet Wwise pour le projet Unreal
- Plug-in Wwise pour Project Acoustics

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurer l’exemple de projet Project Acoustics
Tout d’abord, installez le plug-in Project Acoustics dans Wwise. Ensuite, déployez les fichiers binaires Wwise dans le projet Unreal. Ensuite, ajustez le plug-in Unreal Wwise pour prendre en charge Project Acoustics.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Installer le plug-in Wwise Project Acoustics
Ouvrez Wwise Launcher. Sous l’onglet **Plugins** sous **Install New Plugins**, sélectionnez **Add From Directory**. Choisissez le répertoire *AcousticsWwisePlugin\ProjectAcoustics* qui était inclus dans le package que vous avez téléchargé.

![Option permettant d’installer le plug-in Wwise dans Wwise Launcher](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Ajouter les fichiers binaires de Wwise à l’exemple de projet Project Acoustics Unreal
1. Dans Wwise Launcher, sélectionnez l’onglet **Unreal Engine**. 
1. Sélectionnez le menu (icône) « Hamburger » en regard de **Recent Unreal Engine Projects**, puis sélectionnez **Browse for project**. Ouvrez l’exemple de fichier de projet Unreal *.uproject* dans le package *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Onglet Unreal tab dans Wwise Launcher](media/wwise-unreal-tab.png)

3. Ensuite, en regard de l’exemple de projet Project Acoustics, sélectionnez **Integrate Wwise in Project**.

   ![Wwise Launcher affiche le projet Acoustics Game Unreal avec l’option d’intégration mise en surbrillance.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Étendre les fonctionnalités du plug-in Wwise Unreal
Le plug-in Project Acoustics Unreal nécessite l’exposition d’un comportement supplémentaire à partir de l’API du plug-in Unreal Wwise. Exécutez le fichier de commandes par lots fourni avec le plug-in Project Acoustics Unreal pour automatiser ces modifications.
* Dans *AcousticsGame\Plugins\ProjectAcoustics\Resources*, exécutez *PatchWwise.bat*.

    ![Script pour corriger le projet Wwise mis en surbrillance dans une fenêtre de l’Explorateur Windows](media/patch-wwise-script.png)

* Si vous n’avez pas installé le kit SDK DirectX : En fonction de la version de Wwise que vous utilisez, vous devrez peut-être ajouter un commentaire sur la ligne qui contient `DXSDK_DIR` dans *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* :

    ![Éditeur de code montrant « DXSDK » avec des commentaires](media/directx-sdk-comment.png)

* Si vous compilez à l’aide de Visual Studio 2019 : Pour contourner une erreur de liaison avec Wwise, remplacez manuellement la valeur par défaut *VSVersion* dans *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* par **vc150** :

    ![Éditeur de code avec VSVersion remplacé par « VC150 »](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Ouvrir le projet Unreal 
Quand vous ouvrez le projet Unreal, vous êtes invité à regénérer les modules. Sélectionnez **Oui**.

Si l’ouverture du projet échoue à cause d’échecs de génération, vérifiez que vous avez installé le plug-in Project Acoustics Wwise pour la même version de Wwise que celle utilisée dans l’exemple de projet Project Acoustics.

Si vous utilisez une version d’[AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) antérieure à la version 2019.1, vous ne pouvez pas générer de banques de sons avec l’exemple de projet Project Acoustics. Vous devez intégrer Wwise version 2019.1 dans l’exemple de projet.

## <a name="experiment-with-project-acoustics-design-controls"></a>Faire des essais avec les contrôles de conception de Project Acoustics
Écoutez le son de la scène en sélectionnant le bouton Play dans l’éditeur Unreal. Utilisez les touches W, A, S et D et la souris pour vous déplacer. Pour afficher des raccourcis clavier de contrôles supplémentaires, sélectionnez F1.

Les informations suivantes décrivent certaines activités de conception à essayer.

### <a name="modify-occlusion-and-transmission"></a>Modifier l’occlusion et la transmission
Il existe des contrôles de conception Project Acoustics par source sur chaque acteur de son Unreal.

![Contrôles de conception Unreal Editor Acoustics](media/demo-scene-sound-source-design-controls.png)

Si le multiplicateur **Occlusion** est supérieur à 1 (la valeur par défaut est 1), l’occlusion est exagérée. Une valeur inférieure à 1 rend l’effet d’occlusion plus subtil.

Pour activer la transmission à travers les murs, éloignez le curseur **Transmission (dB)** de son niveau le plus bas.

### <a name="modify-wetness-for-a-source"></a>Modifier l’humidité pour une source
Pour modifier la rapidité avec laquelle l’humidité change avec la distance, utilisez la déformation de distance de perception (**Perceptual Distance Warp**). Project Acoustics calcule les niveaux d’humidité dans l’espace par le biais de la simulation. Les niveaux varient graduellement avec la distance et fournissent des indices de distance perceptifs. Pour exagérer cet effet, augmentez les niveaux d’humidité liés à la distance afin d’augmenter la déformation de distance. Des valeurs de déformation inférieures à 1 rendent plus subtiles les modifications de réverbération en fonction de la distance. Vous pouvez également apporter des ajustements plus précis à cet effet par le biais du paramètre **Wetness (dB)** .

Pour augmenter le temps d’atténuation dans l’espace, ajustez **Decay Time Scale**. Prenons le cas où le résultat de la simulation est un temps d’atténuation de 1,5 secondes. Si vous affectez la valeur 2 à **Decay Time Scale**, un temps d’atténuation de 3 secondes est appliqué à la source.

### <a name="modify-distance-based-attenuation"></a>Modifier l’atténuation basée sur la distance
Le plug-in Mixer Project Acoustics Wwise respecte l’atténuation par source basée sur la distance intégrée dans Wwise. La modification de cette courbe change le niveau du chemin de sécheresse. Le plug-in Project Acoustics ajuste le niveau d’humidité de façon à conserver la combinaison humide/sec spécifiée par la simulation et les contrôles de conception.

![Panneau des courbes d’atténuation de Wwise montrant l’atténuation qui atteint zéro avant la limite de simulation](media/demo-sounds-attenuation.png)

Project Acoustics calcule dans une zone « région de simulation » centrée autour de chaque emplacement d’un auditeur simulé. Les ressources acoustiques dans l’exemple de package ont fait l’objet d’un bake avec un rayon de région de simulation de 45 mètres. Les atténuations ont été conçues pour tomber à 0 avant 45 mètres. Bien que cette diminution ne soit pas strictement nécessaire, elle présente l’inconvénient que seule la géométrie dans les 45 mètres de l’auditeur bloquera les sons.

## <a name="next-steps"></a>Étapes suivantes
* [Intégrer le plug-in Project Acoustics](unreal-integration.md) dans votre projet Unreal.
* [Créer un compte Azure](create-azure-account.md) pour vos propres bakes.
