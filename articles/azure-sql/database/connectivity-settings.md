---
title: Paramètres de connectivité pour Azure SQL Database et Data Warehouse
description: Ce document explique le choix de la version du protocole TLS ainsi que le proxy vs. Paramètre de redirection pour Azure SQL Database et Azure Synapse Analytics
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: eab9004b37da83b5d571ff700c32215354286c94
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443847"
---
# <a name="azure-sql-connectivity-settings"></a>Paramètres de connectivité d’Azure SQL
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Cet article présente les paramètres qui contrôlent la connectivité au serveur pour Azure SQL Database et Azure Synapse Analytics. Ces paramètres s’appliquent à **toutes** les bases de données SQL Database et Azure Synapse associées au serveur.

> [!IMPORTANT]
> Cet article ne s’applique *pas* à **Azure SQL Managed Instance**

Les paramètres de connectivité sont accessibles à partir de l’écran **Pare-feu et réseaux virtuels**, comme illustré dans la capture d’écran suivante :

 ![Capture d’écran des paramètres de connectivité][1]

> [!NOTE]
> Une fois ces paramètres appliqués, ils **prennent immédiatement effet** et peuvent entraîner une perte de connexion pour vos clients s’ils ne remplissent pas les conditions requises pour chaque paramètre.

## <a name="deny-public-network-access"></a>Refuser l’accès au réseau public

Lorsque le paramètre **Refuser l’accès au réseau public** est défini sur **Oui**, seules les connexions via des points de terminaison privés sont autorisées. Lorsque ce paramètre est défini sur **Non** (par défaut), les clients peuvent se connecter à l’aide de points de terminaison publics (règles de pare-feu basées sur l’adresse IP, règles de pare-feu basées sur un réseau virtuel) ou de points de terminaison privés (avec Private Link), comme indiqué dans la [vue d’ensemble de l’accès réseau](network-access-controls-overview.md). 

 ![Capture d’écran de la connectivité avec l’accès refusé au réseau public][2]

Toute tentative de définition du paramètre **Refuser l’accès au réseau public** sur **Oui** sans aucun point de terminaison privé existant au niveau du serveur logique échouera avec un message d’erreur semblable à celui-ci :  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Quand le paramètre **Refuser l’accès au réseau public** a la valeur **Oui**, seules les connexions via des points de terminaison privés sont autorisées et toutes les connexions via des points de terminaison publics sont refusées avec un message d’erreur semblable au suivant :  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Lorsque le paramètre **Refuser l’accès au réseau public** est défini sur **Oui**, toute tentative d’ajout ou de mise à jour des règles de pare-feu sera refusée avec un message d’erreur semblable à celui-ci :

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Modifier l’accès au réseau public via PowerShell

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Le script suivant nécessite le [module Azure PowerShell](/powershell/azure/install-az-ps).

Le script PowerShell suivant montre comment `Get` et `Set` la propriété **Accès au réseau public** au niveau du serveur :

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
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

## <a name="minimal-tls-version"></a>Version TLS minimale 

Le paramètre de version [TLS (Transport Layer Security)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) minimale permet aux clients de contrôler la version de TLS utilisée par leur instance Azure SQL Database.

À l’heure actuelle, nous prenons en charge TLS 1.0, 1.1 et 1.2. La définition d’une version TLS minimale garantit que les versions TLS ultérieures plus récentes sont prises en charge. Par exemple, si le choix se porte sur une version TLS supérieure à 1.1, seules les connexions avec TLS 1.1 et 1.2 sont acceptées et TLS 1.0 est rejeté. Après le test pour confirmer que vos applications la prennent en charge, nous vous recommandons de définir la version TLS minimale sur 1.2, car elle comprend des correctifs pour les vulnérabilités détectées dans les versions précédentes et est la version la plus récente de TLS prise en charge dans Azure SQL Database.

> [!IMPORTANT]
> Par défaut, la version minimale de TLS autorise toutes les versions. Toutefois, une fois que vous avez appliqué une version de TLS, il n’est pas possible de rétablir le paramétrage par défaut.

Pour les clients disposant d’applications qui reposent sur des versions antérieures de TLS, nous vous recommandons de définir la version TLS minimale conformément aux exigences de vos applications. Pour les clients qui s’appuient sur des applications pour se connecter à l’aide d’une connexion non chiffrée, nous vous recommandons de ne pas définir de version TLS minimale.

Pour plus d’informations, consultez [Considérations relatives au protocole TLS pour la connectivité de SQL Database](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Une fois définie la version TLS minimale, les tentatives de connexion à partir de clients qui utilisent une version TLS inférieure à la version TLS minimale du serveur échouent avec l’erreur suivante :

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Définir la version TLS minimale par le biais de PowerShell

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Le script suivant nécessite le [module Azure PowerShell](/powershell/azure/install-az-ps).

Le script PowerShell suivant montre comment `Get` et `Set` la propriété **Version TLS minimale** au niveau du serveur logique :

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Définir la version TLS minimale par le biais d’Azure CLI

> [!IMPORTANT]
> Tous les scripts de cette section nécessitent [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI dans un interpréteur de commandes Bash

Le script CLI suivant montre comment changer le paramètre **Version TLS minimale** dans un interpréteur de commandes Bash :

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>Modifier la stratégie de connexion

La [stratégie de connexion](connectivity-architecture.md#connection-policy) détermine la façon dont les clients se connectent à Azure SQL Database.


## <a name="change-connection-policy-via-powershell"></a>Modifier la stratégie de connexion via PowerShell

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Le script suivant nécessite le [module Azure PowerShell](/powershell/azure/install-az-ps).

Le script PowerShell suivant montre comment modifier la stratégie de connexion à l’aide de PowerShell :

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

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

- Pour obtenir une vue d’ensemble du fonctionnement de la connectivité dans Azure SQL Database, consultez [Architecture de connectivité](connectivity-architecture.md)
- Pour plus d’informations sur la façon de modifier la stratégie de connexion d’un serveur, consultez [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
