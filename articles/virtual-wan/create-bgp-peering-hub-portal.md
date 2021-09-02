---
title: Créer un appairage BGP avec un hub virtuel (préversion) - Portail Azure
titleSuffix: Azure Virtual WAN
description: Découvrez comment créer un appairage BGP avec un routeur hub WAN virtuel.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 6c1d845e4f4ad3a61e3131f6451e12070f2af48c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563202"
---
# <a name="how-to-create-bgp-peering-with-virtual-hub-preview---azure-portal"></a>Comment créer un appairage BGP avec un hub virtuel (préversion) - Portail Azure

Cet article vous aide à configurer un routeur hub Azure Virtual WAN pour effectuer un appairage avec une appliance virtuelle réseau (NVA) dans votre réseau virtuel en utilisant le portail Azure. Le routeur hub virtuel apprend les itinéraires à partir de l’appliance virtuelle réseau dans un réseau virtuel spoke connecté à un hub WAN virtuel. Le routeur hub virtuel publie également les itinéraires de réseau virtuel vers l’appliance virtuelle réseau. Pour plus d’informations, consultez [Scénario : appairage BGP avec un hub virtuel](scenario-bgp-peering-hub.md).

[!INCLUDE [Gated public preview SLA link](../../includes/virtual-wan-gated-public-preview-sla.md)]

:::image type="content" source="./media/create-bgp-peering-hub-portal/diagram.png" alt-text="Diagramme de la configuration.":::

## <a name="prerequisites"></a>Configuration requise

> [!IMPORTANT]
> La fonctionnalité d’appairage BGP avec le hub WAN virtuel est actuellement en préversion publique contrôlée. Si vous souhaitez essayer cette fonctionnalité, envoyez un e-mail à **previewbgpwithvhub@microsoft.com** avec l’ID de ressource de votre ressource WAN virtuelle. 
>
> Pour localiser l’ID de ressource, ouvrez le portail Azure, naviguez jusqu’à votre ressource WAN virtuelle et sélectionnez **Paramètres > Propriétés > ID de ressource.**<br> Exemple : `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Créer un WAN virtuel

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Créer un hub

Un hub est un réseau virtuel qui peut contenir des passerelles pour offrir des fonctionnalités de site à site, ExpressRoute ou de point à site. Une fois le hub créé, vous serez facturé, même si vous n’y joignez aucun site.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Connecter le réseau virtuel au hub

Dans cette section, vous créez une connexion entre votre hub et le réseau virtuel.

[!INCLUDE [Connect a VNet to a hub](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="configure-a-bgp-peer"></a>Configurer un pair BGP

1.  Ouvrez le [portail de préversion Azure](https://aka.ms/azurecortexv2) en utilisant [https://aka.ms/azurecortexv2](https://aka.ms/azurecortexv2). La fonctionnalité d’appairage BGP avec le hub WAN virtuel est actuellement en préversion gérée et les pages de configuration ne sont pas disponibles dans le portail Azure standard.

1.  Sur la page du portail de votre WAN virtuel, dans la section **Connectivité**, sélectionnez **Hubs** pour afficher la liste des hubs. Cliquez sur un hub pour configurer un pair BGP.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/hubs.png" alt-text="Capture d'écran des hubs.":::

1.  Sur la page **Hub virtuel**, sous la section **Routage**, sélectionnez **Pairs BGP** et cliquez sur **+ Ajouter** pour ajouter un pair BGP.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/bgp-peers.png" alt-text="3.":::

1.  Sur la page **Ajouter un pair BGP**, remplissez tous les champs.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/add-peer.png" alt-text="4.":::

    * **Nom** : nom de ressource pour identifier un pair BGP spécifique. 
    * **ASN** : ASN pour le pair BGP.
    * **Adresse IPv4** : adresse IPv4 du pair BGP.
    * **Connexion au réseau virtuel** : choisissez l’identificateur de connexion qui correspond au réseau virtuel qui héberge le pair BGP.

1.  Cliquez sur **Ajouter** pour terminer la configuration BGP et afficher le pair.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/view-peer.png" alt-text="Capture d’écran du pair ajouté.":::

## <a name="modify-a-bgp-peer"></a>Modifier un pair BGP

1. Sur la ressource du **hub virtuel**, cliquez sur **Pairs BGP** et sélectionnez le pair BGP. Cliquez sur **…** puis sur **Modifier**.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/modify.png" alt-text="Capture d’écran de la modification.":::

1. Une fois le pair BGP modifié, cliquez sur **Ajouter** pour enregistrer.

## <a name="delete-a-bgp-peer"></a>Supprimer un pair BGP

1. Sur la ressource du **hub virtuel**, cliquez sur **Pairs BGP** et sélectionnez le pair BGP. Cliquez sur **…** , puis sur **Supprimer**.

    :::image type="content" source="./media/create-bgp-peering-hub-portal/delete.png" alt-text="Capture d’écran de la suppression d’un pair.":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les scénarios BGP, consultez [Scénario : appairage BGP avec un hub virtuel](scenario-bgp-peering-hub.md).
