---
title: Démarrage rapide - Créer une application Android Unity avec Azure Spatial Anchors | Microsoft Docs
description: Dans ce démarrage rapide, vous allez apprendre à générer une application Android avec Unity en utilisant Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5647aa20f444d5efd36f03d813ee87ef199cc41
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621870"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Démarrage rapide : Créer une application Android Unity avec Azure Spatial Anchors

Ce démarrage rapide explique comment créer une application Android Unity en utilisant [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement d’un appareil à un autre au fil du temps. Quand vous aurez terminé, vous aurez une application ARCore Android générée avec Unity capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Spatial Anchors
> * Préparer les paramètres de build Unity
> * Télécharger et importer le kit SDK ARCore pour Unity
> * Configurer l’identificateur et la clé du compte Spatial Anchors
> * Exporter le projet Android Studio
> * Déployer et exécuter sur un appareil Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Une machine Windows ou macOS dotée d’<a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> et d’<a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Si vous exécutez Windows, vous avez aussi besoin de <a href="https://git-scm.com/download/win" target="_blank">Git pour Windows</a>.
  - Si vous exécutez macOS, installez Git via HomeBrew. Entrez la commande suivante sur une seule ligne du Terminal : `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Exécutez ensuite `brew install git`.
- Un appareil Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">prêt pour le développement</a> et <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatible ARCore</a>.
- Votre application doit utiliser la version **1.5** du SDK ARCore pour Unity (la prise en charge d’ARCore 1.6 + sera disponible à une date ultérieure).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Ouvrir l’exemple de projet dans Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

Dans le volet **Project**, accédez à `Assets/AzureSpatialAnchorsPlugin/Examples` et ouvrez le fichier de scène `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Enregistrez la scène en sélectionnant **File** -> **Save**.

## <a name="export-the-android-studio-project"></a>Exporter le projet Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Vérifiez que la case à cocher **Export Project** (Exporter le projet) n’est pas cochée. Cliquez sur **Build And Run** (Générer et exécuter). Vous allez être invité à enregistrer votre fichier `.apk` et vous pouvez choisir n’importe quel nom pour celui-ci.

Suivez les instructions dans l’application pour placer et rappeler une ancre.

> [!NOTE]
> Pendant l’exécution de l’application, si vous ne voyez pas de caméra en arrière-plan (au lieu de cela, l’arrière-plan est vide, bleu ou présente d’autres textures), vous devez probablement réimporter les ressources dans Unity. Arrêtez l’application. Dans le menu supérieur d’Unity, choisissez **Assets -> Reimport all**. Ensuite, exécutez de nouveau l’application.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
