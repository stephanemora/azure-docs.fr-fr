---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907634"
---
## <a name="update-properties"></a>Mettre à jour les propriétés

Pour mettre à jour les propriétés sur un point d’ancrage, vous utilisez la méthode UpdateAnchorProperties. Si vous essaient de deux ou plusieurs appareils mettre à jour des propriétés pour le même point d’ancrage en même temps, nous utilisons un modèle d’accès concurrentiel optimiste. Ce qui signifie que la première écriture gagne.  Toutes les autres écritures obtiendra une erreur « Accès concurrentiel » : une actualisation des propriétés est nécessaire avant de réessayer.
