---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8864e8025c77fb0b4b6efc694d9f4938e9ed8ae7
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108737484"
---
1. Dans le panneau Paramètres de la ressource, du groupe de ressources ou de l’abonnement que vous voulez verrouiller, sélectionnez **Verrous**.

    :::image type="content" source="media/resource-manager-lock-resources/select-lock.png" alt-text="Sélectionnez le verrou.":::

1. Pour ajouter un verrou, sélectionnez **Ajouter**. Si vous souhaitez créer un verrou au niveau du parent, sélectionnez le parent. La ressource actuellement sélectionnée hérite du verrou du parent. Vous pouvez par exemple verrouiller le groupe de ressources afin d’appliquer un verrou à toutes ses ressources.

    :::image type="content" source="media/resource-manager-lock-resources/add-lock.png" alt-text="Ajoutez un verrou.":::

1. Choisissez un nom et un niveau de verrouillage pour le verrou. Si vous le souhaitez, vous pouvez ajouter des notes décrivant le verrou.

    :::image type="content" source="media/resource-manager-lock-resources/set-lock.png" alt-text="Définissez le verrou.":::

1. Pour supprimer le verrou, sélectionnez le bouton **Supprimer**.

    :::image type="content" source="media/resource-manager-lock-resources/delete-lock.png" alt-text="Supprimez le verrou.":::
