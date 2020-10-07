---
title: 'Démarrage rapide : Créer une application Android Unity'
description: Dans ce démarrage rapide, vous allez apprendre à générer une application Android avec Unity en utilisant Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c84ebfe9752b8594e97c7c72d706ee25c4852f6e
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538125"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Démarrage rapide : Créer une application Android Unity avec Azure Spatial Anchors

Ce guide de démarrage rapide explique comment créer une application Android Unity en utilisant [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement sur les appareils. Quand vous aurez terminé, vous aurez une application ARCore Android générée avec Unity capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Spatial Anchors
> * Préparer les paramètres de build Unity
> * Configurer l’identificateur et la clé du compte Spatial Anchors
> * Exporter le projet Android Studio
> * Déployer et exécuter sur un appareil Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Une machine Windows ou macOS avec <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a>, avec les modules **Android Build Support** avec **Android SDK & NDK Tools** et **OpenJDK**.
  - Si vous exécutez Windows, vous avez aussi besoin de <a href="https://git-scm.com/download/win" target="_blank">Git pour Windows</a> et <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Si vous exécutez macOS, installez Git via HomeBrew. Entrez la commande suivante sur une seule ligne du Terminal : `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Puis exécutez `brew install git` et `brew install git-lfs`.
- Un appareil Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">prêt pour le développement</a> et <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatible ARCore</a>.
  - Des pilotes d’appareils supplémentaires peuvent être nécessaires pour que votre ordinateur puisse communiquer avec votre appareil Android. Vous pouvez obtenir des informations et des instructions supplémentaires [ici](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Télécharger et ouvrir l’exemple de projet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Exporter le projet Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Sélectionnez votre appareil dans **Appareil d’exécution**, puis sélectionnez **Générer et exécuter**. Vous êtes ensuite invité à enregistrer in fichier `.apk`, auquel vous pouvez donner n’importe quel nom.

Dans l’application, sélectionnez **BasicDemo** à l’aide des flèches, puis appuyez sur le bouton **Go!** pour exécuter la démo. Suivez les instructions pour placer et rappeler une ancre.

![Capture d’écran 1](./media/get-started-unity-android/screenshot-1.jpg)
![Capture d’écran 2](./media/get-started-unity-android/screenshot-2.jpg)
![Capture d’écran 3](./media/get-started-unity-android/screenshot-3.jpg)

Suivez les instructions dans l’application pour placer et rappeler une ancre.

## <a name="troubleshooting"></a>Dépannage

### <a name="rendering-issues"></a>Problèmes de rendu

Pendant l’exécution de l’application, si vous ne voyez pas de caméra en arrière-plan (par exemple, vous voyez au lieu de cela un arrière-plan blanc, bleu ou d’une autre texture), vous devez probablement réimporter les ressources dans Unity. Arrêtez l’application. Dans le menu supérieur d’Unity, choisissez **Assets -> Reimport all**. Ensuite, exécutez de nouveau l’application.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Guide pratique pour configurer Azure Spatial Anchors dans un projet Unity](../how-tos/setup-unity-project.md)
