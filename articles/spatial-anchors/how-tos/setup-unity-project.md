---
title: Installer des Spatial Anchors Azure pour Unity
description: Configurer un projet Unity pour utiliser des Spatial Anchors Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812248"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurer des Spatial Anchors Azure dans un projet Unity

## <a name="requirements"></a>Spécifications

Les Spatial Anchors Azure prennent actuellement en charge Unity 2019.4 (LTS) avec les configurations suivantes.

* Unity 2019.4 avec AR Foundation 3.1 est pris en charge dans les Spatial Anchors Azure 2.4.0+.

## <a name="configuring-a-project"></a>Configuration d’un projet

Les Spatial Anchors Azure pour Unity sont actuellement distribuées à l’aide d’un package de ressources Unity (`.unitypackage`), qui se trouve dans les [Mises en production GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).

### <a name="import-the-asset-package"></a>Importer le package de ressources

1. Téléchargez le fichier `AzureSpatialAnchors.unitypackage` pour la version que vous souhaitez cibler à partir des [Mises en production GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Suivez les instructions [ici](https://docs.unity3d.com/Manual/AssetPackagesImport.html) pour importer le package de ressources Unity dans votre projet.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Créer et localiser des ancres dans Unity](./create-locate-anchors-unity.md)
