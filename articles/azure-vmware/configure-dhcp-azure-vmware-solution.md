---
title: Configurer le protocole DHCP pour Azure VMware Solution
description: Découvrez comment configurer DHCP à l’aide de NSX-T Manager pour héberger un serveur DHCP ou utiliser un serveur DHCP externe tiers.
ms.topic: how-to
ms.custom: contperf-fy21q2, contperf-fy22q1
ms.date: 09/13/2021
ms.openlocfilehash: 10234147303ab9959fa1a907b0c558be9e3fe0f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600953"
---
# <a name="configure-dhcp-for-azure-vmware-solution"></a>Configurer le protocole DHCP pour Azure VMware Solution

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]

Dans cet article de procédure, vous allez utiliser NSX-T Manager pour configurer DHCP pour Azure VMware Solution de l’une des manières suivantes : 


- [Utiliser la Portail Azure pour créer un serveur DHCP ou un relais](#use-the-azure-portal-to-create-a-dhcp-server-or-relay)

- [Utiliser NSX-T pour héberger votre serveur DHCP](#use-nsx-t-to-host-your-dhcp-server)

- [Utiliser un serveur DHCP externe tiers](#use-a-third-party-external-dhcp-server)

>[!TIP]
>Si vous souhaitez configurer le protocole DHCP à l’aide d’une vue simplifiée des opérations NSX-T, consultez [Configurer DHCP pour Azure VMware Solution](configure-dhcp-azure-vmware-solution.md).


>[!IMPORTANT]
>Pour les clouds créés le 1er juillet 2021 ou après, l’affichage simplifié des opérations NSX-T doit être utilisé pour configurer DHCP sur la passerelle par défaut de niveau 1 dans votre environnement.
>
>DHCP ne fonctionne pas pour les machines virtuelles sur le réseau Stretch HCX L2 VMware lorsque le serveur DHCP se trouve dans le centre de données local.  Par défaut, le service NSX empêche toutes les requêtes DHCP de traverser le réseau Stretch L2. Pour obtenir la solution, consultez la procédure [Configurer DHCP sur des réseaux VMware HCX étendus L2](configure-l2-stretched-vmware-hcx-networks.md).

## <a name="use-the-azure-portal-to-create-a-dhcp-server-or-relay"></a>Utiliser la Portail Azure pour créer un serveur DHCP ou un relais

Vous pouvez créer un serveur ou un relais DHCP directement dans Azure VMware Solution sur le Portail Azure. Le serveur ou relais DHCP se connecte à la passerelle de niveau 1 qui a été créée lors du déploiement d’Azure VMware Solution. Tous les segments pour lesquels vous avez donné des plages DHCP feront partie de ce dispositif DHCP. Une fois que vous avez créé un serveur DHCP ou un relais DHCP, vous devez définir un sous-réseau ou une plage au niveau du segment pour le consommer.

1. Dans votre cloud privé Azure VMware Solution, sous **Réseau des charges de travail**, sélectionnez **DHCP** > **Ajouter**.

2. Sélectionnez **Serveur DHCP** ou **relais DHCP**, puis indiquez un nom pour le serveur ou le relais et trois adresses IP. 

   >[!NOTE]
   >Pour le relais DHCP, une seule adresse IP est nécessaire à la configuration.

   :::image type="content" source="media/networking/add-dhcp-server-relay.png" alt-text="Capture d’écran montrant comment ajouter un serveur DHCP ou un relais DHCP dans Azure VMware Solution":::

4. Terminez la configuration DHCP en [fournissant des plages DHCP sur les segments logiques](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment), puis sélectionnez **OK**.



## <a name="use-nsx-t-to-host-your-dhcp-server"></a>Utiliser NSX-T pour héberger votre serveur DHCP
Si vous envisagez d’utiliser NSX-T pour héberger votre serveur DHCP, créez un serveur DHCP et un service de relais. Ensuite, vous ajoutez un segment réseau et spécifiez la plage d’adresses IP DHCP.

### <a name="create-a-dhcp-server"></a>Créer un serveur DHCP

1. Dans NSX-T Manager, sélectionnez **Mise en réseau** > **DHCP**, puis sélectionnez **Ajouter un serveur**.

1. Sélectionnez le **DHCP** pour le **Type de serveur**, indiquez le nom du serveur et l’adresse IP, puis sélectionnez **Enregistrer**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Capture d’écran montrant comment ajouter un serveur DHCP dans NSX-T Manager." border="true":::

1. Sélectionnez **Passerelles de niveau 1**, sélectionnez les trois points suspendus sur la passerelle de niveau 1, puis sélectionnez **Modifier**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Capture d’écran montrant comment modifier la passerelle de niveau 1 pour l’utilisation d’un serveur DHCP." border="true":::

1. Sélectionnez **Aucune allocation d’adresse IP définie** pour ajouter un sous-réseau.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Capture d’écran montrant comment ajouter un sous-réseau la passerelle de niveau 1 pour l’utilisation d’un serveur DHCP." border="true":::

1. Pour **Type**, sélectionnez **Serveur local DHCP**. 
   
1. Pour **Serveur DHCP**, sélectionnez **DHCP par défaut**, puis sélectionnez **Enregistrer**.

1. Sélectionnez de nouveau **Enregistrer**, puis sélectionnez **Fermer l’édition**.

### <a name="add-a-network-segment"></a>Ajouter un segment réseau

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

### <a name="specify-the-dhcp-ip-address-range"></a>Spécifier la plage d’adresses IP DHCP
 
Lorsque vous créez un relais vers un serveur DHCP, vous ajoutez également la plage d’adresses IP DHCP.

>[!NOTE]
>La plage d’adresses IP ne doit pas chevaucher celle utilisée dans d’autres réseaux virtuels situés dans votre abonnement et des réseaux locaux.

1. Dans NSX-T Manager, sélectionnez **Mise en réseau** > **Segments**. 
   
1. Sélectionnez les trois points suspendus sur le nom du segment, puis **Modifier**.
   
1. Sélectionnez **Définir des sous-réseaux** pour spécifier l’adresse IP DHCP du sous-réseau. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Capture d’écran montrant comment définir les sous-réseaux pour spécifier l’adresse IP DHCP pour l’utilisation d’un serveur DHCP." border="true":::
      
1. Modifiez l’adresse IP de la passerelle si nécessaire, puis entrez l’adresse IP de la plage DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Capture d’écran montrant l’adresse IP de la passerelle et les plages DHCP pour l’utilisation d’un serveur DHCP." border="true":::
      
1. Sélectionnez **Appliquer**, puis **Enregistrer**. Le segment est affecté à un pool de serveurs DHCP.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Capture d’écran montrant que le pool de serveurs DHCP est attribué au segment pour l’utilisation d’un serveur DHCP." border="true":::


## <a name="use-a-third-party-external-dhcp-server"></a>Utiliser un serveur DHCP externe tiers

Si vous voulez utiliser un serveur DHCP externe tiers, vous devez créer un service de relais DHCP dans NSX-T Manager. Vous spécifiez aussi la plage d’adresses IP DHCP.


>[!IMPORTANT]
>Pour les clouds créés le 1er juillet 2021 ou après, l’affichage simplifié des opérations NSX-T doit être utilisé pour configurer DHCP sur la passerelle par défaut de niveau 1 dans votre environnement.


### <a name="create-dhcp-relay-service"></a>Créer un service de relais DHCP

Utilisez un relais DHCP pour tout service DHCP non-NSX. Par exemple, une machine virtuelle exécutant DHCP dans Azure VMware Solution, Azure IaaS ou en local.

1. Dans NSX-T Manager, sélectionnez **Mise en réseau** > **DHCP**, puis sélectionnez **Ajouter un serveur**.

1. Sélectionnez **Relais DHCP** pour le **Type serveur**, indiquez le nom du serveur et l’adresse IP, puis sélectionnez **Enregistrer**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Capture d’écran montrant comment créer un service de relais DHCP dans NSX-T Manager." border="true":::

1. Sélectionnez **Passerelles de niveau 1**, sélectionnez les trois points suspendus sur la passerelle de niveau 1, puis sélectionnez **Modifier**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Capture d’écran montrant comment modifier la passerelle de niveau 1." border="true":::

1. Sélectionnez **Aucune allocation IP définie** pour définir l’allocation d’adresse IP.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Capture d’écran montrant comment ajouter un sous-réseau de passerelle de niveau 1." border="true":::

1. Pour **Type**, sélectionnez **Serveur DHCP**. 
   
1. Pour **Serveur DHCP**, sélectionnez **Relais DHCP**, puis sélectionnez **Enregistrer**.

1. Sélectionnez de nouveau **Enregistrer**, puis sélectionnez **Fermer l’édition**.


### <a name="specify-the-dhcp-ip-address-range"></a>Spécifier la plage d’adresses IP DHCP

Lorsque vous créez un relais vers un serveur DHCP, vous ajoutez également la plage d’adresses IP DHCP.

>[!NOTE]
>La plage d’adresses IP ne doit pas chevaucher celle utilisée dans d’autres réseaux virtuels situés dans votre abonnement et des réseaux locaux.

1. Dans NSX-T Manager, sélectionnez **Mise en réseau** > **Segments**. 
   
1. Sélectionnez les trois points suspendus sur le nom du segment, puis **Modifier**.
   
1. Sélectionnez **Définir des sous-réseaux** pour spécifier l’adresse IP DHCP du sous-réseau. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Capture d’écran montrant comment définir les sous-réseaux pour spécifier l’adresse IP DHCP." border="true":::
      
1. Modifiez l’adresse IP de la passerelle si nécessaire, puis entrez l’adresse IP de la plage DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Capture d’écran montrant l’adresse IP de la passerelle et les plages DHCP." border="true":::
      
1. Sélectionnez **Appliquer**, puis **Enregistrer**. Le segment est affecté à un pool de serveurs DHCP.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Capture d’écran montrant que le pool de serveurs DHCP est attribué au segment." border="true":::


## <a name="next-steps"></a>Étapes suivantes

Si vous voulez envoyer des requêtes DHCP à partir de vos machines virtuelles Azure VMware Solution vers un serveur DHCP non-NSX-T, consultez la procédure [Configurer DHCP sur des réseaux HCX VMware étirés L2](configure-l2-stretched-vmware-hcx-networks.md).
