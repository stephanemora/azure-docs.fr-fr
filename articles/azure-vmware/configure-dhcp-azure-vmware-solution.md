---
title: Configurer et gérer le protocole DHCP dans Azure VMware Solution
description: Découvrez comment créer et gérer le protocole DHCP pour votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 05/17/2021
ms.openlocfilehash: 28fb995cbc91d6e350e8d295c1f57d62c706e59e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104050"
---
# <a name="configure-and-manage-dhcp-in-azure-vmware-solution"></a>Configurer et gérer le protocole DHCP dans Azure VMware Solution

Les applications et les charges de travail exécutées dans un environnement de cloud privé nécessitent des services DHCP pour les affectations d’adresses IP.  Cet article explique les procédures de création et de gestion du protocole DHCP dans Azure VMware Solution de deux manières :

- Si vous utilisez NSX-T pour héberger votre serveur DHCP, vous devez [créer un serveur DHCP](#create-a-dhcp-server) et [relayer vers ce serveur](#create-dhcp-relay-service). Lorsque vous créez le serveur DHCP, vous ajoutez également un segment réseau et spécifiez la plage d’adresses IP DHCP.   

- Si vous utilisez un serveur DHCP externe tiers sur votre réseau, vous devez [créer un service de relais DHCP](#create-dhcp-relay-service). Lorsque vous créez un relais vers un serveur DHCP, que vous utilisiez NSX-T ou un tiers pour héberger votre serveur DHCP, vous devez spécifier la plage d’adresses IP DHCP.

>[!IMPORTANT]
>DHCP ne fonctionne pas pour les machines virtuelles sur le réseau Stretch HCX L2 VMware lorsque le serveur DHCP se trouve dans le centre de données local.  Par défaut, le service NSX empêche toutes les requêtes DHCP de traverser le réseau Stretch L2. Pour obtenir la solution, consultez la procédure [Configurer DHCP sur des réseaux VMware HCX étendus L2](configure-l2-stretched-vmware-hcx-networks.md).


## <a name="create-a-dhcp-server"></a>Créer un serveur DHCP

Si vous envisagez d’utiliser NSX-T pour héberger votre serveur DHCP, créez un serveur DHCP. Ensuite, vous ajoutez un segment réseau et spécifiez la plage d’adresses IP DHCP.

1. Dans NSX-T Manager, sélectionnez **Mise en réseau** > **DHCP**, puis sélectionnez **Ajouter un serveur**.

1. Sélectionnez le **Type de serveur** **DHCP**, indiquez le nom du serveur et l’adresse IP, puis sélectionnez **Enregistrer**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Ajouter un serveur DHCP" border="true":::

1. Sélectionnez **Passerelles de niveau 1**, sélectionnez les trois points suspendus sur la passerelle de niveau 1, puis sélectionnez **Modifier**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Sélectionner la passerelle à utiliser" border="true":::

1. Sélectionnez **Aucune allocation d’adresse IP définie** pour ajouter un sous-réseau.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Ajouter un sous-réseau" border="true":::

1. Pour **Type**, sélectionnez **Serveur local DHCP**. 
   
1. Pour **Serveur DHCP**, sélectionnez **DHCP par défaut**, puis sélectionnez **Enregistrer**.

1. Sélectionnez de nouveau **Enregistrer**, puis sélectionnez **Fermer l’édition**.

### <a name="add-a-network-segment"></a>Ajouter un segment réseau

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>Créer un service de relais DHCP

Si vous voulez utiliser un serveur DHCP externe tiers sur votre réseau, vous devez créer un service de relais DHCP. Vous allez également spécifier la plage d’adresses IP DHCP dans NSX-T Manager. 

1. Dans NSX-T Manager, sélectionnez **Mise en réseau** > **DHCP**, puis sélectionnez **Ajouter un serveur**.

1. Sélectionnez le **Type de serveur** **Relais DHCP**, indiquez le nom du serveur et l’adresse IP, puis sélectionnez **Enregistrer**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="créer un service de relais DHCP" border="true":::

1. Sélectionnez **Passerelles de niveau 1**, sélectionnez les trois points suspendus sur la passerelle de niveau 1, puis sélectionnez **Modifier**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Modifier la passerelle de niveau 1" border="true":::

1. Sélectionnez **Aucune allocation IP définie** pour définir l’allocation d’adresse IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Modifier l’allocation d’adresse IP" border="true":::

1. Pour **Type**, sélectionnez **Serveur DHCP**. 
   
1. Pour **Serveur DHCP**, sélectionnez **Relais DHCP**, puis sélectionnez **Enregistrer**.

1. Sélectionnez de nouveau **Enregistrer**, puis sélectionnez **Fermer l’édition**.


## <a name="specify-the-dhcp-ip-address-range"></a>Spécifier la plage d’adresses IP DHCP

1. Dans NSX-T Manager, sélectionnez **Mise en réseau** > **Segments**. 
   
1. Sélectionnez les trois points suspendus sur le nom du segment, puis **Modifier**.
   
1. Sélectionnez **Définir des sous-réseaux** pour spécifier l’adresse IP DHCP du sous-réseau. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segments réseau" border="true":::
      
1. Modifiez l’adresse IP de la passerelle si nécessaire, puis entrez l’adresse IP de la plage DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Modifier des sous-réseaux" border="true":::
      
1. Sélectionnez **Appliquer**, puis **Enregistrer**. Le segment est affecté à un pool de serveurs DHCP.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Pool de serveurs DHCP affecté à un segment" border="true":::



## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez envoyer des requêtes DHCP de vos machines virtuelles Azure VMware Solution à un serveur DHCP non NSX-T, créez un nouveau profil de segment de sécurité. Pour obtenir la solution, consultez la procédure [Configurer DHCP sur des réseaux VMware HCX étendus L2](configure-l2-stretched-vmware-hcx-networks.md).

