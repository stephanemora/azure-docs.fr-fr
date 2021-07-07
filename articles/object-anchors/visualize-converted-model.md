---
title: Guide pratique pour visualiser un modèle Object Anchors
description: Décrivez comment visualiser un modèle converti.
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 05/28/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: a9140004d9f4d79cabb9890851ba5c5cb7b815f2
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987817"
---
# <a name="how-to-visualize-an-object-anchors-model"></a>Guide pratique pour visualiser un modèle Object Anchors

Vous n’avez pas besoin de visualiser un modèle converti pour l’utiliser. Cependant, il existe un moyen simple de voir son maillage avant de l’utiliser si vous le souhaitez.

Suivez les étapes de notre [Démarrage rapide - Application Unity](quickstarts/get-started-unity-hololens.md) avec un changement mineur. Lors de la création de l’exemple de scène, au lieu d’ouvrir **AOASampleScene**, ouvrez **VisualizeScene** et ajoutez-la à la liste de création de scène. Ensuite, dans **Build Settings**, vérifiez que *seule* **VisualizeScene** est cochée : aucune autre scène ne doit être incluse.

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-build-settings-visualize.png" alt-text="Visualiser les paramètres de création":::

Fermez la boîte de dialogue **Build Settings** *au lieu de* sélectionner le bouton **Build**.

Dans le volet **Hierarchy**, sélectionnez le GameObject **Visualizer**.

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-hierarchy.png" alt-text="Hiérarchie":::

Dans le volet **Inspector**, recherchez la propriété **Model path** sous la section **Mesh Loader (Script)** , puis tapez le chemin de votre fichier de modèle Object Anchors, en y incluant l’extension de fichier.

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-inspector.png" alt-text="Inspecteur":::

Sélectionnez le bouton **Play** en haut de l’éditeur Unity, puis vérifiez que la vue **Scene** est sélectionnée.

:::image type="content" source="../../includes/media/object-anchors-quickstarts-unity/aoa-unity-editor.png" alt-text="Lecture et vue Scene":::

En utilisant les [contrôles de navigation de la vue Scene d’Unity](https://docs.unity3d.com/Manual/SceneViewNavigation.html), vous pouvez maintenant inspecter votre modèle Object Anchors.

:::image type="content" source="./media/object-anchors-visualize.png" alt-text="Visualiser le modèle":::
