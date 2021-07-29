---
title: Synchroniser le paramétrage des serveurs DNS du réseau virtuel sur le cluster virtuel SQL Managed Instance
description: Découvrez comment synchroniser le paramétrage des serveurs DNS du réseau virtuel sur le cluster virtuel SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 276b4033dc2a5c157245448453b72a8074b79702
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110707148"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>Synchroniser le paramétrage des serveurs DNS du réseau virtuel sur le cluster virtuel SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article explique quand et comment synchroniser le paramétrage des serveurs DNS du réseau virtuel sur le cluster virtuel SQL Managed Instance.

## <a name="when-to-synchronize-the-dns-setting"></a>Quand synchroniser le paramètre DNS

Quelques scénarios (comme le courrier de base de données, les serveurs liés à d’autres instances SQL dans votre cloud ou environnement hybride) nécessitent des noms d’hôte privés pour être résolus depuis SQL Managed Instance. Si tel est le cas, vous devez configurer un DNS personnalisé dans Azure. Pour plus d’informations, consultez [Configurer un DNS personnalisé pour Azure SQL Managed Instance](custom-dns-configure.md).

Si cette modification est implémentée après la création du [cluster virtuel](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) qui héberge Managed Instance, vous devez synchroniser le paramétrage des serveurs DNS sur le cluster virtuel avec la configuration du réseau virtuel.

> [!IMPORTANT]
> La synchronisation du paramétrage des serveurs DNS affecte toutes les instances gérées hébergées dans le cluster virtuel.

## <a name="how-to-synchronize-the-dns-setting"></a>Comment synchroniser le paramètre DNS

### <a name="azure-rbac-permissions-required"></a>Autorisations Azure RBAC requises

L’utilisateur qui synchronise la configuration des serveurs DNS doit avoir l’un des rôles Azure suivants :

- Rôle de contributeur au niveau de l'abonnement, ou
- Rôle personnalisé avec l’autorisation suivante :
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Récupérez le réseau virtuel dans lequel le paramétrage des serveurs DNS a été mis à jour.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
Utilisez la commande PowerShell [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) pour synchroniser la configuration des serveurs DNS pour tous les clusters virtuels du sous-réseau.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

Récupérez le réseau virtuel dans lequel le paramétrage des serveurs DNS a été mis à jour.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Utilisez la commande Azure CLI [az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) pour synchroniser la configuration des serveurs DNS pour tous les clusters virtuels du sous-réseau.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la configuration d’un DNS personnalisé, consultez [Configurer un DNS personnalisé pour Azure SQL Managed Instance](custom-dns-configure.md).
- Pour obtenir une vue d’ensemble, consultez [Présentation d’Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).
