---
title: 'Tutoriel : Créer un segment réseau NSX-T dans AVS (Azure VMware Solution)'
description: Dans ce tutoriel, vous allez créer les segments réseau NSX-T utilisés pour les machines virtuelles dans vCenter.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 5654fbb6a063d4dfeb541c20407f9a09dff1509f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093585"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution-avs"></a>Tutoriel : Créer un segment réseau NSX-T dans AVS (Azure VMware Solution)

Les segments réseau créés dans NSX-T Manager sont utilisés comme réseaux pour les machines virtuelles dans vCenter. Les machines virtuelles créées dans vCenter sont placées sur les segments réseau créés dans NSX-T et sont visibles dans vCenter.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Naviguer dans NSX-T Manager pour ajouter des segments réseau
> * Ajouter un nouveau segment réseau
> * Observer le nouveau segment réseau dans vCenter

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer d’un cloud privé AVS avec accès aux interfaces de gestion vCenter et NSX-T Manager. Pour obtenir la procédure pas à pas de cet exemple de code d’application, consultez le [Tutoriel : Configurer le réseau pour votre cloud privé VMware dans Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Provisionner un segment réseau dans NSX-T

1. Dans vCenter, pour votre cloud privé, sélectionnez **SDDC-Datacenter > Networks**. Vous remarquez qu’il n’y a pas encore de réseau.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Sélectionner SDDC-Datacenter > Networks":::

1. Dans NSX-T Manager, pour votre cloud privé, sélectionnez **Networking**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Sélectionnez Networking pour accéder à la vue Network Overview dans NSX-T Manager.":::

1. Sélectionnez **Segments**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Sélectionnez Segments dans la page Network Overview.":::

1. Dans la page de vue d’ensemble des segments NSX-T, sélectionnez **ADD SEGMENT**. Trois segments sont créés dans le cadre du provisionnement du cloud privé. Ils ne peuvent pas être utilisés pour les machines virtuelles.  Vous devez ajouter un nouveau segment réseau pour cela.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Sélectionnez Add Segment dans la page de vue d’ensemble des segments réseau.":::

1. Nommez le segment, choisissez la passerelle de niveau 1 préconfigurée TNTxx-T1 comme passerelle connectée sous **Connected Gateway**, conservez Flexible sous **Type**, choisissez la zone de transport de superposition préconfigurée TNTxx-OVERLAY-TZ sous **Transport Zone**, puis sélectionnez Set Subnets. Vous pouvez conserver toutes les autres valeurs de paramètre par défaut de cette configuration dans cette section et les sections **PORTS** et **SEGMENT PROFILES**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Définissez les paramètres Segment Name, Connected Gateway and Type et Transport Zone, puis sélectionnez « Set Subnets ».":::

1. Définissez l’adresse IP de la passerelle pour le nouveau segment, puis sélectionnez **ADD**. L’adresse IP que vous utilisez doit se trouver dans un bloc d’adresses RFC1918 sans chevauchement. Vous vous assurerez ainsi que vous pouvez vous connecter aux machines virtuelles sur le nouveau segment.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Spécifiez l’adresse IP de la passerelle du segment en notation CIDR et sélectionnez ADD.":::

1. Appliquez le nouveau segment réseau en sélectionnant **APPLY**, puis enregistrez la configuration en sélectionnant **SAVE**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Appliquez le nouveau segment réseau à la configuration NSX-T en sélectionnant APPLY.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Enregistrez le nouveau segment réseau de la configuration NSX-T en sélectionnant SAVE.":::

1. Le segment réseau a été créé. Un message vous invite à poursuivre la configuration du segment. Vous allez refuser cette option en sélectionnant **NO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Refusez de poursuivre la configuration du segment réseau nouvellement créé en sélectionnant NO.":::

1. Vérifiez que le nouveau segment réseau est présent dans NSX-T en sélectionnant **Networking > Segments** et en vous assurant qu’il figure dans la liste (dans le cas présent, « ls01 »).

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Vérifiez que le nouveau segment réseau est présent dans NSX-T.":::

1. Vérifiez que le nouveau segment réseau est présent dans vCenter en sélectionnant **Networking > SDDC-Datacenter** et en vous assurant qu’il figure dans la liste (dans le cas présent, « ls01 »).

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Vérifiez que le nouveau segment réseau est présent dans vCenter.":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé les segments réseau NSX-T utilisés pour les machines virtuelles dans vCenter. Vous pouvez maintenant suivre le [Tutoriel : Créer une bibliothèque de contenu pour déployer des machines virtuelles dans Azure VMware Solution (AVS)](tutorial-deploy-vm-content-library.md) pour créer une bibliothèque de contenu dans vCenter et provisionner une machine virtuelle sur le réseau que vous avez créé dans ce tutoriel.

<!-- LINKS - external-->

<!-- LINKS - internal -->
