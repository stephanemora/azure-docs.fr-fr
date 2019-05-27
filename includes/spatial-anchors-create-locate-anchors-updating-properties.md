---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110769"
---
## <a name="update-properties"></a>Mettre à jour les propriétés

Pour mettre à jour les propriétés sur un point d’ancrage, vous utilisez le `UpdateAnchorProperties()` (méthode). Si vous essaient de deux ou plusieurs appareils mettre à jour des propriétés pour le même point d’ancrage en même temps, nous utilisons un modèle d’accès concurrentiel optimiste. Ce qui signifie que la première écriture gagne.  Toutes les autres écritures obtiendra une erreur « Accès concurrentiel » : une actualisation des propriétés est nécessaire avant de réessayer.
