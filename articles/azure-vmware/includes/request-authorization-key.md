---
title: Demander une clé d’autorisation pour ExpressRoute
description: Procédure à suivre afin de demander une clé d’autorisation pour ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026971"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Dans le portail Azure, sous la section **Connectivité**, sous l’onglet **ExpressRoute**, sélectionnez **+ Demander une clé d’autorisation**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Capture d’écran montrant comment demander une clé d’autorisation ExpressRoute" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Spécifiez un nom pour la clé, puis sélectionnez **Créer**. 
      
   La création de la clé peut prendre environ 30 secondes. La clé créée apparaît dans la liste des clés d’autorisation pour le cloud privé.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Capture d’écran montrant la clé d’autorisation ExpressRoute Global Reach":::
  
1. Copiez la clé d’autorisation et l’ID ExpressRoute. Vous les utiliserez pour effectuer le peering.  

   > [!NOTE]
   > La clé d’autorisation disparaît après un certain temps. Copiez-la dès qu’elle apparaît.