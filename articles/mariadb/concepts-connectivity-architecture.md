---
title: Architecture de connectivité dans Azure Database pour MariaDB
description: Décrit l’architecture de connectivité pour votre serveur Azure Database pour MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariaDB
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 126e7ce4d9784a2f6a59f39562dff9a0b9d60ea0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735390"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Architecture de connectivité dans Azure Database pour MariaDB
Cet article explique la base de données Azure pour l’architecture de connectivité de MariaDB, ainsi que la manière dont le trafic est dirigé vers votre serveur Azure Database pour MariaDB instance à partir de clients au sein et en dehors d’Azure.

## <a name="connectivity-architecture"></a>Architecture de connectivité

Connexion à votre base de données Azure pour MariaDB est établie via une passerelle qui est responsable de routage des connexions entrantes à l’emplacement physique de votre serveur dans nos clusters. Le diagramme suivant illustre le flux de trafic.

![Vue d’ensemble de l’architecture de connectivité](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Comme client de se connecter à la base de données, ils obtiennent une chaîne de connexion qui se connecte à la passerelle. Cette passerelle a une adresse IP publique qui écoute le port 3306. À l’intérieur du cluster de base de données, le trafic est transféré à la base de données Azure approprié pour MariaDB. Par conséquent, pour vous connecter à votre serveur, comme à partir de réseaux d’entreprise, il est nécessaire ouvrir le pare-feu du côté client pour autoriser le trafic sortant être en mesure d’atteindre nos passerelles. Vous trouverez ci-dessous une liste complète des adresses IP utilisées par nos passerelles par région.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Base de données Azure pour les adresses IP de passerelle MariaDB

Le tableau suivant répertorie les adresses IP au principal et secondaire de la base de données Azure pour la passerelle MariaDB pour toutes les régions de données. L’adresse IP principale est l’adresse IP actuelle de la passerelle et la deuxième adresse IP est une adresse IP de basculement en cas de défaillance du principal. Comme mentionné, les clients doivent autoriser le trafic sortants vers les adresses IP. La deuxième adresse IP n’écoute pas sur tous les services jusqu'à ce qu’il est activé par Azure Database pour MariaDB accepter les connexions.

| **Nom de la région** | **Adresse IP principale** | **Adresse IP secondaire** |
|:----------------|:-------------|:------------------------|
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
| Sud du Royaume-Uni | 51.140.184.11 | |
| Ouest du Royaume-Uni | 51.141.8.11| |
| Europe Ouest | 191.237.232.75 | 40.68.37.158 |
| USA Ouest 1 | 23.99.34.75 | 104.42.238.205 |
| USA Ouest 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *USA Est 2* possède également l'adresse IP tertiaire `52.167.104.0`.

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des règles de pare-feu Azure Database for MariaDB à l’aide du portail Azure](./howto-manage-firewall-portal.md)
* [Créer et gérer la base de données Azure pour les règles de pare-feu MariaDB avec Azure CLI](./howto-manage-firewall-cli.md)
