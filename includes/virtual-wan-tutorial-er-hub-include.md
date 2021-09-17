---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: f5d641a1d017f40081b311e9b9b610b7fcc7c13c
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820866"
---
1. Localisez l’instance Virtual WAN que vous avez créée. Dans la page Virtual WAN, sous la section **Connectivité**, sélectionnez **Hubs**. Cliquez sur **Nouveau hub** pour ouvrir la page **Créer un hub virtuel**.

   :::image type="content" source="media/virtual-wan-empty-hub/new-hub.jpg" alt-text="Capture d’écran affichant la boîte de dialogue Hubs configuration avec l’option New Hub sélectionnée.":::

1. Dans la page **Créer un hub virtuel**, renseignez les champs.

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/create-hub.png" alt-text="Capture d’écran présentant l’onglet Informations de base dans lequel vous pouvez entrer des valeurs.":::

   * **Région** : sélectionnez la région dans laquelle vous souhaitez déployer le hub virtuel.
   * **Nom** : nom sous lequel vous souhaitez que le hub virtuel soit connu.
   * **Espace d’adressage privé du hub** : plage d’adresses du hub en notation CIDR.

1. Sélectionnez l’**onglet ExpressRoute**. Cliquez sur **Oui** pour indiquer les paramètres et renseignez le champ. Pour plus d’informations sur les unités d’échelle de passerelle, consultez la [FAQ](../articles/virtual-wan/virtual-wan-faq.md#what-are-virtual-wan-gateway-scale-units).

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/expressroute.png" alt-text="Capture d’écran présentant l’onglet ExpressRoute dans lequel vous pouvez entrer des valeurs.":::

1. Sélectionnez **Vérifier + créer** pour valider.
1. Sélectionnez **Créer** pour créer le hub avec une passerelle ExpressRoute. L’exécution d’un hub peut prendre environ 30 minutes. Après 30 minutes, **actualisez** pour afficher le hub dans la page **Hubs**. Sélectionnez **Accéder à la ressource** pour accéder à ressource.