---
title: Paramètres DNS du Pare-feu Azure
description: Vous pouvez configurer le pare-feu Azure avec les paramètres de serveur DNS et de proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: ad0ac040b510783656617ddbf2063cd94c80aae7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380944"
---
# <a name="azure-firewall-dns-settings"></a>Paramètres DNS du Pare-feu Azure

Vous pouvez configurer un serveur DNS personnalisé et activer le proxy DNS pour le pare-feu Azure. Vous pouvez configurer ces paramètres lorsque vous déployez le pare-feu ou plus tard à partir de la page **Paramètres DNS**.

## <a name="dns-servers"></a>Serveurs DNS

Un serveur DNS gère et résout les noms de domaine en adresses IP. Par défaut, le Pare-feu Azure utilise Azure DNS pour la résolution de noms. Le paramètre **Serveur DNS** vous permet de configurer vos propres serveurs DNS pour la résolution de noms du Pare-feu Azure. Vous pouvez configurer un ou plusieurs serveurs.

> [!NOTE]
> Pour les Pare-feu Azure gérés à l’aide d’Azure Firewall Manager, les paramètres DNS sont configurés dans la stratégie de Pare-feu Azure associée.

### <a name="configure-custom-dns-servers---azure-portal"></a>Configurer des serveurs DNS personnalisés – Portail Azure

1. Sous **Paramètres** du pare-feu Azure, sélectionnez **Paramètres DNS**.
2. Sous **Serveurs DNS**, vous pouvez taper ou ajouter des serveurs DNS existants qui ont été précédemment spécifiés dans votre réseau virtuel.
3. Sélectionnez **Enregistrer**.
4. Le pare-feu dirige désormais le trafic DNS vers le ou les serveurs DNS spécifiés pour la résolution de noms.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Serveurs DNS":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Configurer des serveurs DNS personnalisés – Azure CLI

L’exemple suivant met à jour le Pare-feu Azure avec des serveurs DNS personnalisés à l’aide d’Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> La commande `az network firewall` nécessite l’installation de l’extension Azure CLI `azure-firewall` . Celle-ci peut être installée à l’aide de la commande `az extension add --name azure-firewall`. 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Configurer des serveurs DNS personnalisés – Azure PowerShell

L’exemple suivant met à jour le Pare-feu Azure avec des serveurs DNS personnalisés à l’aide d’Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>Proxy DNS

Vous pouvez configurer le Pare-feu Azure pour qu’il agisse comme proxy DNS. Un proxy DNS agit comme un intermédiaire pour les requêtes DNS des machines virtuelles clientes à un serveur DNS. Si vous configurez un serveur DNS personnalisé, vous devez activer le proxy DNS pour éviter les incompatibilités de résolution DNS et activer le filtrage de nom de domaine complet dans les règles de réseau.

Si vous n’activez pas le proxy DNS, les requêtes DNS du client peuvent se déplacer vers un serveur DNS à une heure différente ou retourner une réponse différente de celle du pare-feu. Le proxy DNS place le Pare-feu Azure sur le trajet des demandes du client pour éviter toute incohérence.

Il existe deux types de fonctions de mise en cache qui interviennent quand le Pare-feu Azure est un proxy DNS :

- Cache positif : la résolution DNS est réussie. Le pare-feu utilise la durée de vie (Time-to-Live, TTL) du paquet ou de l’objet. 

- Cache négatif : la résolution DNS ne produit aucune réponse ou résolution. Le pare-feu met en cache ces informations pendant une heure.

Le proxy DNS stocke toutes les adresses IP résolues à partir de noms de domaine complets dans des règles de réseau. Nous vous recommandons d’utiliser des noms de domaine complets qui se résolvent en une seule adresse IP.  

### <a name="dns-proxy-configuration"></a>Configuration du proxy DNS

La configuration du proxy DNS nécessite trois étapes :
1. Activez le proxy DNS dans les paramètres DNS du Pare-feu Azure.
2. Éventuellement, configurez votre serveur DNS personnalisé ou utilisez la valeur par défaut fournie.
3. Enfin, vous devez configurer l’adresse IP privée du Pare-feu Azure en tant qu’adresse DNS personnalisée dans les paramètres du serveur DNS de votre réseau virtuel. Cela garantit que le trafic DNS est dirigé vers le Pare-feu Azure.

#### <a name="configure-dns-proxy---azure-portal"></a>Configurer le proxy DNS – Portail Azure

Pour configurer le proxy DNS, vous devez configurer le paramètre des serveurs DNS de votre réseau virtuel pour utiliser l’adresse IP privée du pare-feu. Ensuite, activez le proxy DNS dans les **paramètres DNS** du Pare-feu Azure.

##### <a name="configure-virtual-network-dns-servers"></a>Configurer les serveurs DNS du réseau virtuel 

1. Sélectionnez le réseau virtuel dans lequel le trafic DNS sera routé via le Pare-feu Azure.
2. Sous **Paramètres**, sélectionnez **Serveurs DNS**.
3. Sélectionnez **Personnalisé** sous **Serveurs DNS**.
4. Saisissez l’adresse IP privée du pare-feu.
5. Sélectionnez **Enregistrer**.
6. Redémarrez les machines virtuelles connectées au réseau virtuel, afin que les nouveaux paramètres de serveur DNS leur soient assignés. Les machines virtuelles continuent d’utiliser leurs paramètres DNS actifs jusqu’à ce qu’elles soient redémarrés.

##### <a name="enable-dns-proxy"></a>Activer le proxy DNS

1. Sélectionnez votre pare-feu Azure.
2. Sous **Paramètres**, sélectionnez **Paramètres DNS**.
3. Le **proxy DNS** est désactivé par défaut. Lorsqu’il est activé, le pare-feu écoute le port 53 et transfère les requêtes DNS aux serveurs DNS configurés.
4. Vérifiez la configuration des **serveurs DNS** pour vous assurer que les paramètres sont adaptés à votre environnement.
5. Sélectionnez **Enregistrer**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Proxy DNS":::

#### <a name="configure-dns-proxy---azure-cli"></a>Configurer le proxy DNS – Azure CLI

Vous pouvez utiliser Azure CLI pour configurer les paramètres du proxy DNS dans le Pare-feu Azure, et mettre à jour les réseaux virtuels afin qu’ils utilisent le Pare-feu Azure comme serveur DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurer les serveurs DNS du réseau virtuel

Cet exemple configure le réseau virtuel pour utiliser le Pare-feu Azure comme serveur DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Activer le proxy DNS

Cet exemple active la fonctionnalité de proxy DNS dans le Pare-feu Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Configurer le proxy DNS – Azure PowerShell

Vous pouvez utiliser Azure PowerShell pour configurer les paramètres du proxy DNS dans le Pare-feu Azure, et mettre à jour les réseaux virtuels afin qu’ils utilisent le Pare-feu Azure comme serveur DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurer les serveurs DNS du réseau virtuel

 Cet exemple configure le réseau virtuel pour utiliser le Pare-feu Azure comme serveur DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Activer le proxy DNS

Cet exemple active la fonctionnalité de proxy DNS dans le Pare-feu Azure.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Étapes suivantes

[Filtrage de nom de domaine complet dans les règles de réseau](fqdn-filtering-network-rules.md)
