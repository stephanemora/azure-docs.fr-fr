---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882310"
---
## <a name="android"></a>[Android](#tab/Android)

L’exemple Android Java prend en charge le partage entre appareils.
Ouvrez le fichier `SharedActivity.java` se trouvant dans le dossier d’exemples d’Android Studio. Entrez l’URL que vous avez obtenue à l’étape précédente (à partir du déploiement Azure de votre application web ASP.NET) comme valeur pour `SharingAnchorsServiceUrl` dans le fichier `SharedActivity.java`. Remplacez `index.html` dans l’URL par `api/anchors`. Ce que vous obtenez doit ressembler à ceci : `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

L’exemple iOS Objective-C prend en charge le partage entre appareils.
Ouvrez le fichier `SharedDemoViewController.m` se trouvant dans le dossier d’exemples. Entrez l’URL que vous avez obtenue à l’étape précédente (à partir du déploiement Azure de votre application web ASP.NET) comme valeur pour `SharingAnchorsServiceUrl` dans le fichier `SharedActivity.java`. Remplacez `index.html` dans l’URL par `api/anchors`. Ce que vous obtenez doit ressembler à ceci : `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Les exemples Xamarin Android et iOS prennent en charge le partage entre appareils.
Ouvrez le fichier `AccountDetails.cs` se trouvant dans le dossier d’exemples. Entrez l’URL que vous avez obtenue à l’étape précédente (à partir du déploiement Azure de votre application web ASP.NET) comme valeur pour `AnchorSharingServiceUrl` dans le fichier `SharedActivity.java`. Remplacez `index.html` dans l’URL par `api/anchors`. Ce que vous obtenez doit ressembler à ceci : `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurer un appareil Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurer un appareil iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

Dans le volet **Project**, accédez à `Assets/AzureSpatialAnchorsPlugin/Examples` et ouvrez le fichier de scène `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Dans le volet **Projet**, accédez à `Assets\AzureSpatialAnchors.Examples\Resources`. Sélectionnez `SpatialAnchorSamplesConfig`. Ensuite, dans le volet **Inspector** (Inspecteur), entrez `Sharing Anchors Service url` (à partir du déploiement Azure de votre application web ASP.NET) comme valeur pour `Base Sharing Url`, en remplaçant `index.html` par `api/anchors`. Ce que vous obtenez doit ressembler à ceci : `https://<app_name>.azurewebsites.net/api/anchors`.

Enregistrez la scène en sélectionnant **File** > **Save**.

## <a name="deploy-to-your-device"></a>Déployer sur votre appareil

### <a name="deploy-to-android-device"></a>Déployer sur un appareil Android

Connectez-vous sur votre appareil Android et reliez-le à votre ordinateur à l’aide d’un câble USB.

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** (Fichier) > **Build Settings** (Paramètres de build).

Sous **Scenes In Build** (Scènes dans la build), assurez-vous que toutes les scènes ont une coche à côté d’elles.

Assurez-vous que la case **Export Project** (Exporter le projet) n’est pas cochée. Sélectionnez **Build And Run** (Générer et exécuter). Vous êtes invité à enregistrer votre fichier `.apk`. Vous pouvez choisir n’importe quel nom pour celui-ci.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Déployer sur un appareil iOS

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** (Fichier) > **Build Settings** (Paramètres de build).

Sous **Scenes In Build** (Scènes dans la build), assurez-vous que toutes les scènes ont une coche à côté d’elles.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Dans Xcode, arrêtez l’application en sélectionnant **Stop**.
