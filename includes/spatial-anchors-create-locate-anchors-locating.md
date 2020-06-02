---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006403"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localiser une ancre spatiale cloud

La capacité à localiser une ancre spatiale cloud enregistrée précédemment constitue l’un des principaux avantages d’Azure Spatial Anchors. Il existe plusieurs méthodes permettant de localiser une ancre spatiale cloud. Vous ne pouvez utiliser une stratégie que sur un seul observateur à la fois.
- Localisez les ancres à l’aide de leur identificateur.
- Localisez les ancres qui sont connectées à une ancre précédemment localisée. Pour plus d’informations sur les relations entre les ancres, [cliquez ici](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Localisez l’ancre à l’aide de l’option [Relocalisation grossière](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Chaque fois que vous localisez une ancre, les ancres spatiales Azure essaient d’utiliser les données d’environnement collectées pour compléter les informations visuelles sur l’ancre. Si vous rencontrez des difficultés pour localiser une ancre, il peut être utile de créer une ancre, puis de la localiser plusieurs fois à partir de différents angles et conditions d’éclairage.

Si vous recherchez des ancres spatiales cloud à l’aide de leur identificateur, vous devez stocker celui-ci dans le service back-end de votre application, et le rendre accessible à tous les appareils qui peuvent s’authentifier correctement auprès de lui. Pour obtenir un exemple, consultez [Tutoriel : Partager des ancres spatiales sur des appareils](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instanciez un objet `AnchorLocateCriteria`, définissez les identificateurs que vous recherchez, et invoquez la méthode `CreateWatcher` sur la session en fournissant votre `AnchorLocateCriteria`.