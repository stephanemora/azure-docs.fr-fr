---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176779"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localiser une ancre spatiale cloud

La capacité à localiser une ancre spatiale cloud chargée précédemment constitue l’une des principales raisons d’utiliser la bibliothèque Azure Spatial Anchors. Pour localiser les ancres spatiales cloud, vous devez connaître leurs identificateurs. L’ID d’une ancre peut être stocké dans le service back-end de votre application, et est accessible à tous les appareils qui peuvent s’y authentifier correctement. Pour obtenir un exemple, consultez [Didacticiel : Partager des ancres spatiales sur des appareils](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instanciez un objet `AnchorLocateCriteria`, définissez les identificateurs que vous recherchez, et invoquez la méthode `CreateWatcher` sur la session en fournissant votre `AnchorLocateCriteria`.
