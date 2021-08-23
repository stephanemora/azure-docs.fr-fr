---
title: Demander une clé d’autorisation pour ExpressRoute
description: Procédure à suivre afin de demander une clé d’autorisation pour ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 5f4c436a8559a4b579a3a0c9bbfbe79bcb50764e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729137"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Dans le portail Azure, accédez au cloud privé d’Azure VMware Solution. Sélectionnez **Gérer** > **Connectivité** > **ExpressRoute**, puis sélectionnez **+ Demander une clé d’autorisation**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Capture d’écran montrant comment demander une clé d’autorisation ExpressRoute" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Spécifiez un nom pour la clé, puis sélectionnez **Créer**.

   La création de la clé peut prendre environ 30 secondes. La clé créée apparaît dans la liste des clés d’autorisation pour le cloud privé.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Capture d’écran montrant la clé d’autorisation ExpressRoute Global Reach" lightbox="../media/expressroute-global-reach/show-global-reach-auth-key.png":::
  
1. Copiez la clé d’autorisation et l’ID ExpressRoute. Vous en avez besoin pour effectuer le peering. La clé d’autorisation disparaît après un certain temps. Copiez-la dès qu’elle apparaît.

