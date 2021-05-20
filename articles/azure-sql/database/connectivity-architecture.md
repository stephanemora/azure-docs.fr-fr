---
title: Architecture de connectivité Azure SQL Database
description: Ce document décrit l’architecture de connectivité Azure SQL Database pour les connexions de base de données à l’intérieur et à l’extérieur d’Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: 1e0015887482023097cfb98ff244c3b53c808a85
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685834"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Architecture de connectivité Azure SQL Database et Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Cet article décrit l’architecture des différents composants qui dirigent le trafic réseau vers Azure SQL Database ou Azure Synapse Analytics. Il explique également les différentes stratégies de connexion et la façon dont elle affecte les clients qui se connectent à partir d’Azure et ceux qui se connectent en dehors d’Azure.

> [!IMPORTANT]
> Cet article ne s'applique *pas* à **Azure SQL Managed Instance**. Consultez [Architecture de connectivité d’une instance gérée](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Architecture de connectivité

Le diagramme suivant donne une vue d’ensemble de haut niveau de l’architecture de connectivité.

![Diagramme illustrant une vue d’ensemble de l’architecture de connectivité.](./media/connectivity-architecture/connectivity-overview.png)

Les étapes suivantes expliquent comment une connexion est établie avec Azure SQL Database :

- Les clients se connectent à la passerelle, qui possède une adresse IP publique et écoute le port 1433.
- Cette passerelle, en fonction de la stratégie de connexion en place, redirige ou applique un proxy au trafic vers le cluster de bases de données approprié.
- À l'intérieur du cluster de bases de données, le trafic est transféré vers la base de données appropriée.

## <a name="connection-policy"></a>Stratégie de connexion

Les serveurs dans SQL Database et Azure Synapse prennent en charge les trois options suivantes pour la configuration de la stratégie de connexion du serveur :

- **Redirection (recommandée) :** Les clients établissent des connexions directement au nœud qui héberge la base de données, ce qui permet de réduire la latence et d’améliorer le débit. Pour que les connexions utilisent ce mode, les clients doivent :
  - Autoriser les communications sortantes du client vers toutes les adresses IP Azure SQL de la région sur les ports de la plage comprise entre 11000 et 11999. Utilisez les balises de service pour SQL afin de faciliter la gestion.  
  - Autoriser les communications sortantes à partir du client vers les adresses IP de la passerelle Azure SQL Database sur le port 1433.

- **Proxy :** Dans ce mode, toutes les connexions sont traitées par proxy via les passerelles Azure SQL Database, ce qui permet d’augmenter la latence et de réduire le débit. Pour que les connexions utilisent ce mode, les clients doivent autoriser les communications sortantes à partir du client vers les adresses IP de la passerelle Azure SQL Database sur le port 1433.

- **Par défaut :** stratégie de connexion en vigueur sur tous les serveurs après la création, sauf si vous remplacez explicitement la stratégie de connexion par `Proxy` ou `Redirect`. La stratégie par défaut est `Redirect` pour toutes les connexions clientes en provenance d’Azure (par exemple, à partir d’une machine virtuelle Azure) et `Proxy` pour toutes les connexions clientes externes (par exemple, les connexions à partir de votre station de travail locale).

