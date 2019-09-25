---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180047"
---
Une fois votre ressource Watcher créée, l’événement `AnchorLocated` se déclenche pour chaque ancre demandée. Cet événement se déclenche lorsqu’une ancre est localisée, ou si l’ancre ne peut pas être localisée. Si cette situation se produit, la raison est indiquée dans l’état. Après le traitement de toutes les ancres d’une ressource Watcher, trouvées ou non, l’événement `LocateAnchorsCompleted` se déclenche. Il y a une limite de 35 identificateurs par ressource Watcher. 
