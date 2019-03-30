---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632311"
---
Vous ne pouvez pas mettre à jour l’emplacement d’une ancre une fois qu’il a été créé sur le service, vous devez créer un nouveau point d’ancrage et supprimer l’ancienne pour effectuer le suivi d’une nouvelle position.

Si vous n’avez pas besoin de localiser un point d’ancrage pour mettre à jour ses propriétés, vous pouvez utiliser la `GetAnchorPropertiesAsync()` (méthode), qui retourne un `CloudSpatialAnchor` objet avec des propriétés.
