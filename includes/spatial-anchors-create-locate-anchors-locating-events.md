---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176782"
---
Une fois votre ressource Watcher créée, l’événement `AnchorLocated` se déclenche pour chaque ancre demandée. Cet événement se déclenche lorsqu’une ancre est localisée, ou si l’ancre ne peut pas être localisée. Si cette situation se produit, la raison est indiquée dans l’état. Après le traitement de toutes les ancres d’une ressource Watcher, trouvées ou non, l’événement `LocateAnchorsCompleted` se déclenche.
