---
title: Paramètres de connectivité pour Azure SQL Database et Data Warehouse
description: Ce document explique le choix de la version de TLS ainsi que le proxy et le paramètre de redirection pour Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096681"
---
# <a name="azure-sql-connectivity-settings"></a>Paramètres de connectivité d’Azure SQL
> [!NOTE]
> Cet article s’applique à un serveur SQL Azure et aux bases de données SQL Database et SQL Data Warehouse créées sur le serveur SQL Azure. Par souci de simplicité, la base de données SQL est utilisée pour faire référence à SQL Database et SQL Data Warehouse.

> [!IMPORTANT]
> Cet article ne s’applique *pas* à **Azure SQL Database Managed Instance**

Cet article présente les paramètres qui contrôlent la connectivité à Azure SQL Database au niveau du serveur. Ces paramètres s’appliquent à **toutes** les bases de données SQL Database et SQL Data Warehouse associées au serveur.

> [!NOTE]
> Une fois ces paramètres appliqués, ils **prennent immédiatement effet** et peuvent entraîner une perte de connexion pour vos clients s’ils ne remplissent pas les conditions requises pour chaque paramètre.

Les paramètres de connectivité sont accessibles à partir du panneau **Pare-feux et réseaux virtuels**, comme illustré dans la capture d’écran ci-dessous :

 ![Capture d’écran des paramètres de connectivité][1]


## <a name="deny-public-network-access"></a>Refuser l’accès au réseau public
Dans le portail Azure, lorsque le paramètre **Refuser l’accès au réseau public** est défini sur **Oui**, seules les connexions via des points de terminaison privés sont autorisées. Lorsque ce paramètre est défini sur **Non**, les clients peuvent se connecter à l’aide du point de terminaison privé ou public.

Une fois le paramètre **Refuser l’accès au réseau public** défini sur **Oui**, les tentatives de connexion des clients à l’aide du point de terminaison public échouent avec l’erreur suivante :

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Modifier l’accès au réseau public via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Le script suivant nécessite le [module Azure PowerShell](/powershell/azure/install-az-ps).

Le script PowerShell suivant montre comment `Get` et `Set` la propriété **Accès au réseau public** au niveau du serveur logique :

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Modifier l’accès au réseau public via CLI
> [!IMPORTANT]
> Tous les scripts de cette section nécessitent [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI dans un interpréteur de commandes Bash
Le script CLI suivant montre comment modifier l’**Accès au réseau public** dans un interpréteur de commandes Bash :

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Stratégie de connexion
La [stratégie de connexion](sql-database-connectivity-architecture.md#connection-policy) détermine la façon dont les clients se connectent à Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Modifier la stratégie de connexion via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Le script suivant nécessite le [module Azure PowerShell](/powershell/azure/install-az-ps).

Le script PowerShell suivant montre comment modifier la stratégie de connexion à l’aide de PowerShell :

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Modifier la stratégie de connexion via Azure CLI
> [!IMPORTANT]
> Tous les scripts de cette section nécessitent [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI dans un interpréteur de commandes Bash
Le script CLI suivant montre comment modifier la stratégie de connexion dans un interpréteur de commandes Bash : 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI à partir d’une invite de commandes Windows
Le script CLI suivant montre comment modifier la stratégie de connexion à partir d’une invite de commandes Windows (avec Azure CLI installée).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Étapes suivantes
- Pour une vue d’ensemble du fonctionnement de la connectivité dans Azure SQL Database, voir [Architecture de la connectivité Azure SQL](sql-database-connectivity-architecture.md)
- Pour des informations sur le changement de la stratégie de connexion Azure SQL Database pour un serveur Azure SQL Database, consultez [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
