---
title: Vue d’ensemble d’Azure Object Anchors
description: Découvrez comment Azure Object Anchors vous permet de détecter des objets du monde physique.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 84a69c46e9d7587ed87d8288fca5e7d8ef25f546
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983571"
---
# <a name="azure-object-anchors-overview"></a>Vue d’ensemble d’Azure Object Anchors

Azure Object Anchors permet à une application de détecter un objet du monde physique à l’aide d’un modèle 3D, et d’estimer sa position 6ddl. La position 6ddl (6 degrés de liberté) est définie comme une rotation et une translation entre un modèle 3D et son équivalent physique, l’objet réel.

La solution Azure Object Anchors se compose d’un service pour la conversion de modèle et d’un kit de développement logiciel client de runtime pour HoloLens. Le service accepte un modèle d’objet 3D et génère un modèle Azure Object Anchors. Le modèle Azure Object Anchors est utilisé avec le kit de développement logiciel (SDK) de runtime pour permettre à une application HoloLens de charger un modèle d’objet, ainsi que de détecter et suivre une ou plusieurs instances de ce modèle dans le monde physique.

:::image type="content" source="./media/object-anchors-overview.jpg" alt-text="Azure Object Anchors en action":::

## <a name="examples"></a>Exemples

Voici quelques exemples de cas d’usage rendus possibles par Azure Object Anchors :

- **Entraînement**. Créez des expériences de formation mixte pour vos employés, sans avoir à placer de marqueurs ou passer du temps à ajuster manuellement l’alignement des hologrammes. Si vous souhaitez enrichir vos expériences d’apprentissage de la réalité mixte grâce à la détection et au suivi automatisés, introduisez votre modèle dans le service Azure Object Anchors pour progresser vers l’expérience sans marqueur.

- **Guide de tâches**. Le guidage des employés dans un ensemble de tâches peut être considérablement simplifié en cas d’utilisation de la réalité mixte. La superposition d’instructions numériques et de meilleures pratiques à l’objet physique (qu’il s’agisse d’une machine d’usine ou d’une machine à café dans une cuisine communautaire) peut réduire considérablement les difficultés liées à l’exécution d’un ensemble des tâches. Le déclenchement de ces expériences nécessite généralement une forme de marqueur ou d’alignement manuel mais, avec Azure Object Anchors, vous pouvez créer une expérience qui détecte automatiquement l’objet associé à la tâche à exécuter. Ensuite, guidez le flux en toute transparence dans les instructions de réalité mixte sans marqueur ou alignement manuel.

- **Recherche de ressources**. Si vous disposez déjà d’un modèle 3D d’un objet dans votre espace physique, le service Azure Object Anchors peut vous permettre de localiser et de suivre les instances de cet objet dans votre environnement physique.

## <a name="usage-flow"></a>Déroulement de l’utilisation

Commencez par charger votre ressource 3D dans notre service de conversion Azure Object Anchors. Vous pouvez suivre la procédure d’un de nos guides de démarrage rapide :

  - [Unity HoloLens](quickstarts/get-started-unity-hololens.md)
  - [Unity HoloLens avec MRTK](quickstarts/get-started-unity-hololens-mrtk.md)
  - [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)

Le service convertit ensuite votre ressource en un modèle Azure Object Anchors. Si vous le souhaitez, téléchargez le modèle converti afin de pouvoir [visualiser son maillage](visualize-converted-model.md). Enfin, copiez le modèle sur un appareil HoloLens possédant le kit de développement logiciel (SDK) Runtime pour [Unity](/dotnet/api/Microsoft.Azure.ObjectAnchors) ou [HoloLens C++/WinRT](/cpp/api/object-anchors/winrt) : vous pouvez maintenant détecter des objets physiques qui correspondent à votre modèle d’origine.

:::image type="content" source="./media/object-anchors-flow.png" alt-text="déroulement de l’utilisation":::

## <a name="asset-requirements"></a>Exigences en matière de ressources

Chaque dimension d’une ressource doit être comprise entre 1 et 10 mètres et la taille de fichier doit être inférieure à 150 Mo.

Les formats de ressource actuellement pris en charge sont les suivants : `fbx`, `ply`, `obj`, `glb` et `gltf`.

## <a name="next-steps"></a>Étapes suivantes

Les sections suivantes fournissent des informations sur la prise en main de l’utilisation et de la création d’applications avec Azure Object Anchors.

> [!div class="nextstepaction"]
> [Ingestion de modèle](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens avec MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
