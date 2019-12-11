---
title: Architecture de connectivité
description: Ce document décrit l’architecture de connectivité Azure SQL pour les connexions de base de données à l’intérieur et à l’extérieur d’Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: 6f6c64acf814b39d38138ed0e6a9c6075b693c7d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707982"
---
# <a name="azure-sql-connectivity-architecture"></a>Architecture de la connectivité Azure SQL

Cet article décrit l’architecture de la connectivité Azure SQL Database et SQL Data Warehouse, et explique comment les différents composants fonctionnent pour diriger le trafic vers votre instance d’Azure SQL. Ces composants de connectivité permettent de diriger le trafic réseau vers Azure SQL Database ou SQL Data Warehouse, avec des clients se connectant à partir d'Azure et d’autres en dehors d’Azure. Cet article comporte également des exemples de script permettant de modifier la façon dont la connectivité se produit et des considérations liées à la modification des paramètres de connectivité par défaut.

## <a name="connectivity-architecture"></a>Architecture de connectivité

Le diagramme suivant donne une vue d’ensemble détaillée de l’architecture de connectivité Azure SQL Database.

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Les étapes suivantes expliquent comment une connexion est établie avec une base de données Azure SQL :

- Les clients se connectent à la passerelle, qui possède une adresse IP publique et écoute le port 1433.
- Cette passerelle, en fonction de la stratégie de connexion en place, redirige ou applique un proxy au trafic vers le cluster de bases de données approprié.
- À l'intérieur du cluster de bases de données, le trafic est transféré vers la base de données Azure SQL appropriée.

## <a name="connection-policy"></a>Stratégie de connexion

Azure SQL Database prend en charge les trois options suivantes pour la configuration de la stratégie de connexion d’un serveur SQL Database :

- **Redirection (recommandée) :** Les clients établissent des connexions directement au nœud qui héberge la base de données, ce qui permet de réduire la latence et d’améliorer les performances. Pour que les connexions utilisent ce mode, les clients doivent
   - Autoriser les communications entrantes et sortantes à partir du client vers toutes les adresses IP Azure de la région sur les ports de la plage comprise entre 11000 et 11999.  
   - Autorisez les communications entrantes et sortantes à partir du client vers les adresses IP de la passerelle Azure SQL Database sur le port 1433.

- **Proxy :** Dans ce mode, toutes les connexions sont traitées par proxy via les passerelles Azure SQL Database, ce qui permet d’augmenter la latence et de réduire le débit. Pour que les connexions utilisent ce mode, les clients doivent autoriser les communications entrantes et sortantes à partir du client vers les adresses IP de la passerelle Azure SQL Database sur le port 1433.

