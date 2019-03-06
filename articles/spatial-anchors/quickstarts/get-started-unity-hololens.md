---
title: Démarrage rapide - Créer une application HoloLens Unity avec Azure Spatial Anchors | Microsoft Docs
description: Dans ce démarrage rapide, vous allez apprendre à générer une application HoloLens avec Unity en utilisant Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b2bfec47bc92ebf5db1561d8fca33940dc376866
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752610"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>Démarrage rapide : Créer une application HoloLens Unity à l’aide d’Azure Spatial Anchors

Ce démarrage rapide explique comment créer une application HoloLens Unity en utilisant [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement d’un appareil à un autre au fil du temps. Quand vous aurez terminé, vous aurez une application HoloLens générée avec Unity capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Spatial Anchors
> * Préparer les paramètres de build Unity
> * Configurer l’identificateur et la clé du compte Spatial Anchors
> * Exporter le projet HoloLens Visual Studio
> * Déployer et exécuter sur un appareil HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Une machine Windows dotée d’<a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> et de <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> avec la charge de travail de **développement pour la plateforme Windows universelle**.
- Un appareil HoloLens avec le [mode développeur](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) activé.
- Votre application doit définir la fonctionnalité **SpatialPerception** sous **Build Settings** (Paramètres de build)->**Player Settings** (Paramètres du lecteur)->**Publishing Settings** (Paramètres de publication)->**Capabilities** (Fonctionnalités).
- Votre application doit activer **Virtual Reality Supported** (Réalité virtuelle prise en charge) avec le kit **SDK Windows Mixed Reality** sous **Build Settings** (Paramètres de build)->**Player Settings** (Paramètres du lecteur)->**XR Settings** (Paramètres XR).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Ouvrir l’exemple de projet dans Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Ouvrez Unity, puis le projet situé dans le dossier `Unity`.

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** (Fichier) -> **Build Settings** (Paramètres de build).

Dans la section **Platform** (Plateforme), sélectionnez **Universal Windows Platform** (Plateforme Windows universelle). Ensuite, pour **Target Device** (Appareil cible), choisissez **HoloLens**.

Sélectionnez **Switch Platform** (Changer de plateforme) pour passer à la plateforme **Universal Windows Platform** (Plateforme Windows universelle).

![Paramètres de build Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Fermez la fenêtre **Build Settings**.

## <a name="configure-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

Dans le volet **Project**, accédez à `Assets/AzureSpatialAnchorsPlugin/Examples` et ouvrez le fichier de scène `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Enregistrez la scène en sélectionnant **File** -> **Save**.

## <a name="export-the-hololens-visual-studio-project"></a>Exporter le projet HoloLens Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Sélectionnez **Build** (Générer) pour ouvrir une boîte de dialogue. Sélectionnez ensuite un dossier pour exporter le projet HoloLens Visual Studio.

À l’issue de l’exportation, un dossier s’affiche dans lequel figure le projet HoloLens exporté.

## <a name="deploy-the-hololens-application"></a>Déployer l’application HoloLens

Dans le dossier, double-cliquez sur `HelloAR U3D.sln` pour ouvrir le projet dans Visual Studio.

Pour **Configuration de la solution**, choisissez **Mise en production**, pour **Plateforme de solution** choisissez **x86**, puis sélectionnez **Appareil**  dans les options de cible de déploiement.

![Configuration de Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Allumez l’appareil HoloLens, connectez-vous, puis connectez l’appareil au PC au moyen d’un câble USB.

Sélectionnez **Déboguer** > **Démarrer le débogage** pour déployer votre application et commencer le débogage.

Suivez les instructions dans l’application pour placer et rappeler une ancre.

Dans Visual Studio, arrêtez l’application en sélectionnant **Arrêter le débogage** ou en appuyant sur **Maj+F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
