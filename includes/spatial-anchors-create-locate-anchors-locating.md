---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632241"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Recherchez un ancrage spatial cloud

Qui est en mesure de localiser un point d’ancrage spatial cloud précédemment téléchargé est une des raisons principales pour l’utilisation de la bibliothèque ancres Spatial Azure. Pour localiser les ancres spatial cloud, vous devez connaître leurs identificateurs. ID d’ancrage peuvent être stockées dans le service de back-end de votre application et accessible à tous les appareils qui peuvent s’authentifier correctement à ce dernier. Pour obtenir un exemple de cette consultez [didacticiel : Partager des ancres spatiales entre appareils](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instancier un `AnchorLocateCriteria` d’objets, définir les identificateurs que vous cherchez et appelez le `CreateWatcher` méthode sur la session en fournissant votre `AnchorLocateCriteria`.
