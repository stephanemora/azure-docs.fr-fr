---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67176774"
---
Vous ne pouvez pas mettre à jour l’emplacement d’une ancre une fois qu’elle a été créée sur le service, vous devez créer une nouvelle ancre et supprimer l’ancienne pour effectuer le suivi d’une nouvelle position.

Si vous n’avez pas besoin de localiser une ancre pour mettre à jour ses propriétés, vous pouvez utiliser la méthode `GetAnchorPropertiesAsync()`, qui renvoie un objet `CloudSpatialAnchor` avec des propriétés.
