---
author: baanders
description: Fichier include pour Azure Digital Twins - Instructions relatives au modèle pour le tutoriel avec la ligne de commande
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: 784dde0790633adbbae0ae315ce0fd5c271bdb4c
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108766750"
---
1. **Mettez à jour le numéro de version** pour indiquer que vous fournissez une version mise à jour de ce modèle. Pour ce faire, remplacez le *1* à la fin de la valeur `@id` par un *2*. Tout nombre supérieur au numéro de version actuel convient aussi.
1. **Modifiez une propriété**. Remplacez le nom de la propriété `Humidity` par *HumidityLevel* (ou une autre valeur si vous le souhaitez. Si vous utilisez autre chose que *HumidityLevel*, souvenez-vous de ce que vous avez utilisé et continuez à l’utiliser à la place de *HumidityLevel* tout au long du tutoriel).
1. **Ajoutez une propriété**. Sous la propriété `HumidityLevel` qui se termine à la ligne 15, collez le code suivant pour ajouter une propriété `RoomName` à la pièce :

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Ajoutez une relation**. Sous la propriété `RoomName` que vous venez d’ajouter, collez le code suivant pour permettre à ce type de jumeau de former des relations *contains* avec d’autres jumeaux :

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Quand vous avez terminé, le modèle mis à jour doit ressembler à ceci :

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

N’oubliez pas d’enregistrer le fichier avant de continuer.