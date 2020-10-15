---
title: 'Passerelle VPN : Modifier les paramètres d’adresse IP de la passerelle : Portail Azure'
description: Cet article vous guide tout au long du processus de modification des préfixes d’adresse IP de la passerelle de votre réseau local à l’aide du portail Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983171"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modifier les paramètres de la passerelle du réseau local à l’aide du portail Azure

Parfois, les paramètres de la passerelle de réseau local AddressPrefix ou GatewayIPAddress changent. Cet article vous montre comment modifier les paramètres de passerelle de votre réseau local. Vous pouvez également modifier ces paramètres à l’aide d’une méthode différente en sélectionnant une autre option dans la liste suivante :

Avant de supprimer la connexion, vous pouvez télécharger la configuration de vos appareils de connexion afin d’obtenir la clé pré-partagée (PSK) définie. Ceci vous évite d’avoir à la redéfinir de l’autre côté.

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configuration de la passerelle de réseau local

La capture d’écran ci-dessous montre la page **Configuration** d’une ressource de passerelle de réseau local utilisant un point de terminaison d’adresse IP publique :

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Configurer une passerelle de réseau local - Adresse IP":::

Il s’agit de la même page de configuration avec un point de terminaison de nom de domaine complet :

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Configurer une passerelle de réseau local - Adresse IP":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Modifier l’adresse IP de la passerelle

Si le périphérique VPN auquel vous souhaitez vous connecter a changé son adresse IP publique, vous devez modifier la passerelle de réseau local pour refléter cette modification.

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Dans la zone **Adresse IP**, modifiez l’adresse IP.
3. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>Modifier le nom de domaine complet de la passerelle

Si le périphérique VPN auquel vous souhaitez vous connecter a changé son nom de domaine complet (FQDN), vous devez modifier la passerelle de réseau local pour refléter cette modification.

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Dans la zone **Nom de domaine complet**, modifiez le nom de domaine.
3. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

> ![REMARQUE] Vous ne pouvez pas modifier une passerelle de réseau local entre un point de terminaison FQDN et un point de terminaison d’adresse IP. Vous devez supprimer toutes les connexions associées à cette passerelle de réseau local, en créer une nouvelle avec le nouveau point de terminaison (adresse IP ou nom de domaine complet), puis recréer les connexions.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modifier les préfixes d’adresse IP

### <a name="to-add-additional-address-prefixes"></a>Pour ajouter des préfixes d’adresses :

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Ajoutez l’espace d’adressage IP dans la zone *Ajouter une autre plage d’adresses*.
3. Cliquez sur **Save** pour enregistrer vos paramètres.

### <a name="to-remove-address-prefixes"></a>Pour supprimer des préfixes d’adresses :

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Cliquez sur **'...'** sur la ligne contenant le préfixe à supprimer.
3. Cliquez sur **Supprimer**.
4. Cliquez sur **Save** pour enregistrer vos paramètres.

## <a name="modify-bgp-settings"></a><a name="bgp"></a>Modifier les paramètres de BGP

### <a name="to-add-or-update-bgp-settings"></a>Pour ajouter ou mettre à jour les paramètres de BGP :

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Sélectionnez **Configurer les paramètres de BGP** pour afficher ou mettre à jour les configurations BGP pour cette passerelle de réseau local
3. Ajouter ou mettre à jour le numéro de système autonome ou l’adresse IP du pair BGP dans les champs correspondants
4. Cliquez sur **Save** pour enregistrer vos paramètres.

### <a name="to-remove-bgp-settings"></a>Pour supprimer les paramètres BGP :

1. Sur la ressource de la passerelle de réseau local, dans la section **Paramètres** , cliquez sur **Configuration**.
2. Désactivez **« Configurer les paramètres de BGP »** pour supprimer l’adresse IP d’ASN BGP et de pair BGP existante
3. Cliquez sur **Save** pour enregistrer vos paramètres.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez vérifier votre connexion à la passerelle. Consultez [Vérifier la connexion à une passerelle](vpn-gateway-verify-connection-resource-manager.md).
