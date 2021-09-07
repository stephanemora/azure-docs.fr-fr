---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 7164a0d9785a9cac50a7fa4535a4229a2c1731c0
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515177"
---
1. Localisez l’instance Virtual WAN que vous avez créée. Dans la page Virtual WAN, sous la section **Connectivité**, sélectionnez **Hubs**.
1. Dans la page **Hubs**, cliquez sur **+Nouveau hub** pour ouvrir la page **Créer un hub virtuel**.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="Capture d’écran montrant le volet Créer un hub virtuel avec l’onglet Informations de base sélectionné.":::

1. Dans la page **Créer un hub virtuel**, sous l’onglet **Fonctions de base**, renseignez les champs suivants :

   * **Région** : ce paramètre faisait référence à l’emplacement. Il s’agit de la région dans laquelle vous souhaitez créer votre hub virtuel.
   * **Nom** : nom sous lequel vous souhaitez que le hub virtuel soit connu.
   * **Espace d’adressage privé du hub** : pour permettre la création d’un hub, l’espace d’adressage minimal est /24. Si vous utilisez un espace d’adressage compris entre /25 et /32, vous obtiendrez une erreur lors de la création. Vous n’avez pas besoin de planifier explicitement l’espace d’adressage du sous-réseau pour les services du hub virtuel. Étant donné que le service Azure Virtual WAN est un service managé, il crée les sous-réseaux appropriés dans le hub virtuel pour les divers services ou passerelles (par exemple, les passerelles VPN, les passerelles ExpressRoute, les passerelles VPN utilisateur/point à site, le pare-feu, le routage, etc.).
