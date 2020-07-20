---
title: Espace scénique
description: Décrit les paramètres et les cas d’utilisation de l’espace scénique
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: b67294c503e513290b474e0059771a73ad526a6a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189341"
---
# <a name="stage-space"></a>Espace scénique

Lorsque vous exécutez ARR sur un appareil qui fournit des données de suivi de la tête comme le HoloLens 2, la posture de la tête est envoyée à l’application utilisateur et au serveur. L’espace dans lequel la transformation de tête est définie est appelé *espace scénique*.

Pour aligner le contenu local et distant, il est supposé que l’espace scénique et l’espace universel sont identiques sur le client et le serveur. Si l’utilisateur décide d’ajouter une transformation supplémentaire en plus de la caméra, elle doit être envoyée également au serveur pour permettre un alignement correct du contenu local et distant.

Les raisons courantes amenant à déplacer l’espace scénique sont les [outils de verrouillage universel](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) ou d’autres techniques d’ancrage actuelles, ainsi que le déplacement d’un personnage virtuel en VR.

## <a name="stage-space-settings"></a>Paramètres de l’espace scénique

> [!CAUTION]
> EXPÉRIMENTAL : Cette fonctionnalité est expérimentale et évoluera dans le temps. Par conséquent, la mise à jour vers des versions plus récentes du kit SDK client peut nécessiter un travail supplémentaire pour mettre à niveau le code. L’implémentation actuelle interrompt l’alignement du contenu local/distant pendant un bref instant lors de la modification de l’origine de l’espace scénique.
Par conséquent, elle est uniquement destinée à être utilisée à des fins de verrouillage universel, comme les points d’ancrage qui présentent uniquement de très petites modifications au fil du temps.

Pour informer le serveur qu’une transformation supplémentaire est appliquée à l’espace scénique, il faut envoyer son origine définie par une position et une rotation dans l’espace universel. Ce paramètre est accessible via le *paramètre d’espace scénique*.

> [!IMPORTANT]
> Dans la [simulation du Bureau](../../concepts/graphics-bindings.md), l’emplacement de l’espace universel de la caméra est fourni par l’application utilisateur. Dans ce cas, la définition de l’origine de l’espace scénique doit être ignorée, car elle est déjà multipliée dans la transformation de la caméra.

```cs
void ChangeStageSpace(AzureSession session)
{
    StageSpaceSettings settings = session.Actions.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<StageSpaceSettings> settings = *session->Actions()->StageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->Position({0, 0, 0});
    settings->Rotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Script d’espace scénique Unity

L’intégration Unity fournit un script appelé **StageSpace** qui peut être ajouté à l’objet GameObject parent de la caméra. Une fois présent, ce script s’occupe de la définition de l’origine de l’espace scénique.

## <a name="next-steps"></a>Étapes suivantes

* [Liaison graphique](../../concepts/graphics-bindings.md)
* [Reprojection en phase tardive](late-stage-reprojection.md)
