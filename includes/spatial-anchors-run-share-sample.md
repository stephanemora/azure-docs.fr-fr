---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903925"
---
## <a name="set-up-your-device-in-unity"></a>Configurer votre appareil dans Unity

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

Une fois que l’application démarre, dans la boîte de dialogue **Choisir une démonstration**, utilisez les flèches gauche et droite pour sélectionner l’option **LocalShare**, puis appuyez sur **Aller !** . Suivez les instructions de l’application. Vous pouvez sélectionner **Create & Share Anchor** (Créer et partager l’ancre) ou **Locate Shared Anchor** (Localiser l’ancre partagée).

Le premier scénario vous permet de créer une ancre qui peut être localisée par la suite sur le même appareil ou un autre.
Si vous avez déjà exécuté l’application sur le même appareil ou un autre, le deuxième scénario vous permet de localiser les ancres partagées antérieurement. Après avoir choisi votre scénario, l’application vous guide avec des instructions supplémentaires pour les tâches à effectuer. Par exemple, vous serez invité à déplacer votre appareil pour collecter des informations sur l’environnement. Par la suite vous allez placer une ancre dans le monde, attendre qu’elle enregistre et ainsi de suite.

### <a name="deploy-to-an-ios-device"></a>Déployer sur un appareil iOS

Ouvrez **Build Settings** (Paramètres de build) en sélectionnant **File** (Fichier) > **Build Settings** (Paramètres de build).

Sous **Scenes In Build** (Scènes dans la build), assurez-vous que toutes les scènes ont une coche à côté d’elles.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Une fois que l’application démarre, dans la boîte de dialogue **Choisir une démonstration**, utilisez les flèches gauche et droite pour sélectionner l’option **LocalShare**, puis appuyez sur **Aller !** . Suivez les instructions de l’application. Vous pouvez sélectionner **Create & Share Anchor** (Créer et partager l’ancre) ou **Locate Shared Anchor** (Localiser l’ancre partagée).

Le premier scénario vous permet de créer une ancre qui peut être localisée par la suite sur le même appareil ou un autre.
Si vous avez déjà exécuté l’application sur le même appareil ou un autre, le deuxième scénario vous permet de localiser les ancres partagées antérieurement. Après avoir choisi votre scénario, l’application vous guide avec des instructions supplémentaires pour les tâches à effectuer. Par exemple, vous serez invité à déplacer votre appareil pour collecter des informations sur l’environnement. Par la suite vous allez placer une ancre dans le monde, attendre qu’elle enregistre et ainsi de suite.

Dans Xcode, arrêtez l’application en sélectionnant **Stop**.
