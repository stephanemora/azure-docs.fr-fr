---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110811"
---
Vous ne pouvez pas mettre à jour l’emplacement d’une ancre une fois qu’il a été créé sur le service, vous devez créer un nouveau point d’ancrage et supprimer l’ancienne pour effectuer le suivi d’une nouvelle position.

Si vous n’avez pas besoin de localiser un point d’ancrage pour mettre à jour ses propriétés, vous pouvez utiliser la `GetAnchorPropertiesAsync()` (méthode), qui retourne un `CloudSpatialAnchor` objet avec des propriétés.
