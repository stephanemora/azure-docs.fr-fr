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
ms.openlocfilehash: e92c812ffc8b72fe79248c602e48ff01ef9fefcb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961006"
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
- Un appareil Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">prêt pour le développement</a> et <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatible ARCore</a>.

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

Sélectionnez **Export** pour ouvrir une boîte de dialogue. Sélectionnez ensuite un dossier pour exporter le projet Android Studio.

Une fois l’exportation terminée, un dossier s’affiche dans lequel figure le projet Android Studio exporté ainsi qu’un sous-dossier appelé **HelloAR U3D**.

## <a name="deploy-the-android-application"></a>Déployer l’application Android

Ouvrez Android Studio et sélectionnez **Open an existing Android Studio project**. Sélectionnez ensuite le sous-dossier **HelloAR U3D** du projet Android Studio exporté, puis cliquez sur **OK**.

À l’ouverture, une invite s’affiche vous demandant d’utiliser le wrapper Gradle. Sélectionnez **OK** pour utiliser le wrapper Gradle et ouvrir le projet.

Allumez l’appareil Android, connectez-vous, puis connectez l’appareil au PC au moyen d’un câble USB.

Sélectionnez **Run** dans la barre d’outils Android Studio.

![Déployer et exécuter dans Android Studio](./media/get-started-unity-android/android-studio-deploy-run.png)

Sélectionnez l’appareil Android dans la boîte de dialogue **Select Deployment Target** et sélectionnez **OK** pour exécuter l’application sur l’appareil Android.

Suivez les instructions dans l’application pour placer et rappeler une ancre.

> [!NOTE]
> Pendant l’exécution de l’application, si vous ne voyez pas de caméra en arrière-plan (au lieu de cela, l’arrière-plan est vide, bleu ou présente d’autres textures), vous devez probablement réimporter les ressources dans Unity. Arrêtez l’application. Dans le menu supérieur d’Unity, choisissez **Assets -> Reimport all**. Ensuite, exécutez de nouveau l’application.

Arrêtez l’application en sélectionnant **Stop** dans la barre d’outils Android Studio.

![Bouton Stop dans Android Studio](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
