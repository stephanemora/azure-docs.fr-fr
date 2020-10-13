---
title: Utiliser Holographic Remoting et Remote Rendering dans Unity
description: Comment utiliser l’aperçu Holographic Remoting avec Azure Remote Rendering
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: 180af30f57a8123b6e90cc8b11848b92b3c86db1
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802172"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Utiliser Holographic Remoting et Remote Rendering dans Unity

[Holographic Remoting](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) et Azure Remote Rendering s’excluent mutuellement au sein d’une même application. Le [mode lecture de Unity](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) n’est donc pas disponible non plus.

Seul l’un des deux peut être utilisé à chaque exécution de l’éditeur Unity. Pour utiliser l’autre, commencez par redémarrer Unity.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Utiliser le mode lecture de Unity pour l’aperçu sur HoloLens 2

 Le mode lecture de Unity peut toujours être utilisé, par exemple pour tester l’interface utilisateur de l’application. Toutefois, ARR ne doit en aucun cas être initialisé. Sinon, il plantera.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Utiliser un casque de réalité virtuelle WMR pour l’aperçu sur le Bureau

Si vous disposez d’un casque de réalité virtuelle Windows Mixed Reality, vous pouvez l’utiliser pour l’aperçu dans Unity. Dans ce cas, vous pouvez initialiser ARR. Cependant, vous ne pourrez pas vous connecter à une session tant que le casque WMR est utilisé.
