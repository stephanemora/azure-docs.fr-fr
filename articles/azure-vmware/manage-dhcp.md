---
title: Création et gestion de protocole DHCP
description: Cet article explique comment gérer le protocole DHCP dans Azure VMware Solution.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461054"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Comment créer et gérer le protocole DHCP dans Azure VMware Solution

NSX-T offre la possibilité de configurer le protocole DHCP pour votre cloud privé. Si vous utilisez NSX-T pour héberger votre serveur DHCP, consultez [Créer un serveur DHCP](#create-dhcp-server). Sinon, si vous disposez d’un serveur DHCP externe tiers sur votre réseau, consultez [Créer un service de relais DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Créer un serveur DHCP

Pour configurer un serveur DHCP sur NSX-T, procédez comme suit.

1. Dans le gestionnaire NSX, accédez à l’onglet **Mise en réseau** , puis sélectionnez **DHCP** . 
1. Sélectionnez **AJOUTER UN SERVEUR** puis fournissez le nom et l’adresse IP du serveur. 
1. Sélectionnez **Enregistrer** .

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Ajouter un serveur DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Connectez le serveur DHCP à la passerelle de niveau 1.

1. Choisissez **Passerelles de niveau 1** , sélectionnez la passerelle dans la liste, puis choisissez **Modifier** .

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Ajouter un serveur DHCP" border="true":::

1. Sélectionnez **Aucune allocation d’adresse IP définie** pour ajouter un sous-réseau.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Ajouter un serveur DHCP" border="true":::

1. Sélectionnez **Serveur local DHCP** dans **Type** . 
1. Sélectionnez **DHCP par défaut** pour **Serveur DHCP** , puis sélectionnez **Enregistrer** .


1. Dans la fenêtre **Passerelle de niveau 1** , sélectionnez **Enregistrer** . 
1. Sélectionnez **Fermer l’édition** pour terminer.

### <a name="add-a-network-segment"></a>Ajouter un segment réseau

Une fois que vous avez créé votre serveur DHCP, vous devez lui ajouter des segments réseau.

1. Dans NSX-T, sélectionnez l’onglet **Mise en réseau** , puis, sous **Connectivité** , sélectionnez **Segments** . 
1. Sélectionnez **AJOUTER UN SEGMENT** et nommez le segment et la connexion à la passerelle de niveau 1. 
1. Sélectionnez **Définir des sous-réseaux** pour configurer un nouveau sous-réseau. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Ajouter un serveur DHCP" border="true":::

1. Dans la fenêtre **Définir des sous-réseaux** , sélectionnez **AJOUTER UN SOUS-RÉSEAU** . 
1. Entrez l’adresse IP de la passerelle et la plage DHCP, puis sélectionnez **Ajouter** puis **APPLIQUER** .

1. Sélectionnez **Enregistrer** pour ajouter le nouveau segment de réseau.

## <a name="create-dhcp-relay-service"></a>Créer un service de relais DHCP

1. Sélectionnez l’onglet **Mise en réseau** puis, sous **Gestion des adresses IP** , sélectionnez **DHCP** . 
1. Sélectionnez **AJOUTER UN SERVEUR** . 
1. Sélectionnez Relais DHCP comme **Type de serveur** , puis entrez le nom du serveur et l’adresse IP du serveur relais. 
1. Sélectionnez **Enregistrer** .

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Ajouter un serveur DHCP" border="true":::

1. Sous **Connectivité** , sélectionnez **Passerelles de niveau 1** . 
1. Sélectionnez les trois points suspendus sur la passerelle de niveau 1, puis **Modifier** .

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Ajouter un serveur DHCP" border="true":::

1. Sélectionnez **Aucune allocation IP définie** pour définir l’allocation d’adresse IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Ajouter un serveur DHCP" border="true":::

1. Sélectionnez **Serveur relais DHCP** pour **Type** .
1. Sélectionnez votre serveur de relais DHCP pour **Relais DHCP** . 
1. Sélectionnez **Enregistrer** .


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>Spécifier une adresse IP de plage DHCP sur un segment

> [!NOTE]
> Cette configuration est nécessaire pour activer la fonctionnalité de relais DHCP sur le segment de client DHCP. 

1. Sous **Connectivité** , sélectionnez **Segments** . 
1. Sélectionnez les trois points suspendus, puis **Modifier** . 

   >[!TIP]
   >Si vous souhaitez ajouter un nouveau segment, sélectionnez **Ajouter un segment** .

1. Ajoutez des détails sur le segment. 
1. Pour ajouter ou modifier le sous-réseau, sélectionnez la valeur sous **Définir des sous-réseaux** .

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Ajouter un serveur DHCP" border="true":::

1. Sélectionnez les ellipses verticales, puis choisissez **Modifier** . Si vous avez besoin de créer un sous-réseau, sélectionnez **Ajouter un sous-réseau** pour créer une passerelle et configurer une plage DHCP. 
1. Indiquez la plage du pool d’adresses IP, sélectionnez **Appliquer** , puis sélectionnez **Enregistrer**

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Ajouter un serveur DHCP" border="true":::

   Un pool de serveurs DHCP est affecté au segment.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Ajouter un serveur DHCP" border="true":::
