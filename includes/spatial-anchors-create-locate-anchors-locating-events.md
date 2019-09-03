---
ms.openlocfilehash: f478956c3a220ea4435c4f6e8b1096a1e9fdcadf
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014763"
---
Une fois votre ressource Watcher créée, l’événement `AnchorLocated` se déclenche pour chaque ancre demandée. Cet événement se déclenche lorsqu’une ancre est localisée, ou si l’ancre ne peut pas être localisée. Si cette situation se produit, la raison est indiquée dans l’état. Après le traitement de toutes les ancres d’une ressource Watcher, trouvées ou non, l’événement `LocateAnchorsCompleted` se déclenche. Il y a une limite de 35 identificateurs par ressource Watcher. 
