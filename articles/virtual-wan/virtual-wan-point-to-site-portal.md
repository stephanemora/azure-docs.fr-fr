---
title: Créer une connexion point à site vers Azure à l’aide d’Azure Virtual WAN | Microsoft Docs
description: Dans ce didacticiel, découvrez comment utiliser Azure Virtual WAN pour créer une connexion VPN point à site vers Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 84f8563a6b03f10f4cbc647426c350d9fac52780
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812682"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutoriel : Créer une connexion de VPN utilisateur à l’aide d’Azure Virtual WAN

Ce didacticiel vous montre comment utiliser Azure Virtual WAN pour vous connecter à vos ressources dans Azure via une connexion VPN IPsec/IKE (IKEv2) ou OpenVPN. Pour utiliser ce type de connexion, un client doit être configuré sur l’ordinateur client. Pour plus d’informations sur le WAN virtuel, consultez [Vue d'ensemble de WAN virtuel](virtual-wan-about.md)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un WAN
> * Créer une configuration P2S
> * Créer un hub
> * Spécifier les serveurs DNS
> * Télécharger un profil de client VPN
> * Afficher votre WAN virtuel

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Créer un WAN virtuel

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Créer une configuration P2S

Une configuration P2S définit les paramètres permettant de connecter des clients distants.Une configuration P2S (point à site) définit les paramètres permettant de connecter des clients distants.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Créer un hub avec une passerelle point à site

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Spécifier un serveur DNS

Les passerelles VPN utilisateur Virtual WAN vous permettent d’indiquer jusqu’à 5 serveurs DNS. Vous pouvez configurer cela durant le processus de création du hub, ou modifier celui-ci ultérieurement. Pour ce faire, localisez le hub virtuel. Sous **VPN utilisateur (point à site)** , sélectionnez **configurer** et entrez l’adresse ou les adresses IP de serveur DNS dans les zones de texte **Serveurs DNS personnalisés**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personnalisé" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Télécharger le profil VPN

Utilisez le profil VPN pour configurer vos clients.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>Configurer les clients VPN utilisateurs

Pour configurer les clients avec accès à distance, utilisez le profil téléchargé. La procédure pour chaque système d’exploitation étant différente, suivez les instructions qui s’appliquent à votre système.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
1. Sur la page **Vue d’ensemble**, chaque point sur la carte représente un hub.
1. Dans la section **Hubs et connexions**, vous pouvez voir l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
