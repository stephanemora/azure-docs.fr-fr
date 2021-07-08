---
title: Ajouter un segment réseau NSX-T
description: Étapes à suivre pour ajouter un segment réseau NSX-T pour Azure VMware Solution.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 10b04c8000b8ef440a2a729be671630b4382f3a8
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110794946"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->

1. Dans NSX-T Manager, sélectionnez **Networking** (Réseaux)  > **Segments**, puis sélectionnez **Add Segment** (Ajouter un segment). 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Capture d’écran montrant comment ajouter un nouveau segment":::

1. Entrez un nom pour le segment.

1. Sélectionnez la passerelle de niveau 1 (TNTxx-T1) comme valeur pour **Connected Gateway** (Passerelle connectée) et laissez Flexible comme **Type**.

1. Sélectionnez la zone de transport (**Transport Zone**) de superposition préconfigurée (TNTxx-OVERLAY-TZ), puis sélectionnez **Set Subnets** (Définir des sous-réseaux). 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Définissez les paramètres Segment Name, Connected Gateway and Type et Transport Zone, puis sélectionnez Set Subnet.":::

1. Entrez l’adresse IP de la passerelle, puis sélectionnez **Ajouter**. 

   >[!IMPORTANT]
   >L’adresse IP doit se trouver dans un bloc d’adresses RFC1918 sans chevauchement, ce qui garantit la connexion aux machines virtuelles sur le nouveau segment.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Définissez l’adresse IP de la passerelle pour le nouveau segment, puis sélectionnez ADD.":::

1. Sélectionnez **Apply** (Appliquer), puis **Save** (Enregistrer).

1. Sélectionnez **No** (Non) pour refuser l’option qui permet de continuer la configuration du segment. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Refusez de poursuivre la configuration du segment réseau nouvellement créé en sélectionnant NO.":::

1. Confirmez la présence du nouveau segment réseau. Dans cet exemple, **ls01** est le nouveau segment réseau.

   1. Dans NSX-T Manager, sélectionnez **Networking** (Réseaux)  > **Segments**. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Vérifiez que le nouveau segment réseau est présent dans NSX-T.":::

   1. Dans vCenter, sélectionnez **Networking (Réseaux) > SDDC-Datacenter**.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Vérifiez que le nouveau segment réseau est présent dans vCenter.":::