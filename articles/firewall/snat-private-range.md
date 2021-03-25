---
title: Pare-feu Azure traduit l’adresse réseau source des plages d’adresses IP privées
description: Vous pouvez configurer des plages d'adresses IP pour la traduction d'adresses réseau sources (SNAT).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: c425afc314435c38d15d53ab0c38dcd48e35a40b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508926"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Pare-feu Azure traduit l’adresse réseau source des plages d’adresses IP privées

Le Pare-feu Azure assure automatiquement la traduction SNAT pour l'ensemble du trafic sortant à destination d'adresses IP publiques. Par défaut, le Pare-feu Azure ne traduit pas l'adresse réseau source avec des règles de réseau lorsque l'adresse IP de destination se trouve dans une plage d'adresses IP privées définie par la norme [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Les règles d’application sont toujours appliquées à l’aide d’un [proxy transparent](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy), quelle que soit l’adresse IP de destination.

Cette logique fonctionne bien lorsque vous acheminez le trafic directement vers Internet. Cependant, si vous avez activé le [tunneling forcé](forced-tunneling.md), le trafic Internet est « SNATé » vers l'une des adresses IP privées du pare-feu dans AzureFirewallSubnet, ce qui a pour effet de masquer la source à votre pare-feu local.

Si votre organisation utilise une plage d’adresses IP publiques pour les réseaux privés, Pare-feu Azure traduit l’adresse réseau source du trafic en une des adresses IP privées du pare-feu dans AzureFirewallSubnet. Toutefois, vous pouvez configurer Pare-feu Azure pour qu’il n’effectue **pas** une telle traduction. Par exemple, pour spécifier une adresse IP individuelle, vous pouvez la spécifier comme suit : `192.168.1.10`. Pour spécifier une plage d’adresses IP, vous pouvez la spécifier comme suit : `192.168.1.0/24`.

- Pour configurer le Pare-feu Azure afin de ne **jamais** utiliser la traduction d’adresses réseau sources (SNAT), quelle que soit l’adresse IP de destination, utilisez **0.0.0.0/0** comme plage d’adresses IP privées. Avec cette configuration, le Pare-feu Azure ne peut jamais acheminer le trafic directement vers Internet. 

- Pour configurer le pare-feu afin qu’il utilise **toujours** la traduction SNAT, quelle que soit l’adresse de destination, utilisez **255.255.255.255/32** comme plage d’adresses IP privées.

> [!IMPORTANT]
> La plage d’adresses privées que vous spécifiez s’applique uniquement aux règles de réseau. Actuellement, les règles d’application sont toujours SNAT.

> [!IMPORTANT]
> Si vous souhaitez spécifier vos propres plages d’adresses IP privées et conserver les plages d’adresses IANA RFC 1918 par défaut, assurez-vous que votre liste personnalisée comprend toujours la plage IANA RFC 1918. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurer des plages d'adresses IP privées pour la traduction SNAT - Azure PowerShell

Vous pouvez utiliser Azure PowerShell afin de spécifier des plages d'adresses IP privées pour le pare-feu.

### <a name="new-firewall"></a>Nouveau pare-feu

Pour un nouveau pare-feu, la cmdlet Azure PowerShell est la suivante :

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> Le déploiement du Pare-feu Azure avec `New-AzFirewall` exige un réseau virtuel et une adresse IP publique existants. Pour accéder à un guide de déploiement complet, consultez [Déploiement et configuration d’un Pare-feu Azure avec Azure PowerShell](deploy-ps.md).

> [!NOTE]
> IANAPrivateRanges est étendu aux valeurs par défaut actuelles sur Pare-feu Azure, tandis que les autres plages y sont ajoutées. Pour conserver la valeur par défaut IANAPrivateRanges dans votre spécification de plage privée, elle doit rester dans votre spécification `PrivateRange`, comme indiqué dans les exemples suivants.

Pour plus d’informations, consultez [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

### <a name="existing-firewall"></a>Pare-feu existant

Pour configurer un pare-feu existant, utilisez les cmdlets Azure PowerShell suivantes :

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Configuration de plages d’adresses IP privées SNAT – Azure CLI

Vous pouvez utiliser Azure CLI afin de spécifier des plages d’adresses IP privées pour le pare-feu.

### <a name="new-firewall"></a>Nouveau pare-feu

Pour un nouveau pare-feu, la commande Azure CLI est la suivante :

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Le déploiement du Pare-feu Azure avec la commande Azure CLI `az network firewall create` impose des étapes de configuration supplémentaires visant à créer des adresses IP publiques et une configuration IP. Pour accéder à un guide de déploiement complet, consultez [Déploiement et configuration d’un Pare-feu Azure avec Azure CLI](deploy-cli.md).

> [!NOTE]
> IANAPrivateRanges est étendu aux valeurs par défaut actuelles sur Pare-feu Azure, tandis que les autres plages y sont ajoutées. Pour conserver la valeur par défaut IANAPrivateRanges dans votre spécification de plage privée, elle doit rester dans votre spécification `PrivateRange`, comme indiqué dans les exemples suivants.

### <a name="existing-firewall"></a>Pare-feu existant

Pour configurer un pare-feu existant, la commande Azure CLI est la suivante :

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Configuration de plages d’adresses IP privées SNAT – Modèle ARM

Pour configurer SNAT pendant le déploiement d’un modèle ARM, vous pouvez ajouter les éléments suivants à la propriété `additionalProperties` :

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurer des plages d'adresses IP privées pour la traduction SNAT - Portail Azure

Vous pouvez utiliser le portail Azure afin de spécifier des plages d'adresses IP privées pour le pare-feu.

1. Sélectionnez votre groupe de ressources, puis votre pare-feu.
2. Sur la page **Présentation**, **Plages IP privées**, sélectionnez la valeur par défaut **IANA RFC 1918**.

   La page **Modifier les préfixes des adresses IP privées** s'ouvre :

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Modifier les préfixes des adresses IP privées":::

1. Par défaut, **IANAPrivateRanges** est configuré.
2. Modifiez les plages d'adresses IP privées de votre environnement, puis sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [Tunneling forcé du Pare-feu Azure](forced-tunneling.md)