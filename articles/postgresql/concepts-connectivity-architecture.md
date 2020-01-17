---
title: Architecture de connectivité - Azure Database pour PostgreSQL - Serveur unique
description: Décrit l’architecture de connectivité de votre serveur Azure Database pour PostgreSQL - Serveur unique
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 6ef7736a50783388b5ec0b32c698a0e747a7fd42
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458435"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Architecture de connectivité dans Azure Database pour PostgreSQL
Cet article présente l’architecture de connectivité d’Azure Database pour PostgreSQL, ainsi que la façon dont le trafic est redirigé vers votre instance de base de données Azure Database pour PostgreSQL à partir de clients au sein d’Azure et en dehors.

## <a name="connectivity-architecture"></a>Architecture de connectivité
La connexion à votre instance Azure Database pour PostgreSQL est établie via une passerelle qui est responsable du routage des connexions entrantes vers l’emplacement physique de votre serveur dans nos clusters. Le schéma suivant illustre le flux de trafic.

![Vue d’ensemble de l’architecture de connectivité](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Quand le client se connecte à la base de données, il obtient une chaîne de connexion qui se connecte à la passerelle. Cette passerelle a une adresse IP publique qui écoute le port 5432. À l’intérieur du cluster de bases de données, le trafic est transféré vers l’instance Azure Database pour PostgreSQL appropriée. Par conséquent, pour vous connecter à votre serveur, notamment à partir de réseaux d’entreprise, il est nécessaire d’ouvrir le pare-feu côté client pour autoriser le trafic sortant à atteindre nos passerelles. Vous trouverez ci-dessous une liste complète des adresses IP utilisées par nos passerelles en fonction de la région.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Adresses IP de passerelle Azure Database pour PostgreSQL
Le tableau suivant répertorie les adresses IP principales et secondaires de la passerelle Azure Database pour PostgreSQL pour toutes les régions de données. L’adresse IP principale est l’adresse IP actuelle de la passerelle et l’adresse IP secondaire est une adresse IP de basculement en cas de défaillance de la première. Comme mentionné, les clients doivent autoriser le trafic sortant vers les deux adresses IP. La deuxième adresse IP n’écoute pas les services tant qu’elle n’est pas activée par Azure Database pour PostgreSQL de manière à accepter les connexions.

| **Nom de la région** | **Adresses IP de passerelle** |
|:----------------|:-------------|
| Centre de l’Australie| 20.36.105.0     |
| Australie Centre 2     | 20.36.113.0   |
| Australie Est | 13.75.149.87, 40.79.161.1     |
| Sud-Est de l’Australie |191.239.192.109, 13.73.109.251   |
| Brésil Sud | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Centre du Canada |40.85.224.249  |
| Est du Canada | 40.86.226.166    |
| USA Centre | 23.99.160.139, 13.67.215.62   |
| Chine orientale | 139.219.130.35    |
| Chine orientale 2 | 40.73.82.1  |
| Chine du Nord | 139.219.15.17    |
| Chine Nord 2 | 40.73.50.0     |
| Asie Est | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| USA Est | 40.121.158.30, 191.238.6.43  |
| USA Est 2 |40.79.84.180, 191.239.224.107, 52.167.104.0     |
| France Centre | 40.79.137.0, 40.79.129.1  |
| Centre de l’Allemagne | 51.4.144.100     |
| Nord-Est de l’Allemagne | 51.5.144.179  |
| Inde Centre | 104.211.96.159     |
| Sud de l’Inde | 104.211.224.146  |
| Inde Ouest | 104.211.160.80    |
| Japon Est | 13.78.61.196, 191.237.240.43  |
| OuJapon Est | 104.214.148.156, 191.238.68.11    |
| Centre de la Corée | 52.231.32.42   |
| Corée du Sud | 52.231.200.86    |
| Centre-Nord des États-Unis | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europe Nord | 40.113.93.91, 191.235.193.75    |
| Afrique du Sud Nord  | 102.133.152.0    |
| Afrique du Sud Ouest | 102.133.24.0   |
| États-Unis - partie centrale méridionale |13.66.62.124, 23.98.162.75   |
| Asie Sud-Est | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Émirats arabes unis Centre | 20.37.72.64  |
| Émirats arabes unis Nord | 65.52.248.0    |
| Sud du Royaume-Uni | 51.140.184.11   |
| Ouest du Royaume-Uni | 51.141.8.11  |
| Centre-USA Ouest | 13.78.145.25     |
| Europe Ouest | 40.68.37.158, 191.237.232.75     |
| USA Ouest | 104.42.238.205, 23.99.34.75  |
| USA Ouest 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des règles de pare-feu de la base de données Azure pour PostgreSQL à l’aide du Portail Azure](./howto-manage-firewall-using-portal.md)
* [Créer et gérer des règles de pare-feu de la base de données Azure pour PostgreSQL à l’aide d’Azure CLI](./howto-manage-firewall-using-cli.md)
