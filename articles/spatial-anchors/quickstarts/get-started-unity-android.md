---
title: 'Démarrage rapide : Créer une application Android Unity'
description: Dans ce démarrage rapide, vous allez apprendre à générer une application Android avec Unity en utilisant Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 213cf9b64f2c43274192c22efa2fa5a7dfbce5e5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277056"
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

- Un ordinateur Windows ou macOS avec <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1+</a>, incluant les modules Android Build Support et Android SDK & NDK Tools.
  - Si vous exécutez Windows, vous avez aussi besoin de <a href="https://git-scm.com/download/win" target="_blank">Git pour Windows</a>.
  - Si vous exécutez macOS, installez Git via HomeBrew. Entrez la commande suivante sur une seule ligne du Terminal : `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Exécutez ensuite `brew install git`.
- Un appareil Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">prêt pour le développement</a> et <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatible ARCore</a>.
  - Des pilotes d’appareils supplémentaires peuvent être nécessaires pour que votre ordinateur puisse communiquer avec votre appareil Android. Cliquez [ici](https://developer.android.com/studio/run/device.html) pour obtenir des informations et instructions supplémentaires.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Télécharger et ouvrir l’exemple de projet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

Dans le volet **Project**, accédez à `Assets/AzureSpatialAnchors.Examples/Scenes` et ouvrez le fichier de scène `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Enregistrez la scène en sélectionnant **File** -> **Save**.

## <a name="export-the-android-studio-project"></a>Exporter le projet Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Sélectionnez votre appareil dans **Appareil d’exécution**, puis cliquez sur **Générer et exécuter**. Vous êtes ensuite invité à enregistrer in fichier `.apk` dont vous pouvez choisir le nom.

Suivez les instructions dans l’application pour placer et rappeler une ancre.

## <a name="troubleshooting"></a>Résolution de problèmes

Pendant l’exécution de l’application, si vous ne voyez pas de caméra en arrière-plan (au lieu de cela, l’arrière-plan est vide, bleu ou présente d’autres textures), vous devez probablement réimporter les ressources dans Unity. Arrêtez l’application. Dans le menu supérieur d’Unity, choisissez **Assets -> Reimport all**. Ensuite, exécutez de nouveau l’application.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
