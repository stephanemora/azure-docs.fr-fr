---
title: 'Configurer des passerelles VPN de type actif/actif : Portail Azure'
titleSuffix: Azure VPN Gateway
description: Apprenez à configurer des passerelles de réseau virtuel de type actif/actif à l’aide du portail Azure.
services: vpn-gateway
author: JackStromberg
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/22/2021
ms.author: jstrom
ms.openlocfilehash: 1a5ca58b9e56826c7173c499b64ebabc80ddf11e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525063"
---
# <a name="configure-active-active-vpn-gateways-using-the-portal"></a>Configurer des passerelles VPN de type actif/actif à l’aide du portail

Cet article vous aide à créer des passerelles VPN de type actif/actif hautement disponibles à l’aide du [modèle de déploiement Resource Manager](../azure-resource-manager/management/deployment-models.md) et du portail Azure. Vous pouvez également configurer une passerelle de type actif/actif à l’aide de [PowerShell](vpn-gateway-activeactive-rm-powershell.md).

Pour obtenir une haute disponibilité des connexions intersites et de réseau virtuel à réseau virtuel, vous devez déployer plusieurs passerelles VPN, et établir plusieurs connexions parallèles entre vos réseaux et Azure. Pour une vue d’ensemble des options de connectivité et de la topologie, consultez [Connectivité hautement disponible entre différents locaux et de réseau virtuel à réseau virtuel](vpn-gateway-highlyavailable.md).

> [!IMPORTANT]
> Le mode actif/actif est disponible pour toutes les références SKU, sauf De base ou Standard. Pour plus d’informations, consultez [Paramètres de configuration](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
>

Les étapes décrites dans cet article vous aident à configurer une passerelle VPN en mode actif/actif. Il existe quelques différences entre les modes actif/actif et actif/passif. Les autres propriétés sont les mêmes que celles des passerelles en mode actif/actif. 

* Les passerelles de type actif/actif ont deux configurations IP de passerelle et deux adresses IP publiques.
* Le paramètre actif/actif est activé pour les passerelles de type actif/actif.
* La référence SKU de la passerelle de réseau virtuel ne peut pas être De base ou Standard.

Si vous disposez déjà d’une passerelle VPN, vous pouvez [Mettre à jour une passerelle VPN existante](#update) du mode actif/passif au mode actif/actif, ou inversement.

## <a name="create-a-vnet"></a><a name="vnet"></a>Créer un réseau virtuel

Si vous ne disposez pas déjà d’un réseau virtuel que vous souhaitez utiliser, créez un réseau virtuel à l’aide des valeurs suivantes :

* **Groupe de ressources :** TestRG1
* **Nom :** VNet1
* **Région :** (États-Unis) USA Est
* **Espace d’adressage IPv4 :** 10.1.0.0/16
* **Nom du sous-réseau** : FrontEnd
* **Espace d’adressage du sous-réseau :** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-an-active-active-vpn-gateway"></a><a name="gateway"></a>Créer une passerelle VPN de type actif/actif

Dans cette étape, vous créez la passerelle de réseau virtuel (passerelle VPN) de type actif/actif pour votre réseau virtuel. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

Créez une passerelle de réseau virtuel à l’aide des valeurs suivantes :

* **Nom :** VNet1GW
* **Région :** USA Est
* **Type de passerelle :** VPN
* **Type de VPN :** basé sur la route
* **Référence SKU :** VpnGw2
* **Génération :** Génération 2
* **Réseau virtuel :** VNet1
* **Plage d’adresses du sous-réseau de passerelle :** 10.1.255.0/27
* **Adresse IP publique :** Création
* **Nom de l'adresse IP publique :** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-active-portal-include.md)]

Vous pouvez voir l’état du déploiement dans la page Vue d’ensemble pour votre passerelle. Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="update-an-existing-vpn-gateway"></a><a name ="update"></a> Mettre à jour une passerelle VPN existante

Cette section vous aide à configurer une passerelle VPN Azure existante pour passer du mode actif/passif au mode actif/actif, ou inversement. Lorsque vous transformez une passerelle de type actif/passif en passerelle de type actif/actif, vous créez une autre adresse IP publique, puis ajoutez une deuxième configuration IP de la passerelle. 

### <a name="change-active-standby-to-active-active"></a>Passer du type actif/passif au type actif/actif

Procédez comme suit pour convertir une passerelle en mode actif/passif en mode actif/actif. Si votre passerelle a été créée à l’aide du [modèle de déploiement Resource Manager](../azure-resource-manager/management/deployment-models.md), vous pouvez également mettre à niveau la référence SKU sur cette page.

1. Accédez à la page de votre passerelle de réseau virtuel.

1. Dans le menu de gauche, sélectionnez **Configuration**.

1. Sur la page **Configuration**, configurez les paramètres suivants : 

   * Définissez le mode actif/actif sur **Activé**.
   * Cliquez sur **Créer une autre configuration IP de passerelle**.

   :::image type="content" source="./media/active-active-portal/configuration.png" alt-text="Capture d’écran montrant la page Configuration.":::

1. Sur la page **Choisir une adresse IP publique**, spécifiez une adresse IP publique existante correspondant aux critères ou sélectionnez **+Créer nouveau** pour créer une nouvelle adresse IP publique à utiliser pour la deuxième instance de passerelle VPN.

1. Sur la page **Créer une adresse IP publique**, sélectionnez la référence SKU **De base**, puis cliquez sur **OK**.

1. En haut de la page **Configuration**, cliquez sur **Enregistrer**. Cette mise à jour peut prendre environ 30-45 minutes.

### <a name="change-active-active-to-active-standby"></a>Passer du type actif/actif au type actif/passif

Procédez comme suit pour convertir une passerelle en mode actif/actif en mode actif/passif.

1. Accédez à la page de votre passerelle de réseau virtuel.

1. Dans le menu de gauche, sélectionnez **Configuration**.

1. Sur la page **Configuration**, définissez le mode actif/actif sur **Désactivé**.

1. En haut de la page **Configuration**, cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des connexions, consultez les articles suivants :

* [Connexions VPN de site à site](./tutorial-site-to-site-portal.md)
* [Connexions de réseau virtuel à réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md#configure-the-vnet1-gateway-connection)