---
title: 'Tutoriel : Ajouter un segment réseau dans Azure VMware Solution'
description: Découvrez comment ajouter un segment réseau utilisable par des machines virtuelles dans vCenter.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 04681c5d55536e3ee1cc0b12d2ebc94f19b3873f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464427"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Tutoriel : Ajouter un segment réseau dans Azure VMware Solution 

Après le déploiement d’Azure VMware Solution, vous pouvez configurer un segment réseau NSX-T à partir de NSX-T Manager ou du portail Azure.  Une fois configurés, les segments sont visibles dans Azure VMware Solution, NSX-T Manager et vCenter.  NSX-T est préapprovisionné par défaut avec une passerelle NSX-T de niveau 0 en mode **Actif/Actif** et une passerelle NSX-T de niveau 1 par défaut en mode **Actif/En attente**.  Ces passerelles vous permettent de connecter les segments (commutateurs logiques) et de fournir une connectivité Est-Ouest et Nord-Sud. 

>[!TIP]
>Le portail Azure présente un affichage simplifié des opérations NSX-T dont un administrateur VMware a régulièrement besoin. Cet affichage s’adresse aux utilisateurs qui ne connaissent pas NSX-T Manager. 


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Ajouter des segments réseau à l’aide de NSX-T Manager ou du portail Azure
> * Vérifier le nouveau segment réseau 

## <a name="prerequisites"></a>Prérequis

Un cloud privé Azure VMware Solution avec accès aux interfaces de gestion vCenter et NSX-T Manager. Pour plus d’informations, consultez le tutoriel [Configurer la mise en réseau](tutorial-configure-networking.md).

## <a name="use-nsx-t-manager-to-add-network-segment"></a>Utiliser NSX-T Manager pour ajouter un segment réseau 

Les machines virtuelles créées dans vCenter sont placées sur les segments réseau créés dans NSX-T, et sont visibles dans vCenter.

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="use-azure-portal-to-add-an-nsx-t-segment"></a>Utiliser Portail Azure pour ajouter un segment NSX-T

[!INCLUDE [create-nsxt-segment-azure-portal-steps](includes/create-nsxt-segment-azure-portal-steps.md)]


## <a name="verify-the-new-network-segment"></a>Vérifier le nouveau segment réseau

Vérifiez la présence du nouveau segment réseau. Dans cet exemple, **ls01** est le nouveau segment réseau.

1. Dans NSX-T Manager, sélectionnez **Networking** (Réseaux)  > **Segments**. 

    :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Capture d’écran montrant la confirmation de la présence et l’état du nouveau segment réseau dans NSX-T.":::

1. Dans vCenter, sélectionnez **Networking** (Réseaux) > **SDDC-Datacenter**.

    :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Capture d’écran montrant la confirmation que le nouveau segment réseau est présent dans vCenter.":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un segment réseau NSX-T utilisable par des machines virtuelles dans vCenter. 

Vous pouvez désormais : 

- [Configurer et gérer le protocole DHCP pour Azure VMware Solution](configure-dhcp-azure-vmware-solution.md)
- [Créer une bibliothèque de contenu pour déployer des machines virtuelles dans Azure VMware Solution](deploy-vm-content-library.md) 
- [Appairer des environnements locaux avec un cloud privé](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
