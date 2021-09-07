---
title: Installer des Spatial Anchors Azure pour Unity
description: Configurer un projet Unity pour utiliser des Spatial Anchors Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/30/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: d87c789b4c5f1decc468838ccabd136cec32cabc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531363"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurer des Spatial Anchors Azure dans un projet Unity

Ce guide vous montre comment prendre en main le kit SDK Azure Spatial Anchors dans votre projet Unity.

## <a name="project-requirements"></a>Configuration requise du projet

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Configuration d’un projet

Avant d’inclure le Kit de développement logiciel (SDK) Azure Spatial Anchors dans votre projet Unity, veillez à installer les packages [requis](#project-requirements) via le gestionnaire de package d’Unity.

### <a name="download-asa-packages"></a>Télécharger les packages Azure Spatial Anchors
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importer des packages Azure Spatial Anchors
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="hololens-only-configure-your-unity-project-xr-settings"></a>HoloLens uniquement : Configurer les paramètres XR de votre projet Unity
Lorsque vous développez des applications MixedReality sur HoloLens, vous devez définir la configuration XR dans Unity. Pour plus d’informations, consultez [Définition de votre configuration XR - Mixed Reality | Microsoft Docs](https://docs.microsoft.com/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr) et [Choix de la version d’Unity et du plug-in XR - Mixed Reality | Microsoft Docs](https://docs.microsoft.com/windows/mixed-reality/develop/unity/choosing-unity-version).

La version 2.9 du SDK Azure Spatial Anchors et les versions antérieures prennent uniquement en charge le plug-in XR Windows (com.unity.xr.windowsmr). Par conséquent, le package Azure Spatial Anchors HoloLens Unity a une dépendance explicite au package com.unity.xr.windowsmr.

La version 2.10.0 du SDK Azure Spatial Anchors et les versions ultérieures prennent en charge à la fois le plug-in Mixed Reality OpenXR ([com.microsoft.mixedreality.openxr](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=package&feed=Unity-packages&view=overview&package=com.microsoft.mixedreality.openxr&protocolType=Npm)) et le plug-in Windows XR ([com.unity.xr.windowsmr](https://docs.unity3d.com/Manual/com.unity.xr.windowsmr.html)). Vous devez inclure le package com.microsoft.mixedreality.openxr ou le package com.unity.xr.windowsmr à votre projet, selon votre préférence.

### <a name="android-only-configure-the-maintemplategradle-file"></a>Android uniquement : Configurer le fichier mainTemplate.gradle

1. Accédez à **Edit** > **Project Settings** > **Player**.
2. Dans le **panneau Inspector** (Inspecteur) pour **Player Settings** (Paramètres du lecteur), sélectionnez l’icône **Android**.
3. Sous la section **Build**, cochez la case **Custom Main Gradle Template** (Principal modèle Gradle personnalisé) pour générer un modèle Gradle personnalisé sur `Assets\Plugins\Android\mainTemplate.gradle`.
4. Ouvrez votre fichier `mainTemplate.gradle` dans un éditeur de texte.
5. Dans la section `dependencies`, collez les dépendances suivantes :

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Quand vous avez terminé, votre section `dependencies` doit ressembler à ce qui suit :

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Créer et localiser des ancres dans Unity](./create-locate-anchors-unity.md)
