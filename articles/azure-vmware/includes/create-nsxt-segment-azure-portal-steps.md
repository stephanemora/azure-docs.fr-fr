---
title: Ajouter un segment réseau NSX-T à l’aide d’Azure VMware Solution
description: Étapes à suivre pour ajouter un segment réseau NSX-T pour Azure VMware Solution dans le portail Azure.
ms.topic: include
ms.date: 07/16/2021
ms.openlocfilehash: 7744884da7e1e6d46428bb00b52f88772c866760
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229305"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->


>[!NOTE]
>Si vous envisagez d’utiliser le protocole DHCP, vous devez [configurer un serveur DHCP ou un relais DHCP](../configure-dhcp-azure-vmware-solution.md) avant de pouvoir configurer un segment réseau NSX-T. 

1. Dans votre cloud privé Azure VMware Solution, sous **Réseau des charges de travail**, sélectionnez **segments** > **Ajouter**. 

2. Fournissez les détails du nouveau segment logique, puis sélectionnez **OK**.

   :::image type="content" source="../media/networking/add-new-nsxt-segment.png" alt-text="Capture d’écran montrant comment ajouter un nouveau segment NSX-T dans le portail Azure.":::

   - **Nom du segment** : nom du segment qui est visible dans vCenter.

   - **Passerelle de sous-réseau** : adresse IP de la passerelle pour le sous-réseau du segment avec un masque de sous-réseau. Les machines virtuelles sont attachées à un segment, et toutes les machines virtuelles qui se connectent à ce segment appartiennent au même sous-réseau.  De plus, toutes les machines virtuelles attachées à ce segment logique doivent contenir une adresse IP du même segment.

   - **DHCP** (facultatif) : plages DHCP pour un segment logique. Un [serveur DHCP ou relais DHCP](../configure-dhcp-azure-vmware-solution.md) doit être configuré pour consommer DHCP sur des segments.  

   >[!NOTE]
   >La **passerelle connectée** est sélectionnée par défaut et est en lecture seule.  Elle indique la passerelle de niveau 1 et des informations relatives au type de segment. 
   >
   >- **T1** : nom de la passerelle de niveau 1 dans NSX-T Manager. Un cloud privé est livré avec une passerelle NSX-T de niveau 0 en mode Actif/Actif et une passerelle NSX-T de niveau 1 par défaut en mode Actif/En attente.  Les segments créés par le biais de la console Azure VMware Solution se connectent uniquement à la passerelle de niveau 1 par défaut, et les charges de travail de ces segments obtiennent une connectivité Est-Ouest et Nord-Sud. Vous pouvez uniquement créer plus de passerelles de niveau 1 par le biais de NSX-T Manager. Les passerelles de niveau 1 créées à partir de la console NSX-T Manager ne sont pas visibles dans la console Azure VMware Solution. 
   >
   >- **Type** : segment de superposition pris en charge par Azure VMware Solution.

Le segment est désormais visible dans Azure VMware Solution, NSX-T Manager et vCenter.