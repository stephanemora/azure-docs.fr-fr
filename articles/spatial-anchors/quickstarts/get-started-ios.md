---
title: 'Démarrage rapide : Créer une application iOS'
description: Dans ce démarrage rapide, vous allez apprendre à générer une application iOS à l’aide de Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 277a669484201a060a2bb5455d6154165bbb8e84
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465165"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Démarrage rapide : Créer une application iOS avec Azure Spatial Anchors, en Swift ou Objective-C

Ce démarrage rapide explique comment créer une application iOS à l’aide d’[Azure Spatial Anchors](../overview.md) en Swift ou Objective-C. Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement sur les appareils. Quand vous aurez terminé, vous aurez une application iOS ARKit capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Spatial Anchors
> * Configurer l’identificateur et la clé du compte Spatial Anchors
> * Déployer et exécuter sur un appareil iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Une machine macOS prête pour le développement dotée des dernières versions de <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> et <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Git installé via HomeBrew. Entrez la commande suivante sur une seule ligne du Terminal : `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Puis exécutez `brew install git` et `brew install git-lfs`.
- Un appareil iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatible ARKit</a> prêt pour le développement.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Ouvrir l’exemple de projet

Dans le Terminal, exécutez les actions suivantes.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Installez les pods nécessaires à l’aide de CocoaPods :

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Accédez à `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Accédez à `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Exécutez `pod install --repo-update` pour installer les CocoaPods pour le projet.

Ouvrez maintenant `.xcworkspace` dans Xcode.

> [!NOTE]
> Si vous rencontrez des problèmes avec CocoaPod après la mise à niveau vers macOS Catalina (10.15), consultez ces [étapes de dépannage](#cocoapods-issues-on-macos-catalina-1015).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

L’étape suivante configure l’application pour qu’elle utilise votre identificateur et votre clé de compte. Vous les avez copiés dans un éditeur de texte pendant la [configuration de la ressource Spatial Anchors](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Ouvrez `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Localisez le champ `spatialAnchorsAccountKey` et remplacez `Set me` par la clé du compte.

Localisez le champ `spatialAnchorsAccountId` et remplacez `Set me` par l’identificateur du compte.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Ouvrez `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Localisez le champ `SpatialAnchorsAccountKey` et remplacez `Set me` par la clé du compte.

Localisez le champ `SpatialAnchorsAccountId` et remplacez `Set me` par l’identificateur du compte.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Déployer l’application sur votre appareil iOS

Connectez l’appareil iOS au Mac et définissez le **schéma actif** sur votre appareil iOS.

![Sélectionner l’appareil](./media/get-started-ios/select-device.png)

Sélectionnez **Build and then run the current scheme**.

![Déployer et exécuter](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Si l’erreur `library not found for -lPods-SampleObjC` s’affiche, vous avez probablement ouvert le fichier `.xcodeproj` à la place de `.xcworkspace`. Ouvrez `.xcworkspace` et réessayez.

Dans Xcode, arrêtez l’application en appuyant sur **Stop**.

## <a name="troubleshooting"></a>Dépannage

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problèmes relatifs à CocoaPods sur macOS Catalina (10.15)

Si vous avez récemment effectué une mise à jour vers macOS Catalina (10.15) en ayant déjà installé CocoaPods, ce dernier risque de passer en état défectueux et de configurer incorrectement vos pods et fichiers de projet `.xcworkspace`. Pour résoudre ce problème, vous devez réinstaller CocoaPods en exécutant les commandes suivantes :

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
