---
title: 'Passerelle VPN Azure : Configurer les captures de paquets'
description: En savoir plus sur les fonctionnalités de capture de paquets que vous pouvez utiliser sur les passerelles VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353514"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Configurer les captures de paquets pour les passerelles VPN

Les problèmes liés à la connectivité et aux performances sont souvent complexes et nécessitent beaucoup de temps et d’efforts pour identifier la cause du problème. La possibilité de capturer des paquets permet de réduire ce temps en limitant l’étendue du problème à certaines parties du réseau, par exemple si le problème se situe au niveau du client, du côté d’Azure ou entre les deux. Une fois que le problème a été localisé, il est bien plus facile de déboguer et de prendre des mesures correctives.

Il existe des outils couramment disponibles pour la capture de paquets. Toutefois, l’obtention de captures de paquets pertinentes à l’aide de ces outils est souvent fastidieuse, surtout lorsque vous travaillez avec des scénarios de trafic à volume élevé. Les capacités de filtrage fournies par une capture de paquets de passerelle VPN deviennent un avantage majeur. Vous pouvez utiliser une capture de paquets de passerelle VPN en plus des outils de capture de paquets couramment disponibles.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Fonctionnalités de filtrage de capture de paquets de la passerelle VPN

Les captures de paquets de la passerelle VPN peuvent être exécutées sur la passerelle ou sur une connexion spécifique en fonction des besoins du client. Vous pouvez également exécuter des captures de paquets sur plusieurs tunnels à la fois. Vous pouvez capturer le trafic à sens unique ou bidirectionnel, le trafic IKE et ESP, et les paquets internes, ainsi que filtrer sur une passerelle VPN.

L’utilisation de filtres à 5 tuples (sous-réseau source, sous-réseau de destination, port source, port de destination, protocole) et d’indicateurs TCP (SYN, ACK, FIN, URG, PSH, RST) est utile pour isoler les problèmes sur un trafic à volume élevé.

Vous ne pouvez utiliser qu’une seule option par propriété lors de l’exécution de la capture de paquets.

## <a name="setup-packet-capture-using-powershell"></a>Configurer la capture de paquets à l’aide de PowerShell

Consultez les exemples ci-dessous pour connaître les commandes PowerShell permettant de démarrer et d’arrêter les captures de paquets. Pour plus d’informations sur les options des paramètres (par exemple, comment créer des filtres), consultez le [document](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture) PowerShell.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Démarrer la capture de paquets pour une passerelle VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Le paramètre facultatif **FilterData** peut être utilisé pour appliquer des filtres.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Arrêter la capture de paquets pour une passerelle VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Démarrer la capture de paquets pour une connexion de passerelle VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Le paramètre facultatif **FilterData** peut être utilisé pour appliquer des filtres.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Arrêter la capture de paquets sur une connexion de passerelle VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Considérations relatives aux clés

- L’exécution de captures de paquets peut affecter les performances. N’oubliez pas d’arrêter la capture de paquets lorsqu’elle n’est pas nécessaire.
- La durée de capture de paquets minimale suggérée est de 600 secondes. Une durée de capture de paquets plus faible peut ne pas fournir de données complètes en raison des problèmes de synchronisation entre plusieurs composants sur le chemin.
- Les fichiers de données de capture de paquets sont générés au format PCAP. Utilisez Wireshark ou d’autres applications couramment disponibles pour ouvrir les fichiers PCAP.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la passerelle VPN, consultez [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md)
