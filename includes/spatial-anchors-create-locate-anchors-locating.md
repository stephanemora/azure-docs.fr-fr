---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76887844"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localiser une ancre spatiale cloud

La capacité à localiser une ancre spatiale cloud enregistrée précédemment constitue l’un des principaux avantages d’Azure Spatial Anchors. Il existe plusieurs méthodes permettant de localiser une ancre spatiale cloud. Vous ne pouvez utiliser une stratégie que sur un seul observateur à la fois.
- Localisez les ancres à l’aide de leur identificateur.
- Localisez les ancres qui sont connectées à une ancre précédemment localisée. Pour plus d’informations sur les relations entre les ancres, [cliquez ici](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Localisez l’ancre à l’aide de l’option [Relocalisation grossière](/azure/spatial-anchors/concepts/coarse-reloc/).

Si vous recherchez des ancres spatiales cloud à l’aide de leur identificateur, vous devez stocker celui-ci dans le service back-end de votre application, et le rendre accessible à tous les appareils qui peuvent s’authentifier correctement auprès de lui. Pour obtenir un exemple, consultez [Tutoriel : Partager des ancres spatiales sur des appareils](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instanciez un objet `AnchorLocateCriteria`, définissez les identificateurs que vous recherchez, et invoquez la méthode `CreateWatcher` sur la session en fournissant votre `AnchorLocateCriteria`.