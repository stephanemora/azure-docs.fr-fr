---
title: Diriger le trafic Azure vers Azure SQL Database et SQL Data Warehouse | Microsoft Docs
description: Ce document décrit l'architecture de la connectivité Azure SQL Database et SQL Data Warehouse dans et en dehors d'Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 02/06/2019
ms.openlocfilehash: 5ce8464de552fb228b961af199e4b03e645478a2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809978"
---
# <a name="azure-sql-connectivity-architecture"></a>Architecture de la connectivité Azure SQL

Cet article décrit l'architecture de la connectivité Azure SQL Database et SQL Data Warehouse, et explique comment les différents composants fonctionnent pour diriger le trafic vers votre instance d'Azure SQL. Ces composants de connectivité permettent de diriger le trafic réseau vers Azure SQL Database ou SQL Data Warehouse, avec des clients se connectant à partir d'Azure et d'autres en dehors d'Azure. Cet article comporte également des exemples de script permettant de modifier la façon dont la connectivité se produit et des considérations liées à la modification des paramètres de connectivité par défaut.

> [!IMPORTANT]
> **[Changement à venir] Pour les connexions des points de terminaison de service avec les serveurs Azure SQL, le comportement de connectivité `Default` est remplacé par `Redirect`.**
> Les clients sont invités à créer de nouveaux serveurs et à configurer les serveurs existants avec un type de connexion défini explicitement sur Redirection (recommandé) ou Proxy selon l’architecture de leur connectivité.
>
> Pour éviter que ce changement n'entraîne une interruption lorsque la connectivité s'effectue via un point de terminaison de service dans les environnements existants, nous utilisons la télémétrie suivante :
> - Pour les serveurs détectés auxquels on accédait auparavant par l'intermédiaire de points de terminaison de service, nous passons au type de connexion `Proxy`.
> - Pour tous les autres serveurs, nous passons au type de connexion `Redirect`.
>
> Les utilisateurs des points de terminaison de service peuvent encore être affectés dans les scénarios suivants :
> - L'application se connecte rarement à un serveur existant, de sorte que notre télémétrie n'a pas capturé les informations relatives à cette application.
> - La logique de déploiement automatisé crée un serveur SQL Database en supposant que le comportement par défaut des connexions aux points de terminaison de service est `Proxy`
>
> Si les connexions des points de terminaison de service n'ont pas pu être établies avec Azure SQL Server et que vous pensez être affecté par ce changement, vérifiez que le type de connexion est explicitement défini sur `Redirect`. Si tel est le cas, vous devez ouvrir les règles de pare-feu VM et les groupes de sécurité réseau (NSG) à toutes les adresses IP Azure de la région qui appartiennent à la [balise de service​​](../virtual-network/security-overview.md#service-tags) SQL pour les ports 11000-12000. Si cette option ne vous convient pas, basculez explicitement le serveur vers `Proxy`.
> [!NOTE]
> Cette rubrique s’applique aux serveurs Azure SQL Database qui hébergent des bases de données uniques et des pools élastiques, ainsi que des bases de données SQL Data Warehouse. Par souci de simplicité, la base de données SQL est utilisée pour faire référence à SQL Database et SQL Data Warehouse.

## <a name="connectivity-architecture"></a>Architecture de connectivité

Le diagramme suivant donne une vue d’ensemble détaillée de l’architecture de connectivité Azure SQL Database.

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Les étapes suivantes expliquent comment une connexion est établie avec une base de données SQL Azure :

- Les clients se connectent à la passerelle, qui possède une adresse IP publique et écoute le port 1433.
- Cette passerelle, en fonction de la stratégie de connexion en place, redirige ou applique un proxy au trafic vers le cluster de bases de données approprié.
- À l'intérieur du cluster de bases de données, le trafic est transféré vers la base de données SQL Azure appropriée.

## <a name="connection-policy"></a>Stratégie de connexion

