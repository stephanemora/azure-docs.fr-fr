---
title: Types de connexions Managed Instance
description: Découvrez les types de connexion d’instance managée.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: a7df26590ec1514edcab24a1af9d85048b332d92
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773647"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Types de connexion Azure SQL Database Managed Instance

Cet article explique comment les clients se connectent à Azure SQL Database Managed Instance en fonction du type de connexion. Des exemples de script permettant de changer les types de connexion sont fournis ci-dessous, ainsi que des considérations sur le changement des paramètres de connectivité par défaut.

## <a name="connection-types"></a>Types de connexion

Azure SQL Database Managed Instance prend en charge les deux types de connexion suivants :

- **Redirection (recommandée) :** les clients établissent les connexions directement sur le nœud qui héberge la base de données. Pour activer la connectivité à l’aide de la redirection, vous devez ouvrir les pare-feu et les groupes de sécurité réseau (NSG) pour autoriser l’accès sur les ports 1433 et 11000-11999. Les paquets étant directement dirigés vers la base de données, l’utilisation de la redirection offre des améliorations en matière de débit et de latence, par rapport à l’utilisation d’un proxy.
- **Proxy (valeur par défaut) :** dans ce mode, toutes les connexions utilisent un composant passerelle proxy. Pour activer la connectivité, seuls le port 1433 pour les réseaux privés et le port 3342 pour la connexion publique doivent être ouverts. Ce mode peut entraîner une latence plus élevée et un débit inférieur, en fonction de la nature de la charge de travail. Nous vous recommandons la stratégie de connexion Redirection sur la stratégie de connexion Proxy pour bénéficier d’une plus faible latence et d’un débit plus élevé.

## <a name="redirect-connection-type"></a>Type de connexion Redirection

Le type de connexion Redirection signifie qu’une fois que la session TCP au moteur SQL est établie, la session cliente obtient l’adresse IP virtuelle de destination du nœud de cluster virtuel à partir de l’équilibreur de charge. Les paquets suivants sont directement acheminés vers le nœud de cluster virtuel, en ignorant la passerelle. Le schéma suivant illustre ce flux de trafic :

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Actuellement, le type de connexion Redirection ne fonctionne que pour le point de terminaison privé. Quel que soit le paramètre de type de connexion, les connexions entrantes par le biais du point de terminaison public s’effectuent via un proxy.

## <a name="proxy-connection-type"></a>Type de connexion Proxy

Le type de connexion Proxy signifie que la session TCP est établie à l’aide de la passerelle et que tous les paquets suivants transitent par elle. Le schéma suivant illustre ce flux de trafic :

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Script pour changer les paramètres de type de connexion à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le script PowerShell suivant montre comment choisir le type de connexion Redirection pour une instance managée.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer une base de données dans une instance gérée](sql-database-managed-instance-get-started-restore.md)
- Découvrez comment [configurer un point de terminaison public sur une instance managée](sql-database-managed-instance-public-endpoint-configure.md)
- Apprenez-en davantage sur l’[architecture de connectivité d’une instance managée](sql-database-managed-instance-connectivity-architecture.md)