---
title: Comment gérer le protocole DHCP
description: Cet article explique comment gérer le protocole DHCP dans une solution Azure VMware (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 80791dd2041fb9d6fbc7c67f2d7d7b2d0b6c977e
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148359"
---
# <a name="how-to-manage-dhcp-in-azure-vmware-solution-avs-preview"></a>Comment gérer le protocole DHCP dans une solution Azure VMWare (AVS) en préversion

NSX-T offre la possibilité de configurer le protocole DHCP pour votre cloud privé. Si vous envisagez d’utiliser NSX-T pour héberger votre serveur DHCP, consultez [Créer un serveur DHCP](#create-dhcp-server). Autrement, s’il y a un serveur DHCP externe tiers dans votre réseau et si vous souhaitez relayer des demandes vers ce serveur, consultez [Créer un service de relais DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Créer un serveur DHCP

Pour configurer un serveur DHCP sur NSX-T, procédez comme suit.

Dans le gestionnaire NSX, accédez à l’onglet **Mise en réseau**, puis, sous **Gestion IP**, sélectionnez **DHCP**. Sélectionnez le bouton **AJOUTER UN SERVEUR**. Fournissez le nom et l’adresse IP du serveur. Cela fait, sélectionnez **Enregistrer**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Ajouter un serveur DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Connectez le serveur DHCP à la passerelle de niveau 1.

Sélectionnez **Passerelles de niveau 1**, sélectionnez la passerelle, puis sélectionnez **Modifier**.

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Sélectionner la passerelle à utiliser" border="true":::

Ajoutez un sous-réseau en sélectionnant **Aucune allocation d’adresse IP définie**.

:::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Ajouter un sous-réseau" border="true":::

Dans l’écran suivant, dans la liste déroulante **Type**, sélectionnez **Serveur local DHCP**. Pour **Serveur DHCP**, sélectionnez **DHCP par défaut**, puis **Enregistrer**.

:::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="Sélectionner des options pour le serveur DHCP" border="true":::

Dans la fenêtre **Passerelle de niveau 1**, sélectionnez **Enregistrer**. Dans l’écran suivant affichant les **Modifications enregistrées**, sélectionnez **Fermer l’édition** pour terminer.

### <a name="add-a-network-segment"></a>Ajouter un segment réseau

Une fois que vous avez créé votre serveur DHCP, vous devez lui ajouter des segments réseau.

Dans NSX-T, sélectionnez l’onglet **Mise en réseau**, puis, sous **Connectivité**, sélectionnez **Segments**. Sélectionnez **AJOUTER UN SEGMENT**. Nommez le segment et la connexion à la passerelle de niveau 1. Ensuite, sélectionnez **Définir des sous-réseaux** pour configurer un nouveau sous-réseau. 

:::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Ajouter un nouveau segment réseau" border="true":::

Dans la fenêtre **Définir des sous-réseaux**, sélectionnez **AJOUTER UN SOUS-RÉSEAU**. Entrez l’adresse IP de la passerelle et la plage DHCP, puis sélectionnez **Ajouter** puis **APPLIQUER**.

:::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="Ajouter un segment réseau" border="true":::

Lorsque vous avez terminé, sélectionnez **Enregistrer** pour terminer l’ajout d’un segment réseau.

:::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="segments terminés" border="true":::

## <a name="create-dhcp-relay-service"></a>Créer un service de relais DHCP

Dans la fenêtre NXT-T, sélectionnez l’onglet **Mise en réseau**, puis, sous **Gestion IP**, sélectionnez **DHCP**. Sélectionnez **AJOUTER UN SERVEUR**. Choisissez Relais DHCP comme **Type de serveur**, puis entrez le nom du serveur et l’adresse IP du serveur relais. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

:::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Créer un serveur relais DHCP" border="true":::

Sous **Connectivité**, sélectionnez **Passerelles de niveau 1**. Sélectionnez les trois points suspendus sur la passerelle de niveau 1, puis choisissez **Modifier**.

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Modifier la passerelle de niveau 1" border="true":::

Sélectionnez **Aucune allocation IP définie** pour définir l’allocation d’adresse IP.

:::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Modifier l’allocation d’adresse IP" border="true":::

Dans la boîte de dialogue, pour **Type**, sélectionnez **Serveur relais DHCP**. Dans la liste déroulante **Relais DHCP**, sélectionnez votre serveur relais DHCP. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

:::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="Définir la gestion des adresses IP" border="true":::

Spécifiez une adresse IP de plage DHCP sur un segment :

> [!NOTE]
> Cette configuration est nécessaire pour activer la fonctionnalité de relais DHCP sur le segment de client DHCP. 

Sous **Connectivité**, sélectionnez **Segments**. Sélectionnez les trois points suspendus, puis **Modifier**. Au lieu de cela, si vous souhaitez ajouter un nouveau segment, vous pouvez sélectionner **Ajouter un segment** pour créer un segment.

:::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="Modifier un sous-réseau de réseau" border="true":::

Ajoutez des détails sur le segment. Pour ajouter ou modifier le sous-réseau, sélectionnez la valeur sous  **Sous-réseaux** ou **Définir des sous-réseaux**.

:::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segments réseau" border="true":::

Sélectionnez les ellipses verticales, puis choisissez **Modifier**. Si vous avez besoin de créer un sous-réseau, sélectionnez **Ajouter un sous-réseau** pour créer une passerelle et configurer une plage DHCP. Indiquez la plage du pool d’adresses IP, sélectionnez **Appliquer**, puis sélectionnez **Enregistrer**.

:::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Modifier des sous-réseaux" border="true":::

Un pool de serveurs DHCP est maintenant affecté au segment.

:::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Pool de serveurs DHCP affecté à un segment" border="true":::