Azure SQL Database prend en charge les trois options suivantes pour la configuration de la stratégie de connexion d’un serveur SQL Database :

- **Redirection (recommandée) :** les clients établissent les connexions directement sur le nœud qui héberge la base de données. Pour activer la connectivité, les clients doivent autoriser les règles de pare-feu de trafic sortant pour toutes les adresses IP Azure de la région (avec groupes de sécurité réseau et [balises de service](../virtual-network/security-overview.md#service-tags)) pour les ports 11000-12000, et pas seulement les adresses IP de la passerelle Azure SQL Database sur le port 1433. Étant donné que les paquets vont directement à la base de données, la latence et le débit améliorent les performances.
- **Proxy :** dans ce mode, toutes les connexions sont traitées via les passerelles Azure SQL Database. Pour activer la connectivité, le client doit créer des règles de pare-feu de trafic sortant qui autorisent uniquement les adresses IP de la passerelle Azure SQL Database (généralement deux adresses IP par région). Ce mode peut entraîner une latence plus élevée et un débit inférieur, en fonction de la nature de la charge de travail. Nous vous recommandons la stratégie de connexion `Redirect` sur la stratégie de connexion `Proxy` pour bénéficier d'une plus faible latence et d'un débit plus élevé.
- **Par défaut :** stratégie de connexion en vigueur sur tous les serveurs après la création, sauf si vous remplacez explicitement la stratégie de connexion par `Proxy` ou `Redirect`. La stratégie effective varie selon que les connexions proviennent d'Azure (`Redirect`) ou de l'extérieur d'Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Connectivité dans Azure

Si vous vous connectez à partir d'Azure, la stratégie de connexion par défaut de vos connexions est `Redirect`. Une stratégie de `Redirect` signifie qu'après l'établissement de la session TCP avec la base de données SQL Azure, la session client est redirigée vers le cluster de base de données approprié avec une modification de l'adresse IP virtuelle de destination (celle de la passerelle Azure SQL Database est remplacée par celle du cluster). Tous les paquets suivants se dirigent ensuite directement vers le cluster, en contournant la passerelle Azure SQL Database. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connectivité en dehors d’Azure

Si vous vous connectez en dehors d'Azure, la stratégie de connexion par défaut de vos connexions est `Proxy`. Une stratégie de `Proxy` signifie que la session TCP est établie via la passerelle Azure SQL Database et que tous les paquets suivants transitent via la passerelle. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresses IP de la passerelle Azure SQL Database

Pour vous connecter à une base de données SQL Azure à partir de ressources locales, vous devez autoriser le trafic réseau sortant vers la passerelle Azure SQL Database pour votre région Azure. Les connexions transitent uniquement via la passerelle en cas d'utilisation du mode `Proxy`, qui est le mode par défaut lors d'une connexion à partir de ressources locales.

Le tableau suivant répertorie les adresses IP principales et secondaires de la passerelle Azure SQL Database pour toutes les régions de données. Pour certaines régions, il existe deux adresses IP. Dans ces régions, l’adresse IP principale est l’adresse IP actuelle de la passerelle et la deuxième adresse IP est une adresse IP de basculement. L’adresse de basculement est l’adresse vers laquelle nous pouvons déplacer votre serveur pour maintenir la haute disponibilité du service. Pour ces régions, nous vous conseillons d’autoriser le trafic sortant vers les deux adresses IP. La deuxième adresse IP est détenue par Microsoft et n’écoute pas les services tant qu’elle n’est pas activée par Azure SQL Database de manière à accepter les connexions.

| Nom de la région | Adresse IP principale | Adresse IP secondaire |
| --- | --- |--- |
| Australie Est | 13.75.149.87 | 40.79.161.1 |
| Sud-Est de l’Australie | 191.239.192.109 | 13.73.109.251 |
| Brésil Sud | 104.41.11.5 | |
| Centre du Canada | 40.85.224.249 | |
| Est du Canada | 40.86.226.166 | |
| USA Centre | 23.99.160.139 | 13.67.215.62 |
| Chine Est 1 | 139.219.130.35 | |
| Chine Est 2 | 40.73.82.1 | |
| Chine Nord 1 | 139.219.15.17 | |
| Chine Nord 2 | 40.73.50.0 | |
| Asie Est | 191.234.2.139 | 52.175.33.150 |
| USA Est 1 | 191.238.6.43 | 40.121.158.30 |
| USA Est 2 | 191.239.224.107 | 40.79.84.180 * |
| France Centre | 40.79.137.0 | 40.79.129.1 |
| Centre de l’Allemagne | 51.4.144.100 | |
| Nord-Est de l’Allemagne | 51.5.144.179 | |
| Inde Centre | 104.211.96.159 | |
| Sud de l’Inde | 104.211.224.146 | |
| Inde Ouest | 104.211.160.80 | |
| Japon Est | 191.237.240.43 | 13.78.61.196 |
| Japon Ouest | 191.238.68.11 | 104.214.148.156 |
| Centre de la Corée | 52.231.32.42 | |
| Corée du Sud | 52.231.200.86 |  |
| USA Centre Nord | 23.98.55.75 | 23.96.178.199 |
| Europe Nord | 191.235.193.75 | 40.113.93.91 |
| USA Centre Sud | 23.98.162.75 | 13.66.62.124 |
| Asie Sud-Est | 23.100.117.95 | 104.43.15.0 |
| Nord du Royaume-Uni | 13.87.97.210 | |
| Sud du Royaume-Uni 1 | 51.140.184.11 | |
| Sud du Royaume-Uni 2 | 13.87.34.7 | |
| Ouest du Royaume-Uni | 51.141.8.11 | |
| USA Centre-Ouest | 13.78.145.25 | |
| Europe Ouest | 191.237.232.75 | 40.68.37.158 |
| USA Ouest 1 | 23.99.34.75 | 104.42.238.205 |
| USA Ouest 2 | 13.66.226.202 | |
||||

\* **REMARQUE :** *USA Est 2* possède également l'adresse IP tertiaire `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Modifier la stratégie de connexion Azure SQL Database

Pour modifier la stratégie de connexion Azure SQL Database pour un serveur Azure SQL Database, utilisez la commande [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Si votre stratégie de connexion est définie sur `Proxy`, tous les paquets réseau transitent via la passerelle Azure SQL Database. Pour ce paramètre, vous devez autoriser le trafic sortant uniquement vers l’IP de la passerelle Azure SQL Database. L'utilisation d'un paramètre de `Proxy` offre plus de latence qu'un paramètre de `Redirect`.
- Si votre stratégie de connexion repose sur le paramètre de `Redirect`, tous les paquets réseau se dirigent directement vers le cluster de bases de données. Pour ce paramètre, vous devez autoriser le trafic sortant vers plusieurs adresses IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script permettant de modifier les paramètres de connexion via PowerShell

> [!IMPORTANT]
> Ce script nécessite le [module Azure PowerShell](/powershell/azure/install-az-ps).

Le script PowerShell suivant montre comment modifier la stratégie de connexion.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzureRmSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzureRmResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzureRmResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script permettant de modifier les paramètres de connexion via Azure CLI

> [!IMPORTANT]
> Ce script nécessite [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Le script CLI suivant montre comment modifier la stratégie de connexion.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations sur comment modifier la stratégie de connexion Azure SQL Database pour un serveur Azure SQL Database, consultez l’article [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Pour plus d’informations sur le comportement de connexion d’Azure SQL Database pour les clients qui utilisent ADO.NET version 4.5 ou ultérieure, consultez [Ports au-delà de 1433 pour ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Pour en savoir plus sur la vue d’ensemble du développement d’applications générales, consultez [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md).
