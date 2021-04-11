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
ms.openlocfilehash: fe9160f22754c62888b2a61ce9751f596842604e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076692"
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
