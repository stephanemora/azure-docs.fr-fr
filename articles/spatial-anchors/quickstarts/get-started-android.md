---
title: Démarrage rapide - Créer une application Android avec Azure Spatial Anchors | Microsoft Docs
description: Dans ce démarrage rapide, vous allez apprendre à générer une application Android à l’aide de Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8f2a5fdaf2222de7a802e8ff2a1f6fdb37dae4c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880034"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Démarrage rapide : Créer une application Android avec Azure Spatial Anchors

Ce démarrage rapide explique comment créer une application Android à l’aide d’[Azure Spatial Anchors](../overview.md) en Java ou C++/NDK. Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement d’un appareil à un autre au fil du temps. Quand vous aurez terminé, vous aurez une application Android ARCore capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Spatial Anchors
> * Configurer l’identificateur et la clé du compte Spatial Anchors
> * Déployer et exécuter sur un appareil Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Une machine Windows ou macOS dotée d’<a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Si vous exécutez Windows, vous avez aussi besoin de <a href="https://git-scm.com/download/win" target="_blank">Git pour Windows</a>.
  - Si vous exécutez macOS, installez Git via HomeBrew. Entrez la commande suivante sur une seule ligne du Terminal : `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Exécutez ensuite `brew install git`.
  - Pour générer l’exemple NDK, vous devez aussi installer le kit NDK et CMake 3.6 SDK Tools dans Android Studio.
- Un appareil Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">prêt pour le développement</a> et <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatible ARCore</a>.
- Votre application doit cibler ARCore 1.5 (la prise en charge de ARCore 1.6+ sera disponible prochainement)

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Ouvrir l’exemple de projet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Si vous générez l’exemple du kit Android NDK, vous devez télécharger `arcore_c_api.h` [ici](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.5.0/libraries/include/arcore_c_api.h) et le placer dans `Android\NDK\libraries\include`.

Ouvrez Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Sélectionnez **Open an existing Android Studio project**, puis le projet situé à l’emplacement `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Sélectionnez **Open an existing Android Studio project**, puis le projet situé à l’emplacement `Android/NDK/`.

***

## <a name="configure-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

L’étape suivante configure l’application pour qu’elle utilise votre identificateur et votre clé de compte. Vous les avez copiés dans un éditeur de texte pendant la [configuration de la ressource Spatial Anchors](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Ouvrez `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`.

Localisez le champ `SpatialAnchorsAccountKey` et remplacez `Set me` par la clé du compte.

Localisez le champ `SpatialAnchorsAccountId` et remplacez `Set me` par l’identificateur du compte.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Ouvrez `Android/NDK/app/src/main/cpp/spatial_services_application.cc`.

Localisez le champ `SpatialAnchorsAccountKey` et remplacez `Set me` par la clé du compte.

Localisez le champ `SpatialAnchorsAccountId` et remplacez `Set me` par l’identificateur du compte.

***

## <a name="deploy-the-app-to-your-android-device"></a>Déployer l’application sur votre appareil Android

Allumez l’appareil Android, connectez-vous, puis connectez l’appareil au PC au moyen d’un câble USB.

Sélectionnez **Run** dans la barre d’outils Android Studio.

![Déployer et exécuter dans Android Studio](./media/get-started-android/android-studio-deploy-run.png)

Sélectionnez l’appareil Android dans la boîte de dialogue **Select Deployment Target** et sélectionnez **OK** pour exécuter l’application sur l’appareil Android.

Suivez les instructions dans l’application pour placer et rappeler une ancre.

Arrêtez l’application en sélectionnant **Stop** dans la barre d’outils Android Studio.

![Bouton Stop dans Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
