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
ms.openlocfilehash: 009b82188c52df6728aeef6c414c5f9ef0507ccc
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362249"
---
1. Dans la page **Créer un hub virtuel**, cliquez sur **Site à site** pour ouvrir l’onglet **Site à site**.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Capture d’écran montrant le volet Créer un hub virtuel avec l’option Site à site sélectionnée.":::

1. Sous l’onglet **Site à site**, complétez les champs suivants :

   * Sélectionnez **Oui** pour créer un VPN de site à site.
   * Le champ Numéro AS ne peut pas être modifié.
   * Dans la liste déroulante, sélectionnez la valeur **Unités d’échelle de la passerelle**. L’unité d’échelle vous permet de choisir le débit d’agrégats de la passerelle VPN en cours de création dans le hub virtuel auquel connecter les sites. Si vous choisissez 1 unité d’échelle de 500 Mbits/s, cela implique que deux instances sont créées pour la redondance, chacune d’un débit maximal de 500 Mbits/s. Par exemple, si vous avez cinq branches offrant chacune un débit de 10 Mbits/s, vous avez besoin d’un agrégat de 50 Mbits/s à l’extrémité de la tête. La planification de la capacité agrégée de la passerelle VPN Azure doit être effectuée après évaluation de la capacité nécessaire pour prendre en charge le nombre de branches conduisant au hub.
   * Sélectionnez la **préférence de routage** appropriée. Une préférence de routage Azure vous permet de choisir le mode d’acheminement de votre trafic entre Azure et Internet. Vous pouvez choisir de router le trafic via le réseau Microsoft ou le réseau du fournisseur de services Internet (Internet public). Ces options sont également appelées respectivement routage de patate froide et routage de patate chaude. L’IP publique dans Virtual WAN est attribuée par le service en fonction de l’option de routage sélectionnée. Pour plus d’informations sur la préférence de routage par le biais du réseau Microsoft ou de l’ISP, consultez l’article [Préférence de routage](../articles/virtual-network/routing-preference-overview.md).
1. Sélectionnez **Vérifier + créer** pour valider.
1. Sélectionnez **Créer** pour créer le hub. La création d’une passerelle VPN peut prendre jusqu’à 30 minutes. Après 30 minutes, **actualisez** pour afficher le hub dans la page **Hubs**. Sélectionnez **Accéder à la ressource** pour accéder à ressource.