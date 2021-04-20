---
title: 'Démarrage rapide : Créer une application iOS Unity'
description: Dans ce guide de démarrage rapide, vous allez apprendre à générer une application iOS avec Unity en utilisant Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b341237d0e703239233f7ac0e9664f5fb90bbb4b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105803"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Démarrage rapide : Créer une application iOS Unity avec Azure Spatial Anchors

Ce guide de démarrage rapide explique comment créer une application iOS Unity à l’aide d’[Azure Spatial Anchors](../overview.md). Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement sur les appareils. Quand vous aurez terminé, vous aurez une application iOS ARKit générée avec Unity capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Spatial Anchors
> * Préparer les paramètres de build Unity
> * Configurer l’identificateur et la clé du compte Spatial Anchors
> * Exporter le projet Xcode
> * Déployer et exécuter sur un appareil iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Une machine macOS avec la dernière version de <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> et de <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a> installée. Utilisez **Unity 2020 LTS** avec le kit SDK ASA 2.9 ou version ultérieure (qui utilise le [Framework de plug-in Unity XR](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)) ou **Unity 2019 LTS** avec le kit SDK ASA 2.8 ou version antérieure.
- Git installé via HomeBrew. Entrez la commande suivante sur une seule ligne du Terminal : `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Puis exécutez `brew install git` et `brew install git-lfs`.
- Un appareil iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatible ARKit</a> prêt pour le développement.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Télécharger et ouvrir l’exemple de projet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Suivez les instructions fournies [ici](../how-tos/setup-unity-project.md#download-asa-packages) pour télécharger et importer les packages du SDK ASA nécessaires pour la plateforme iOS.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-xcode-project"></a>Exporter le projet Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Dans l’application, sélectionnez **BasicDemo** à l’aide des flèches, puis appuyez sur le bouton **Go!** pour exécuter la démo. Suivez les instructions dans l’application pour placer et rappeler une ancre.

![Capture d’écran 1](./media/get-started-unity-ios/screenshot-1.jpg)
![Capture d’écran 2](./media/get-started-unity-ios/screenshot-2.jpg)
![Capture d’écran 3](./media/get-started-unity-ios/screenshot-3.jpg)

Lorsque vous avez terminé, arrêtez l’application en appuyant sur **Stop** dans Xcode.

## <a name="troubleshooting"></a>Dépannage

### <a name="rendering-issues"></a>Problèmes de rendu

Pendant l’exécution de l’application, si vous ne voyez pas de caméra en arrière-plan (au lieu de cela, l’arrière-plan est vide, bleu ou présente d’autres textures), vous devez probablement réimporter les ressources dans Unity. Arrêtez l’application. Dans le menu supérieur d’Unity, choisissez **Assets -> Re-import all**. Ensuite, exécutez de nouveau l’application.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Guide pratique pour configurer Azure Spatial Anchors dans un projet Unity](../how-tos/setup-unity-project.md)
