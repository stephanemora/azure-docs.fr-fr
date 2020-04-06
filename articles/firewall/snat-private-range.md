---
title: Pare-feu Azure traduit l’adresse réseau source des plages d’adresses IP privées
description: Vous pouvez configurer des plages privées d’adresses IP pour que le pare-feu ne traduise pas l’adresse réseau source du trafic en l’une de ces adresses IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064812"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Pare-feu Azure traduit l’adresse réseau source des plages d’adresses IP privées

Pare-feu Azure ne traduit pas l’adresse réseau source avec des règles de réseau lorsque l’adresse IP de destination est une plage d’adresses IP privées selon la norme [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Les règles d’application sont toujours appliquées à l’aide d’un [proxy transparent](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy), quelle que soit l’adresse IP de destination.

Si votre organisation utilise une plage d’adresses IP publiques pour les réseaux privés, Pare-feu Azure traduit l’adresse réseau source du trafic en une des adresses IP privées du pare-feu dans AzureFirewallSubnet. Toutefois, vous pouvez configurer Pare-feu Azure pour qu’il n’effectue **pas** une telle traduction.

## <a name="configure-snat-private-ip-address-ranges"></a>Configurer la traduction de l’adresse réseau source des plages d’adresses IP privées

Vous pouvez utiliser Azure PowerShell pour spécifier une plage d’adresses IP dont le pare-feu ne traduit pas l’adresse réseau source.

### <a name="new-firewall"></a>Nouveau pare-feu

Pour un nouveau pare-feu, la commande Azure PowerShell est :

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges est étendu aux valeurs par défaut actuelles sur Pare-feu Azure, tandis que les autres plages y sont ajoutées.

Pour plus d’informations, consultez [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Pare-feu existant

Pour configurer un pare-feu existant, utilisez les commandes Azure PowerShell suivantes :

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modèles

Vous pouvez ajouter ce qui suit à la section `additionalProperties` :

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md).