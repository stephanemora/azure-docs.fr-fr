---
title: Fichier Include
description: inclure fichier
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1abf76ee8e6c44eaa61d9de38b163f230327edd
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362307"
---
1. Sur la page de votre réseau étendu virtuel, dans le volet gauche, sélectionnez **Hubs**. Sur la page **Hubs**, sélectionnez **+ Nouveau hub**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="Capture d’écran du nouveau hub.":::

1. Dans la page **Créer un hub virtuel**, renseignez les champs suivants :

   * **Région** : sélectionnez la région dans laquelle vous souhaitez déployer le hub virtuel.
   * **Nom** : entrez le nom que vous souhaitez donner à votre hub virtuel.
   * **Espace d’adressage privé du hub** : plage d’adresses du hub en notation CIDR.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="Capture d’écran de la page Créer un hub virtuel.":::

1. Sous l’onglet **Point à site**, complétez les champs suivants :

   * **Unités d’échelle de la passerelle**, qui représentent la capacité d’agrégation de la passerelle VPN utilisateur.
   * **Configuration point à site**, que vous avez créée à l’étape précédente.
   * **Pool d’adresses des clients**, pour les utilisateurs distants.
   * **Adresse IP de serveur DNS personnalisé**.
   * **Préférence de routage** : Sélectionnez la préférence de routage appropriée. Une préférence de routage Azure vous permet de choisir le mode d’acheminement de votre trafic entre Azure et Internet. Vous pouvez choisir de router le trafic via le réseau Microsoft ou le réseau du fournisseur de services Internet (Internet public). Ces options sont également appelées respectivement routage de patate froide et routage de patate chaude. L’IP publique dans Virtual WAN est attribuée par le service en fonction de l’option de routage sélectionnée. Pour plus d’informations sur la préférence de routage par le biais du réseau Microsoft ou de l’ISP, consultez l’article [Préférence de routage](../articles/virtual-network/routing-preference-overview.md).

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="Capture d’écran de la configuration du hub virtuel avec sélection de l’option Point à site.":::

1. Sélectionnez **Revoir + créer**.
1. Dans la page **Validation réussie**, sélectionnez **Créer**.