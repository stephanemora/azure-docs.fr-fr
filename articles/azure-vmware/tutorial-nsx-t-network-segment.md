---
title: Tutoriel - Créer un segment réseau NSX-T dans Azure VMware Solution
description: Apprendre à créer les segments de réseau NSX-T utilisés pour les machines virtuelles dans vCenter
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 780cac15efc043b9ae44b77af1234adca3fec5a1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254531"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Tutoriel : Créer un segment réseau NSX-T dans Azure VMware Solution

Les segments réseau créés dans NSX-T Manager sont utilisés comme réseaux pour les machines virtuelles dans vCenter. Les machines virtuelles créées dans vCenter sont placées sur les segments réseau créés dans NSX-T et sont visibles dans vCenter.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Naviguer dans NSX-T Manager pour ajouter des segments réseau
> * Ajouter un nouveau segment réseau
> * Observer le nouveau segment réseau dans vCenter

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer d’un cloud privé Azure VMware Solution avec accès aux interfaces de gestion vCenter et NSX-T Manager. Pour plus d’informations, consultez le tutoriel [Configurer le réseau pour votre cloud privé VMware dans Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Provisionner un segment réseau dans NSX-T

1. Dans vCenter, pour votre cloud privé, sélectionnez **SDDC-Datacenter > Networks**. Vous remarquez qu’il n’y a pas encore de réseau.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Dans NSX-T Manager, pour votre cloud privé, sélectionnez **Networking**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Sélectionnez **Segments**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Dans la page de vue d’ensemble des segments NSX-T, sélectionnez **ADD SEGMENT**. Trois segments sont créés dans le cadre du provisionnement du cloud privé. Ils ne peuvent pas être utilisés pour les machines virtuelles.  Vous devez ajouter un nouveau segment réseau pour cela.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Nommez le segment, choisissez la passerelle de niveau 1 préconfigurée TNTxx-T1 comme passerelle connectée sous **Connected Gateway**, conservez Flexible sous **Type**, choisissez la zone de transport de superposition préconfigurée TNTxx-OVERLAY-TZ sous **Transport Zone**, puis sélectionnez Set Subnets. Vous pouvez conserver toutes les autres valeurs de paramètre par défaut de cette configuration dans cette section et les sections **PORTS** et **SEGMENT PROFILES**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Définissez l’adresse IP de la passerelle pour le nouveau segment, puis sélectionnez **ADD**. L’adresse IP que vous utilisez doit se trouver dans un bloc d’adresses RFC1918 sans chevauchement. Vous vous assurerez ainsi que vous pouvez vous connecter aux machines virtuelles sur le nouveau segment.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Appliquez le nouveau segment réseau en sélectionnant **APPLY**, puis enregistrez la configuration en sélectionnant **SAVE**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Le segment réseau a été créé. Un message vous invite à poursuivre la configuration du segment. Vous allez refuser cette option en sélectionnant **NO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Vérifiez que le nouveau segment réseau est présent dans NSX-T en sélectionnant **Networking > Segments** et en vous assurant qu’il figure dans la liste (dans le cas présent, « ls01 »).

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

1. Vérifiez que le nouveau segment réseau est présent dans vCenter en sélectionnant **Networking > SDDC-Datacenter** et en vous assurant qu’il figure dans la liste (dans le cas présent, « ls01 »).

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Dans vCenter, pour votre cloud privé, sélectionnez SDDC-Datacenter > Networks. Vous remarquez qu’il n’y a pas encore de réseau.":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé les segments réseau NSX-T utilisés pour les machines virtuelles dans vCenter. Vous pouvez désormais [créer une bibliothèque de contenu pour déployer des machines virtuelles dans Azure VMware Solution](deploy-vm-content-library.md) et provisionner une machine virtuelle sur le réseau que vous avez créé dans ce tutoriel.

Sinon, passez au prochain tutoriel pour apprendre à créer un peering ExpressRoute Global Reach avec un cloud privé dans une solution Azure VMware Solution.

> [!div class="nextstepaction"]
> [Appairer des environnements locaux avec un cloud privé](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
