---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305341"
---
## <a name="set-up-your-device"></a>Configurer votre appareil

### <a name="set-up-an-android-device"></a>Configurer un appareil Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurer un appareil iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

Dans le volet **Project**, accédez à `Assets/AzureSpatialAnchorsPlugin/Examples` et ouvrez le fichier de scène `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Dans le volet **Inspector** (Inspecteur), entrez `Sharing Anchors Service url` (à partir du déploiement Azure de votre application web ASP.NET) comme valeur pour `Base Sharing Url`, en remplaçant `index.html` par `api/anchors`. Ce que vous obtenez doit ressembler à ceci : `https://<app_name>.azurewebsites.net/api/anchors`.

Enregistrez la scène en sélectionnant **File** > **Save**.

## <a name="to-deploy-the-app-to-an-android-device"></a>Pour déployer l’application sur un appareil Android

Connectez-vous sur votre appareil Android et reliez-le à votre ordinateur à l’aide d’un câble USB.

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** (Fichier) > **Build Settings** (Paramètres de build).

Sous **Scenes In Build** (Scènes dans la build), cochez la scène `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` et supprimez les coches de toutes les autres scènes.

Assurez-vous que la case **Export Project** (Exporter le projet) n’est pas cochée. Sélectionnez **Build And Run** (Générer et exécuter). Vous êtes invité à enregistrer votre fichier `.apk`. Vous pouvez choisir n’importe quel nom pour celui-ci.

Suivez les instructions de l’application. Vous pouvez sélectionner **Create & Share Anchor** (Créer et partager l’ancre) ou **Locate Shared Anchor** (Localiser l’ancre partagée). La première option permet de créer une ancre qui peut se trouver par la suite sur le même ou un autre appareil. La deuxième option, si vous avez déjà exécuté l’application sur le même ou un autre appareil, permet de localiser les ancres précédemment partagées.

## <a name="to-deploy-the-app-to-an-ios-device"></a>Pour déployer l’application sur un appareil iOS

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** (Fichier) > **Build Settings** (Paramètres de build).

Sous **Scenes In Build** (Scènes dans la build), cochez la scène `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` et supprimez les coches de toutes les autres scènes.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Suivez les instructions de l’application. Vous pouvez sélectionner **Create & Share Anchor** (Créer et partager l’ancre) ou **Locate Shared Anchor** (Localiser l’ancre partagée). La première option permet de créer une ancre qui peut se trouver par la suite sur le même ou un autre appareil. La deuxième option, si vous avez déjà exécuté l’application sur le même ou un autre appareil, permet de localiser les ancres précédemment partagées.

Dans Xcode, arrêtez l’application en sélectionnant **Stop**.
