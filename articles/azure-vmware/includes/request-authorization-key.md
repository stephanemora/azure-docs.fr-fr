---
title: Demander une clé d’autorisation pour ExpressRoute
description: Procédure à suivre afin de demander une clé d’autorisation pour ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 44ca81e25dda61ab32ea3455a1f228e134952582
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945559"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Dans le portail Azure, accédez au cloud privé d’Azure VMware Solution. Sélectionnez **Gérer** > **Connectivité** > **ExpressRoute**, puis sélectionnez **+ Demander une clé d’autorisation**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Capture d’écran montrant comment demander une clé d’autorisation ExpressRoute" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Spécifiez un nom pour la clé, puis sélectionnez **Créer**.

   La création de la clé peut prendre environ 30 secondes. La clé créée apparaît dans la liste des clés d’autorisation pour le cloud privé.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Capture d’écran montrant la clé d’autorisation ExpressRoute Global Reach":::
  
1. Copiez la clé d’autorisation et l’ID ExpressRoute. Vous en avez besoin pour effectuer le peering. La clé d’autorisation disparaît après un certain temps. Copiez-la dès qu’elle apparaît.

