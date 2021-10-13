---
title: Demander une clé d’autorisation pour ExpressRoute
description: Procédure à suivre afin de demander une clé d’autorisation pour ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1ab25c1c22dd2697b9101d76602e81d22adc5b37
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638590"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Dans le portail Azure, accédez au cloud privé d’Azure VMware Solution. Sélectionnez **Gérer** > **Connectivité** > **ExpressRoute**, puis sélectionnez **+ Demander une clé d’autorisation**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Capture d’écran montrant comment demander une clé d’autorisation ExpressRoute" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Spécifiez un nom pour la clé, puis sélectionnez **Créer**.

   La création de la clé peut prendre environ 30 secondes. La clé créée apparaît dans la liste des clés d’autorisation pour le cloud privé.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Capture d’écran montrant la clé d’autorisation ExpressRoute Global Reach" lightbox="../media/expressroute-global-reach/show-global-reach-auth-key.png":::
  
1. Copiez la clé d’autorisation et l’ID ExpressRoute. Vous en avez besoin pour effectuer le peering. La clé d’autorisation disparaît après un certain temps. Copiez-la dès qu’elle apparaît.

