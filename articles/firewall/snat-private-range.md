---
title: Pare-feu Azure traduit l’adresse réseau source des plages d’adresses IP privées
description: Vous pouvez configurer des plages d'adresses IP pour la traduction d'adresses réseau sources (SNAT).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: 01707a99b1ff5d077daf3c095e1daf78ccddfeac
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107898186"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Pare-feu Azure traduit l’adresse réseau source des plages d’adresses IP privées

Le Pare-feu Azure assure automatiquement la traduction SNAT pour l'ensemble du trafic sortant à destination d'adresses IP publiques. Par défaut, Pare-feu Azure ne traduit pas l’adresse réseau source avec des règles de réseau lorsque l’adresse IP de destination se trouve dans une plage d’adresses IP privées conformément à la norme [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) ou dans un espace d’adressage partagé conformément à la norme [IANA RFC 6598](https://tools.ietf.org/html/rfc6598). Les règles d’application sont toujours appliquées à l’aide d’un [proxy transparent](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy), quelle que soit l’adresse IP de destination.

Cette logique fonctionne bien lorsque vous acheminez le trafic directement vers Internet. Cependant, si vous avez activé le [tunneling forcé](forced-tunneling.md), le trafic Internet est « SNATé » vers l'une des adresses IP privées du pare-feu dans AzureFirewallSubnet, ce qui a pour effet de masquer la source à votre pare-feu local.

Si votre organisation utilise une plage d’adresses IP publiques pour les réseaux privés, Pare-feu Azure traduit l’adresse réseau source du trafic en une des adresses IP privées du pare-feu dans AzureFirewallSubnet. Toutefois, vous pouvez configurer Pare-feu Azure pour qu’il n’effectue **pas** une telle traduction. Par exemple, pour spécifier une adresse IP individuelle, vous pouvez la spécifier comme suit : `192.168.1.10`. Pour spécifier une plage d’adresses IP, vous pouvez la spécifier comme suit : `192.168.1.0/24`.

- Pour configurer le Pare-feu Azure afin de ne **jamais** utiliser la traduction d’adresses réseau sources (SNAT), quelle que soit l’adresse IP de destination, utilisez **0.0.0.0/0** comme plage d’adresses IP privées. Avec cette configuration, le Pare-feu Azure ne peut jamais acheminer le trafic directement vers Internet. 

- Pour configurer le pare-feu afin qu’il utilise **toujours** la traduction SNAT, quelle que soit l’adresse de destination, utilisez **255.255.255.255/32** comme plage d’adresses IP privées.

> [!IMPORTANT]
> La plage d’adresses privées que vous spécifiez s’applique uniquement aux règles de réseau. Actuellement, les règles d’application sont toujours SNAT.

> [!IMPORTANT]
> Si vous souhaitez spécifier vos propres plages d’adresses IP privées et conserver les plages d’adresses IANA RFC 1918 par défaut, assurez-vous que votre liste personnalisée comprend toujours la plage IANA RFC 1918. 

Vous pouvez configurer les adresses IP privées SNAT à l’aide des méthodes suivantes. Vous devez configurer les adresses privées SNAT à l’aide de la méthode appropriée à votre configuration. Les pare-feu associés à une stratégie de pare-feu doivent spécifier la plage dans la stratégie et ne pas utiliser `AdditionalProperties`.


|Méthode            |Utilisation de règles classiques  |Utilisation d’une stratégie de pare-feu  |
|---------|---------|---------|
|Portail Azure     | [pris en charge](#classic-rules-3)| [pris en charge](#firewall-policy-1)|
|Azure PowerShell     |[configurer `PrivateRange`](#classic-rules)|actuellement non pris en charge|
|Azure CLI|[configurer `--private-ranges`](#classic-rules-1)|actuellement non pris en charge|
|Modèle ARM     |[configurer `AdditionalProperties` dans le pare-feu, propriété](#classic-rules-2)|[configurer `snat/privateRanges` dans le pare-feu, stratégie](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurer des plages d'adresses IP privées pour la traduction SNAT - Azure PowerShell
### <a name="classic-rules"></a>Règles classiques

Vous pouvez utiliser Azure PowerShell afin de spécifier des plages d'adresses IP privées pour le pare-feu.

> [!NOTE]
> La propriété de pare-feu `PrivateRange` est ignorée pour les pare-feu associés à une stratégie de pare-feu. Vous devez utiliser la propriété `SNAT` dans `firewallPolicies`, comme décrit dans [Configuration de plages d’adresses IP privées SNAT - Modèle ARM](#firewall-policy).

#### <a name="new-firewall"></a>Nouveau pare-feu

Pour un nouveau pare-feu utilisant des règles classiques, la cmdlet Azure PowerShell est la suivante :

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

#### <a name="existing-firewall"></a>Pare-feu existant

Pour configurer un pare-feu existant utilisant des règles classiques, utilisez les cmdlets Azure PowerShell suivantes :

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Configuration de plages d’adresses IP privées SNAT – Azure CLI
### <a name="classic-rules"></a>Règles classiques

Vous pouvez utiliser Azure CLI afin de spécifier des plages d’adresses IP privées pour le pare-feu utilisant des règles classiques. 

#### <a name="new-firewall"></a>Nouveau pare-feu

Pour un nouveau pare-feu utilisant des règles classiques, la commande Azure CLI est la suivante :

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Le déploiement du Pare-feu Azure avec la commande Azure CLI `az network firewall create` impose des étapes de configuration supplémentaires visant à créer des adresses IP publiques et une configuration IP. Pour accéder à un guide de déploiement complet, consultez [Déploiement et configuration d’un Pare-feu Azure avec Azure CLI](deploy-cli.md).

> [!NOTE]
> IANAPrivateRanges est étendu aux valeurs par défaut actuelles sur Pare-feu Azure, tandis que les autres plages y sont ajoutées. Pour conserver la valeur par défaut IANAPrivateRanges dans votre spécification de plage privée, elle doit rester dans votre spécification `private-ranges`, comme indiqué dans les exemples suivants.

#### <a name="existing-firewall"></a>Pare-feu existant

Pour configurer un pare-feu existant utilisant des règles classiques, la commande Azure CLI est la suivante :

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Configuration de plages d’adresses IP privées SNAT - Modèle ARM
### <a name="classic-rules"></a>Règles classiques

Pour configurer SNAT pendant le déploiement d’un modèle ARM, vous pouvez ajouter les éléments suivants à la propriété `additionalProperties` :

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>Stratégie de pare-feu

Les pare-feu Azure associés à une stratégie de pare-feu contiennent des plages privées SNAT prises en charge depuis la version 2020-11-01 de l’API. Actuellement, vous pouvez utiliser un modèle pour mettre à jour la plage privée SNAT dans la stratégie de pare-feu. L’exemple suivant configure **toujours** le pare-feu sur le trafic réseau SNAT :

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurer des plages d'adresses IP privées pour la traduction SNAT - Portail Azure
### <a name="classic-rules"></a>Règles classiques

Vous pouvez utiliser le portail Azure afin de spécifier des plages d'adresses IP privées pour le pare-feu.

1. Sélectionnez votre groupe de ressources, puis votre pare-feu.
2. Sur la page **Présentation**, **Plages IP privées**, sélectionnez la valeur par défaut **IANA RFC 1918**.

   La page **Modifier les préfixes des adresses IP privées** s'ouvre :

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Modifier les préfixes des adresses IP privées":::

1. Par défaut, **IANAPrivateRanges** est configuré.
2. Modifiez les plages d'adresses IP privées de votre environnement, puis sélectionnez **Enregistrer**.

### <a name="firewall-policy"></a>Stratégie de pare-feu

1.  Sélectionnez votre groupe de ressources, puis votre stratégie de pare-feu.
2.  Sélectionnez **Plages d’adresses IP privées (SNAT)** dans la colonne **Paramètres**.

    Par défaut, **Utiliser le comportement SNAT par défaut de la stratégie de pare-feu Azure** est sélectionné. 
3. Pour personnaliser la configuration SNAT, décochez la case, puis, sous **Effectuer une traduction SNAT**, sélectionnez les conditions pour effectuer une traduction SNAT pour votre environnement.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="Plages d'adresses IP privées (SNAT)":::


4.   Sélectionnez **Appliquer**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [Tunneling forcé du Pare-feu Azure](forced-tunneling.md)