Nous vous recommandons la stratégie de connexion `Redirect` sur la stratégie de connexion `Proxy` pour bénéficier d'une plus faible latence et d'un débit plus élevé. Toutefois, vous devrez respecter les exigences supplémentaires pour autoriser le trafic réseau comme indiqué ci-dessus. Si le client est une machine virtuelle Azure, vous pouvez le faire à l’aide de groupes de sécurité réseau (NSG) avec des [étiquettes de service](../../virtual-network/network-security-groups-overview.md#service-tags). Si le client se connecte à partir d’une station de travail locale, vous devrez peut-être utiliser votre administrateur réseau pour autoriser le trafic réseau via votre pare-feu d’entreprise.

## <a name="connectivity-from-within-azure"></a>Connectivité dans Azure

Si vous vous connectez à partir d'Azure, la stratégie de connexion par défaut de vos connexions est `Redirect`. Une stratégie de `Redirect` signifie qu'après l'établissement de la session TCP avec Azure SQL Database, la session client est redirigée vers le cluster de base de données approprié avec une modification de l'adresse IP virtuelle de destination, de celle de la passerelle Azure SQL Database vers celle du cluster. Tous les paquets suivants se dirigent ensuite directement vers le cluster, en contournant la passerelle Azure SQL Database. Le schéma suivant illustre ce flux de trafic :

![Présentation de l’architecture](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connectivité en dehors d’Azure

Si vous vous connectez en dehors d'Azure, la stratégie de connexion par défaut de vos connexions est `Proxy`. Une stratégie de `Proxy` signifie que la session TCP est établie via la passerelle Azure SQL Database et que tous les paquets suivants transitent via la passerelle. Le schéma suivant illustre ce flux de trafic :

![Diagramme montrant la façon dont la session TCP est établie via la passerelle Azure SQL Database et tous les paquets suivants transitent via la passerelle.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Vous pouvez également ouvrir les ports TCP 1434 et 14000-14999 pour activer la [connexion avec DAC](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Adresses IP de la passerelle

Le tableau ci-dessous répertorie les adresses IP des passerelles par région. Pour vous connecter à SQL Azure Database ou Azure Synapse, vous devez autoriser le trafic réseau en direction et en provenance de **toutes** les passerelles pour la région.

L’article suivant décrit comment migrer le trafic vers de nouvelles passerelles dans des régions spécifiques : [Migration du trafic des bases de données SQL Azure Database vers des passerelles plus récentes](gateway-migration.md)

| Nom de la région          | Adresses IP de la passerelle |
| --- | --- |
| Centre de l’Australie    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Centre de l’Australie 2   | 20.36.113.0, 20.36.112.6 |
| Australie Est       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Sud-Est de l’Australie | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 |
| Brésil Sud         | 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Centre du Canada       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Est du Canada          | 40.86.226.166, 52.242.30.154, 40.69.105.9, 40.69.105.10 |
| USA Centre           | 13.67.215.62, 52.182.137.15, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| Chine orientale           | 139.219.130.35     |
| Chine orientale 2         | 40.73.82.1         |
| Chine du Nord          | 139.219.15.17      |
| Chine Nord 2        | 40.73.50.0         |
| Asie Est            | 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| USA Est              | 40.121.158.30, 40.79.153.12, 40.78.225.32 |
| USA Est 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0,  191.239.224.107, 104.208.150.3,  40.70.144.193 |
| France Centre       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| France Sud         | 40.79.177.0, 40.79.177.10, 40.79.177.12 |
| Centre de l’Allemagne      | 51.4.144.100       |
| Nord-Est de l’Allemagne   | 51.5.144.179       |
| Allemagne Centre-Ouest | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Inde Centre        | 104.211.96.159, 104.211.86.30, 104.211.86.31 |
| Sud de l’Inde          | 104.211.224.146    |
| Inde Ouest           | 104.211.160.80, 104.211.144.4 |
| Japon Est           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5, 13.78.104.32 |
| OuJapon Est           | 104.214.148.156, 40.74.100.192, 40.74.97.10 |
| Centre de la Corée        | 52.231.32.42, 52.231.17.22, 52.231.17.23, 20.44.24.32, 20.194.64.33 |
| Corée du Sud          | 52.231.200.86, 52.231.151.96 |
| Centre-Nord des États-Unis     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 |
| Europe Nord         | 40.113.93.91, 52.138.224.1, 13.74.104.113 |
| Norvège Est          | 51.120.96.0, 51.120.96.33 |
| Norvège Ouest          | 51.120.216.0       |
| Afrique du Sud Nord   | 102.133.152.0, 102.133.120.2, 102.133.152.32 |
| Afrique du Sud Ouest    | 102.133.24.0       |
| États-Unis - partie centrale méridionale     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Asie Sud-Est      | 104.43.15.0, 40.78.232.3, 13.67.16.193 |
| Suisse Nord    | 51.107.56.0, 51.107.57.0 |
| Suisse Ouest     | 51.107.152.0, 51.107.153.0 |
| Émirats arabes unis Centre          | 20.37.72.64        |
| Émirats arabes unis Nord            | 65.52.248.0        |
| Sud du Royaume-Uni             | 51.140.184.11, 51.105.64.0, 51.140.144.36, 51.105.72.32 |
| Ouest du Royaume-Uni              | 51.141.8.11, 51.140.208.96, 51.140.208.97 |
| Centre-USA Ouest      | 13.78.145.25, 13.78.248.43, 13.71.193.32, 13.71.193.33 |
| Europe Ouest          | 40.68.37.158, 104.40.168.105, 52.236.184.163  |
| USA Ouest              | 104.42.238.205, 13.86.216.196   |
| USA Ouest 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
| USA Ouest 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |


## <a name="next-steps"></a>Étapes suivantes

- Pour des informations sur le changement de stratégie de connexion Azure SQL Database pour un serveur, consultez [conn-policy](/cli/azure/sql/server/conn-policy).
- Pour plus d’informations sur le comportement de connexion d’Azure SQL Database pour les clients qui utilisent ADO.NET version 4.5 ou ultérieure, consultez [Ports au-delà de 1433 pour ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Pour en savoir plus sur la vue d’ensemble du développement d’applications générales, consultez [Vue d’ensemble du développement de base de données SQL](develop-overview.md).