- **Par défaut :** stratégie de connexion en vigueur sur tous les serveurs après la création, sauf si vous remplacez explicitement la stratégie de connexion par `Proxy` ou `Redirect`. La stratégie par défaut est `Redirect` pour toutes les connexions clientes en provenance d’Azure (par exemple, à partir d’une machine virtuelle Azure) et `Proxy` pour toutes les connexions clientes externes (par exemple, les connexions à partir de votre station de travail locale).

 Nous recommandons vivement d’utiliser la stratégie de connexion `Redirect` sur la stratégie de connexion `Proxy` pour la latence la plus faible et le débit le plus élevé. Toutefois, vous devrez respecter les exigences supplémentaires pour autoriser le trafic réseau comme indiqué ci-dessus. Si le client est une machine virtuelle Azure, vous pouvez le faire à l’aide de groupes de sécurité réseau (NSG) avec des [étiquettes de service](../virtual-network/security-overview.md#service-tags). Si le client se connecte à partir d’une station de travail locale, vous devrez peut-être utiliser votre administrateur réseau pour autoriser le trafic réseau via votre pare-feu d’entreprise.

## <a name="connectivity-from-within-azure"></a>Connectivité dans Azure

Si vous vous connectez à partir d'Azure, la stratégie de connexion par défaut de vos connexions est `Redirect`. Une stratégie de `Redirect` signifie qu'après l'établissement de la session TCP avec la base de données Azure SQL, la session client est redirigée vers le cluster de base de données approprié avec une modification de l'adresse IP virtuelle de destination (celle de la passerelle Azure SQL Database est remplacée par celle du cluster). Tous les paquets suivants se dirigent ensuite directement vers le cluster, en contournant la passerelle Azure SQL Database. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connectivité en dehors d’Azure

Si vous vous connectez en dehors d'Azure, la stratégie de connexion par défaut de vos connexions est `Proxy`. Une stratégie de `Proxy` signifie que la session TCP est établie via la passerelle Azure SQL Database et que tous les paquets suivants transitent via la passerelle. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Vous pouvez également ouvrir les ports 14000-14999 pour activer la [connexion avec DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresses IP de la passerelle Azure SQL Database

Le tableau ci-dessous répertorie les adresses IP des passerelles par région. Pour vous connecter à une base de données SQL Azure, vous devez autoriser le trafic réseau en direction et en provenance de **toutes** les passerelles pour la région.

L’article suivant décrit comment migrer le trafic vers de nouvelles passerelles dans des régions spécifiques : [Migration du trafic des bases de données SQL Azure Database vers des passerelles plus récentes](sql-database-gateway-migration.md)


| Nom de la région          | Adresses IP de passerelle |
| --- | --- |
| Centre de l’Australie    | 20.36.105.0 |
| Australie Centre 2   | 20.36.113.0 |
| Australie Est       | 13.75.149.87, 40.79.161.1 |
| Sud-Est de l’Australie | 191.239.192.109, 13.73.109.251 |
| Brésil Sud         | 104.41.11.5, 191.233.200.14 |
| Centre du Canada       | 40.85.224.249      |
| Est du Canada          | 40.86.226.166      |
| USA Centre           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Chine orientale           | 139.219.130.35     |
| Chine Est 2         | 40.73.82.1         |
| Chine du Nord          | 139.219.15.17      |
| Chine Nord 2        | 40.73.50.0         |
| Asie Est            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| USA Est              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA Est 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| France Centre       | 40.79.137.0, 40.79.129.1 |
| Centre de l’Allemagne      | 51.4.144.100       |
| Nord-Est de l’Allemagne   | 51.5.144.179       |
| Inde Centre        | 104.211.96.159     |
| Sud de l’Inde          | 104.211.224.146    |
| Inde Ouest           | 104.211.160.80     |
| Japon Est           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| OuJapon Est           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Centre de la Corée        | 52.231.32.42       |
| Corée du Sud          | 52.231.200.86      |
| Centre-Nord des États-Unis     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europe Nord         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Afrique du Sud Nord   | 102.133.152.0      |
| Afrique du Sud Ouest    | 102.133.24.0       |
| États-Unis - partie centrale méridionale     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Asie Sud-Est      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Émirats arabes unis Centre          | 20.37.72.64        |
| Émirats arabes unis Nord            | 65.52.248.0        |
| Sud du Royaume-Uni             | 51.140.184.11      |
| Ouest du Royaume-Uni              | 51.141.8.11        |
| Centre-USA Ouest      | 13.78.145.25       |
| Europe Ouest          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| USA Ouest              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| USA Ouest 2            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>Modifier la stratégie de connexion Azure SQL Database

Pour changer la stratégie de connexion Azure SQL Database pour un serveur Azure SQL Database, utilisez la commande [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Si votre stratégie de connexion est définie sur `Proxy`, tous les paquets réseau transitent via la passerelle Azure SQL Database. Pour ce paramètre, vous devez autoriser le trafic sortant uniquement vers l’IP de la passerelle Azure SQL Database. L'utilisation d'un paramètre de `Proxy` offre plus de latence qu'un paramètre de `Redirect`.
- Si votre stratégie de connexion repose sur le paramètre de `Redirect`, tous les paquets réseau se dirigent directement vers le cluster de bases de données. Pour ce paramètre, vous devez autoriser le trafic sortant vers plusieurs adresses IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script permettant de modifier les paramètres de connexion via PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Le script suivant nécessite le [module Azure PowerShell](/powershell/azure/install-az-ps).

Le script PowerShell suivant montre comment modifier la stratégie de connexion.

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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script permettant de modifier les paramètres de connexion via Azure CLI

> [!IMPORTANT]
> Ce script nécessite [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI dans un interpréteur de commandes Bash

> [!IMPORTANT]
> Ce script nécessite [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Le script CLI suivant montre comment modifier la stratégie de connexion dans un interpréteur de commandes Bash.

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

> [!IMPORTANT]
> Ce script nécessite [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

- Pour des informations sur le changement de la stratégie de connexion Azure SQL Database pour un serveur Azure SQL Database, consultez [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Pour plus d’informations sur le comportement de connexion d’Azure SQL Database pour les clients qui utilisent ADO.NET version 4.5 ou ultérieure, consultez [Ports au-delà de 1433 pour ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Pour en savoir plus sur la vue d’ensemble du développement d’applications générales, consultez [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md).
