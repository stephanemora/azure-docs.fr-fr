---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 1169909f0cae9b8bcee5939dd00f497c5e1ab2df
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515214"
---
1. Dans la page **Créer un hub virtuel**, cliquez sur **Site à site** pour ouvrir l’onglet **Site à site**.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/hub-site-to-site.png" alt-text="Capture d’écran montrant le volet Créer un hub virtuel avec l’option Site à site sélectionnée.":::

1. Sous l’onglet **Site à site**, complétez les champs suivants :

   * Sélectionnez **Oui** pour créer un VPN de site à site.
   * **Numéro de système autonome** : ce champ n’est pas modifiable.
   * **Unités d’échelle de la passerelle** : sélectionnez dans la liste déroulante la valeur appropriée pour **Unités d’échelle de la passerelle**. L’unité d’échelle vous permet de choisir le débit d’agrégats de la passerelle VPN en cours de création dans le hub virtuel auquel connecter les sites. 

     Si vous choisissez 1 unité d’échelle de 500 Mbits/s, cela implique que deux instances sont créées pour la redondance, chacune d’un débit maximal de 500 Mbits/s. Par exemple, si vous avez cinq branches offrant chacune un débit de 10 Mbits/s, vous avez besoin d’un agrégat de 50 Mbits/s à l’extrémité de la tête. La planification de la capacité agrégée de la passerelle VPN Azure doit être effectuée après évaluation de la capacité nécessaire pour prendre en charge le nombre de branches conduisant au hub.
   * **Préférence de routage** : la préférence de routage Azure vous permet de choisir la façon dont votre trafic est routé entre Azure et Internet. Vous pouvez choisir de router le trafic via le réseau Microsoft ou via le réseau des ISP (Internet public). Ces options sont également appelées respectivement routage de patate froide et routage de patate chaude. 

     L’adresse IP publique dans Virtual WAN est affectée par le service en fonction de l’option de routage sélectionnée. Pour plus d’informations sur la préférence de routage par le biais du réseau Microsoft ou de l’ISP, consultez l’article [Préférence de routage](../articles/virtual-network/routing-preference-overview.md).
1. Sélectionnez **Vérifier + créer** pour valider.
1. Sélectionnez **Créer** pour créer le hub et la passerelle. Cette opération peut prendre jusqu’à 30 minutes. Après 30 minutes, **actualisez** pour afficher le hub dans la page **Hubs**. Sélectionnez **Accéder à la ressource** pour accéder à ressource.