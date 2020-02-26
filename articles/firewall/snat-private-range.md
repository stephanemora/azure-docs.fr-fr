---
title: Pare-feu Azure traduit l’adresse réseau source des plages d’adresses IP privées
description: Vous pouvez configurer des plages privées d’adresses IP pour que le pare-feu ne traduise pas l’adresse réseau source du trafic en l’une de ces adresses IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444830"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Pare-feu Azure traduit l’adresse réseau source des plages d’adresses IP privées

Pare-feu Azure ne traduit pas l’adresse réseau source lorsque l’adresse IP de destination est une plage d’adresses IP privées selon la norme [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

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
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
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