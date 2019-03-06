---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753472"
---
## <a name="open-the-sample-project-in-unity"></a>Ouvrir l’exemple de projet dans Unity

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Configuration pour un appareil Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Configuration pour un appareil iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

Dans le volet **Project** (Projet), accédez à `Assets/AzureSpatialAnchorsPlugin/Examples` et ouvrez le fichier de scène `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

En outre, dans le volet **Inspector** (Inspecteur), entrez `Sharing Anchors Service url` (à partir du déploiement Azure de votre application web ASP.NET) comme valeur pour `Base Sharing Url`, en remplaçant `index.html` par `api/anchors`. Cela doit donc ressembler à : `https://<app_name>.azurewebsites.net/api/anchors`.

Enregistrez la scène en sélectionnant **File** -> **Save** (Fichier - Enregistrer).

## <a name="to-deploy-to-an-android-device"></a>Pour déployer sur un appareil Android

Allumez l’appareil Android, connectez-vous, puis connectez l’appareil au PC au moyen d’un câble USB.

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** -> **Build Settings**.

Sous **Scenes In Build** (Scènes dans la build), cochez la scène `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` et supprimez les coches de toutes les autres scènes.

Vérifiez que la case à cocher **Export Project** (Exporter le projet) n’est pas cochée. Cliquez sur **Build And Run** (Générer et exécuter). Vous allez être invité à enregistrer votre fichier `.apk` et vous pouvez choisir n’importe quel nom pour celui-ci.

Suivez les instructions de l’application. Vous pouvez choisir **Create & Share Anchor** (Créer et partager l’ancre) ou **Locate Shared Anchor** (Localiser l’ancre partagée). La première option permet de créer une ancre qui peut se trouver par la suite sur le même ou un autre appareil. La deuxième option, si vous avez auparavant exécuté l’application sur le même ou un autre appareil, permet de localiser les ancres précédemment partagées.

## <a name="to-deploy-to-an-ios-device"></a>Pour déployer sur un appareil iOS

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** -> **Build Settings**.

Sous **Scenes In Build** (Scènes dans la build), cochez la scène `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` et supprimez les coches de toutes les autres scènes.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Suivez les instructions de l’application. Vous pouvez choisir **Create & Share Anchor** (Créer et partager l’ancre) ou **Locate Shared Anchor** (Localiser l’ancre partagée). La première option permet de créer une ancre qui peut se trouver par la suite sur le même ou un autre appareil. La deuxième option, si vous avez auparavant exécuté l’application sur le même ou un autre appareil, permet de localiser les ancres précédemment partagées.

Dans Xcode, arrêtez l’application en appuyant sur **Stop** (Arrêter).
