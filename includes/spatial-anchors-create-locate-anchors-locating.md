---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907744"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Recherchez un ancrage spatial cloud

Pour localiser les ancres spatial cloud, vous devez connaître leurs identificateurs. ID d’ancrage peuvent être stockée dans le service de serveur principal de votre application et accessible à tous les appareils qui peuvent s’authentifier correctement à ce dernier. Pour obtenir un exemple de cette consultez [didacticiel : Partager des ancres spatiales entre appareils](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instancier un objet AnchorLocateCriteria, définir les identificateurs que vous cherchez et appelez la méthode CreateWatcher sur la session en fournissant votre AnchorLocateCriteria.
