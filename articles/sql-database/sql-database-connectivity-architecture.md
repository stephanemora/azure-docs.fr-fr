---
title: Architecture de connectivité Azure SQL Database | Microsoft Docs
description: Ce document décrit l’architecture de connectivité Azure SQLDB dans et en dehors d’Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DhruvMsft
ms.author: dhruv
ms.reviewer: carlrab
manager: craigg
ms.date: 01/24/2018
ms.openlocfilehash: 66f558db713ab951864fe694f27f2e60d52e875a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064128"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Architecture de connectivité Azure SQL Database 

Cet article explique l’architecture de connectivité Azure SQL Database et comment les différents composants fonctionnent pour diriger le trafic vers votre instance Azure SQL Database. Ces composants de connectivité Azure SQL Database permettent de diriger le trafic réseau vers la base de données Azure avec des clients se connectant à partir d’Azure et d’autres se connectant en dehors d’Azure. Cet article comporte également des exemples de script permettant de modifier la façon dont la connectivité se produit et des considérations liées à la modification des paramètres de connectivité par défaut. 

## <a name="connectivity-architecture"></a>Architecture de connectivité

Le diagramme suivant donne une vue d’ensemble détaillée de l’architecture de connectivité Azure SQL Database.

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/architecture-overview.png)


Les étapes suivantes décrivent la manière dont une connexion est établie vers une base de données SQL Azure via l’équilibreur de charge logiciel et la passerelle Azure SQL Database.

- Les clients dans Azure ou en dehors d’Azure se connectent à l’équilibreur de charge logiciel, qui détient une adresse IP publique et écoute le port 1433.
- L’équilibreur de charge logiciel dirige le trafic vers la passerelle Azure SQL Database.
- La passerelle redirige le trafic vers l’intergiciel de proxy approprié.
- Ce dernier redirige le trafic vers la base de données SQL Azure appropriée.

> [!IMPORTANT]
> Chacun de ces composants dispose d’une protection contre les attaques par déni de service distribué (DDoS) intégrée au niveau du réseau et de la couche d’application.
>

## <a name="connectivity-from-within-azure"></a>Connectivité dans Azure

Si vous vous connectez à partir d’Azure, vos connexions ont une stratégie de connexion par **redirection** par défaut. Une stratégie de **redirection** signifie qu’une fois les connexions établies après la session TCP vers la base de données SQL Azure, la session du client est redirigée vers l’intergiciel de proxy en modifiant l’adresse (en la faisant passer de celle de la passerelle de base de données SQL Azure à celle de l’intergiciel de proxy). Ainsi, tous les paquets suivants flux se dirigent directement vers l’intergiciel de proxy en ignorant la passerelle Azure SQL Database. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connectivité en dehors d’Azure

Si vous vous connectez en dehors d’Azure, vos connexions disposent d’une stratégie de connexion de **proxy** par défaut. Une stratégie de **Proxy** signifie que la session TCP est établie via la passerelle Azure SQL Database et que tous les paquets suivants transitent via la passerelle. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

> [!IMPORTANT]
> Lorsque vous utilisez des points de terminaison de service avec Azure SQL Database, votre stratégie par défaut est **Proxy**. Pour activer la connectivité à l’intérieur de votre réseau virtuel, vous devez autoriser les connexions sortantes vers les adresses IP de passerelle Azure SQL Database spécifiées dans la liste ci-dessous. Si vous utilisez des points de terminaison de service, nous vous recommandons vivement de modifier votre stratégie de connexion et d’adopter une stratégie **Rediriger** afin de bénéficier de meilleures performances. La modification de votre stratégie de connexion pour adopter une stratégie **Rediriger** ne sera pas suffisante pour autoriser le trafic sortant de votre Groupe de sécurité réseau vers les adresses IP de passerelle Azure SQLDB répertoriées ci-dessous. Vous devez autoriser le trafic sortant vers toutes les adresses IP Azure SQLDB. Pour ce faire, vous pouvez vous aider des balises de service du Groupe de sécurité réseau. Pour plus d'informations, consultez la rubrique [Balises de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresses IP de la passerelle Azure SQL Database

