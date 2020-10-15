---
title: Architecture de connectivité - Azure Database for MariaDB
description: Décrit l’architecture de connectivité pour votre serveur Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: c3f557c757a46252b9fa0416cc62a827b233f1b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88065350"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Architecture de connectivité dans Azure Database for MariaDB
Cet article présente l’architecture de connectivité Azure Database for MariaDB, ainsi que la façon dont le trafic est orienté vers votre instance Azure Database for MariaDB des clients dans Azure et en dehors.

## <a name="connectivity-architecture"></a>Architecture de connectivité

La connexion à votre instance Azure Database for MariaDB est établie via une passerelle qui est responsable du routage des connexions entrantes vers l’emplacement physique de votre serveur dans nos clusters. Le schéma suivant illustre le flux de trafic.

![Vue d’ensemble de l’architecture de connectivité](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Quand le client se connecte à la base de données, il obtient une chaîne de connexion qui se connecte à la passerelle. Cette passerelle a une adresse IP publique qui écoute le port 3306. À l’intérieur du cluster de bases de données, le trafic est transféré vers l’instance Azure Database for MariaDB appropriée. Par conséquent, pour vous connecter à votre serveur, notamment à partir de réseaux d’entreprise, il est nécessaire d’ouvrir le pare-feu côté client pour autoriser le trafic sortant à atteindre nos passerelles. Vous trouverez ci-dessous une liste complète des adresses IP utilisées par nos passerelles en fonction de la région.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Adresses IP de passerelle Azure Database for MariaDB

Le tableau suivant répertorie les adresses IP principales et secondaires de la passerelle Azure Database for MariaDB pour toutes les régions de données. L’adresse IP principale est l’adresse IP actuelle de la passerelle et l’adresse IP secondaire est une adresse IP de basculement en cas de défaillance de la première. Comme mentionné, les clients doivent autoriser le trafic sortant vers les deux adresses IP. La deuxième adresse IP n’écoute pas les services tant qu’elle n’est pas activée par Azure Database for MariaDB de manière à accepter les connexions.

| **Nom de la région** | **Adresses IP de passerelle** |
|:----------------|:-------------|
| Centre de l’Australie| 20.36.105.0     |
| Australie Centre 2     | 20.36.113.0   |
| Australie Est | 13.75.149.87, 40.79.161.1     |
| Sud-Est de l’Australie |191.239.192.109, 13.73.109.251   |
| Brésil Sud | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Centre du Canada |40.85.224.249  |
| Est du Canada | 40.86.226.166    |
| USA Centre | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| Chine orientale | 139.219.130.35    |
| Chine orientale 2 | 40.73.82.1  |
| Chine du Nord | 139.219.15.17    |
| Chine Nord 2 | 40.73.50.0     |
| Asie Est | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| USA Est | 40.121.158.30, 191.238.6.43, 40.71.8.203, 40.71.83.113   |
| USA Est 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| France Centre | 40.79.137.0, 40.79.129.1  |
| France Sud | 40.79.177.0     |
| Centre de l’Allemagne | 51.4.144.100     |
| Nord-Est de l’Allemagne | 51.5.144.179  |
| Inde Centre | 104.211.96.159     |
| Sud de l’Inde | 104.211.224.146  |
| Inde Ouest | 104.211.160.80    |
| Japon Est | 13.78.61.196, 191.237.240.43  |
| OuJapon Est | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Centre de la Corée | 52.231.32.42   |
| Corée du Sud | 52.231.200.86    |
| Centre-Nord des États-Unis | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europe Nord | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| Afrique du Sud Nord  | 102.133.152.0    |
| Afrique du Sud Ouest | 102.133.24.0   |
| États-Unis - partie centrale méridionale |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Asie Sud-Est | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Émirats arabes unis Centre | 20.37.72.64  |
| Émirats arabes unis Nord | 65.52.248.0    |
| Sud du Royaume-Uni | 51.140.184.11   |
| Ouest du Royaume-Uni | 51.141.8.11  |
| Centre-USA Ouest | 13.78.145.25     |
| Europe Ouest | 40.68.37.158, 191.237.232.75, 13.69.105.208, 104.40.169.187  |
| USA Ouest | 104.42.238.205, 23.99.34.75, 13.86.216.212, 13.86.217.212 |
| USA Ouest 2 | 13.66.226.202  |
||||

## <a name="connection-redirection"></a>Redirection de connexion

Azure Database for MariaDB prend en charge une stratégie de connexion supplémentaire, la **redirection**, qui permet de réduire la latence réseau entre les applications clientes et les serveurs MariaDB. Avec cette fonctionnalité, une fois que la session TCP initiale est établie au serveur Azure Database for MariaDB, le serveur retourne l’adresse back-end du nœud qui héberge le serveur MariaDB au client. Par la suite, les paquets suivants sont directement acheminés vers le serveur, en ignorant la passerelle. Étant donné que les paquets vont directement au serveur, la latence et le débit améliorent les performances.

Cette fonctionnalité est prise en charge dans les serveurs Azure Database for MariaDB avec les versions de moteur 10.2 et 10.3.

La prise en charge de la redirection est disponible dans l’extension [PHP mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure), développée par Microsoft et est disponible sur [PECL](https://pecl.php.net/package/mysqlnd_azure). Pour plus d’informations sur l’utilisation de la redirection dans vos applications, consultez l’article [configuration de la redirection](./howto-redirection.md).

> [!IMPORTANT]
> La prise en charge de la redirection dans l’extension [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) PHP est actuellement disponible en préversion.

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des règles de pare-feu Azure Database for MariaDB à l’aide du portail Azure](./howto-manage-firewall-portal.md)
* [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-cli.md) (Créer et gérer des règles de pare-feu Azure Database for MariaDB à l’aide de l’interface de ligne de commande Azure)