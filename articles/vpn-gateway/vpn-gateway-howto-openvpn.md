---
title: 'Configuration d’OpenVPN sur la passerelle VPN Azure : PowerShell| Microsoft Docs'
description: Étapes de configuration d’OpenVPN pour la passerelle VPN Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 07dae60d1d4ab43194f88f44bde498d9fa19ce81
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388098"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Configurer OpenVPN pour la passerelle VPN Azure de point à site (préversion)

Cet article vous aide à configurer OpenVPN sur la passerelle VPN Azure. Nous partons du principe que vous disposez déjà d’un environnement de point à site fonctionnel. Si ce n’est pas le cas, suivez les instructions de l’étape 1 pour créer un réseau VPN de point à site.

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register"></a>Inscrire cette fonctionnalité

Pour inscrire facilement cette fonctionnalité à l’aide d’Azure Cloud Shell, cliquez sur **Essayer** dans les étapes suivantes.

>[!NOTE]
>Si vous n’inscrivez pas cette fonctionnalité, vous ne serez pas en mesure de l’utiliser.
>

Après avoir cliqué sur **Essayer** pour ouvrir Azure Cloud Shell, copiez et collez les commandes suivantes :

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Une fois que la fonctionnalité s’affiche comme étant inscrite, réinscrivez l’abonnement auprès de l’espace de noms Microsoft.Network.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Créer un réseau VPN de point à site

Si vous ne disposez pas d’un environnement de point à site opérationnel, suivez les instructions de création. Consultez l’article [Créer un VPN de point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) pour créer et configurer une passerelle VPN de point à site avec l’authentification par certificat native Azure. 

> [!IMPORTANT]
> La référence De base n’est pas prise en charge pour OpenVPN.

## <a name="cmdlets"></a>2. Installer les applets de commande PowerShell

Installez la dernière version des cmdlets PowerShell de Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Ceci est important, car les versions antérieures des cmdlets ne contiennent pas les valeurs actuelles dont vous avez besoin pour cet exercice.

## <a name="enable"></a>3. Activer OpenVPN sur la passerelle

Activez OpenVPN sur votre passerelle. Assurez-vous que la passerelle est configurée de point à site (IKEv2 ou SSTP) avant d’exécuter les commandes suivantes :

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Étapes suivantes

Pour configurer les clients pour OpenVPN, consultez [Configurer les clients OpenVPN](vpn-gateway-howto-openvpn-clients.md).