Pour vous connecter à une base de données SQL Azure à partir de ressources locales, vous devez autoriser le trafic réseau sortant vers la passerelle Azure SQL Database pour votre région Azure. Les connexions transitent uniquement via la passerelle lors de la connexion en mode Proxy, qui est le mode par défaut lors de la connexion à partir de ressources locales.

Le tableau suivant répertorie les adresses IP principales et secondaires de la passerelle Azure SQL Database pour toutes les régions de données. Pour certaines régions, il existe deux adresses IP. Dans ces régions, l’adresse IP principale est l’adresse IP actuelle de la passerelle et la deuxième adresse IP est une adresse IP de basculement. L’adresse de basculement est l’adresse vers laquelle nous pouvons déplacer votre serveur pour maintenir la haute disponibilité du service. Pour ces régions, nous vous conseillons d’autoriser le trafic sortant vers les deux adresses IP. La deuxième adresse IP est détenue par Microsoft et n’écoute pas les services tant qu’elle n’est pas activée par Azure SQL Database de manière à accepter les connexions.

> [!IMPORTANT]
> Si vous vous connectez à partir d’Azure, votre stratégie de connexion sera par défaut **Rediriger** (sauf si vous utilisez des points de terminaison de service). Cela ne sera pas suffisant pour autoriser les adresses IP ci-dessous. Vous devez autoriser toutes les adresses IP Azure SQL Database. Si vous vous connectez à l’intérieur d’un réseau virtuel, vous pouvez utiliser les balises de service des groupes de sécurité réseau (NSG). Pour plus d'informations, consultez la rubrique [Balises de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

| Nom de la région | Adresse IP principale | Adresse IP secondaire |
| --- | --- |--- |
| Australie Est | 191.238.66.109 | 13.75.149.87 |
| Sud-Est de l’Australie | 191.239.192.109 | 13.73.109.251 |
| Brésil Sud | 104.41.11.5 | |
| Centre du Canada | 40.85.224.249 | |
| Est du Canada | 40.86.226.166 | |
| USA Centre | 23.99.160.139 | 13.67.215.62 |
| Asie Est | 191.234.2.139 | 52.175.33.150 |
| USA Est 1 | 191.238.6.43 | 40.121.158.30 |
| USA Est 2 | 191.239.224.107 | 40.79.84.180 * |
| Inde Centre | 104.211.96.159  | |
| Sud de l’Inde | 104.211.224.146  | |
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
| Ouest du Royaume-Uni | 51.141.8.11  | |
| USA Centre-Ouest | 13.78.145.25 | |
| Europe Ouest | 191.237.232.75 | 40.68.37.158 |
| USA Ouest 1 | 23.99.34.75 | 104.42.238.205 |
| USA Ouest 2 | 13.66.226.202  | |
||||

\* **REMARQUE :** *USA Est* a également l’adresse IP tertiaire `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Modifier la stratégie de connexion Azure SQL Database

Pour modifier la stratégie de connexion Azure SQL Database pour un serveur Azure SQL Database, utilisez la commande [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Si votre stratégie de connexion est définie sur **Proxy**, tous les paquets réseau transitent via la passerelle Azure SQL Database. Pour ce paramètre, vous devez autoriser le trafic sortant uniquement vers l’IP de la passerelle Azure SQL Database. L’utilisation d’un paramètre de **Proxy** offre plus de latence qu’un paramètre de **redirection**.
- Si votre stratégie de connexion repose sur le paramètre de **redirection**, tous les paquets réseau se dirigent directement vers le proxy de l’intergiciel. Pour ce paramètre, vous devez autoriser le trafic sortant vers plusieurs adresses IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script permettant de modifier les paramètres de connexion via PowerShell

> [!IMPORTANT]
> Ce script nécessite le [module Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

Le script PowerShell suivant montre comment modifier la stratégie de connexion.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script permettant de modifier les paramètres de connexion via Azure CLI

> [!IMPORTANT]
> Ce script nécessite [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

Le script CLI suivant montre comment modifier la stratégie de connexion.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations sur comment modifier la stratégie de connexion Azure SQL Database pour un serveur Azure SQL Database, consultez l’article [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Pour plus d’informations sur le comportement de connexion d’Azure SQL Database pour les clients qui utilisent ADO.NET version 4.5 ou ultérieure, consultez [Ports au-delà de 1433 pour ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Pour en savoir plus sur la vue d’ensemble du développement d’applications générales, consultez [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md).
