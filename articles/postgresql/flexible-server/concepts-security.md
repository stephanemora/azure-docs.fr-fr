---
title: Sécurité dans Azure Database pour PostgreSQL - Serveur flexible
description: Découvrez la sécurité avec l’option de déploiement Serveur flexible dans Azure Database pour PostgreSQL.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 6fcd772fce8a3f5e869f12e9b20c1219adf8155b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866852"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Sécurité dans Azure Database pour PostgreSQL - Serveur flexible

Plusieurs couches de sécurité permettent de mieux protéger les données de votre serveur Azure Database pour PostgreSQL. Cet article décrit ces différentes options de sécurité.

## <a name="information-protection-and-encryption"></a>Protection et chiffrement des informations

Azure Database pour PostgreSQL chiffre les données de deux façons :

- **Données en transit** : Azure Database pour PostgreSQL chiffre les données en transit avec les protocoles SSL/TLS (Secure Sockets Layer/Transport Layer Security). Le chiffrement est appliqué par défaut.
- **Données au repos** : pour le chiffrement du stockage, Azure Database pour PostgreSQL utilise le module de chiffrement conforme à la norme FIPS 140-2. Les données sont chiffrées sur le disque, y compris les sauvegardes et les fichiers temporaires créés durant l’exécution des requêtes. 

  Le service utilise le chiffrement AES 256 bits inclus dans le chiffrement de stockage Azure, et les clés sont gérées par le système. Cela s’apparente à d’autres technologies de chiffrement au repos comme le chiffrement transparent des données (TDE) dans les bases de données SQL Server ou Oracle. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé.


## <a name="network-security"></a>Sécurité du réseau

Quand vous utilisez Azure Database pour PostgreSQL - Serveur flexible, vous avez deux options de réseau principales :

- **Accès privé** : vous pouvez déployer votre serveur dans un réseau virtuel Azure. Les réseaux virtuels Azure facilitent la mise en place de communications réseau privées et sécurisées. Les ressources incluses sur un réseau virtuel peuvent communiquer par le biais d’adresses IP privées. Pour plus d’informations, consultez la [vue d’ensemble de la mise en réseau pour Azure Database pour PostgreSQL - Serveur flexible](concepts-networking.md).

  Les règles de sécurité dans les groupes de sécurité réseau permettent de filtrer le type de trafic réseau qui peut circuler vers et depuis les interfaces réseau et les sous-réseaux de réseau virtuel. Pour plus d’informations, consultez la [vue d’ensemble des groupes de sécurité réseau](../../virtual-network/network-security-groups-overview.md).

- **Accès public** : le serveur est accessible par le biais d’un point de terminaison public. Le point de terminaison public est une adresse DNS résolvable publiquement. Son accès est sécurisé par un pare-feu qui bloque toutes les connexions par défaut. 

  Les règles de pare-feu IP octroient l’accès aux serveurs en fonction de l’adresse IP d’origine de chaque requête. Pour plus d’informations, consultez la [vue d’ensemble des règles de pare-feu](concepts-firewall-rules.md).

## <a name="access-management"></a>Gestion de l’accès

Quand vous créez un serveur Azure Database pour PostgreSQL, vous fournissez les informations d’identification d’un rôle Administrateur. Ce rôle Administrateur permet de créer des [rôles PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html) supplémentaires.

Vous pouvez aussi vous connecter au serveur en utilisant l’[authentification Azure Active Directory](../concepts-aad-authentication.md). La fonctionnalité d’[enregistrement d’audit](../concepts-audit.md) vous permet de suivre l’activité dans vos bases de données. 

> [!NOTE]
> Azure Database pour PostgreSQL - Serveur flexible ne prend pas en charge la [protection Azure Defender](../../security-center/azure-defender.md). 

## <a name="next-steps"></a>Étapes suivantes
- Activer les [règles de pare-feu pour les adresses IP](concepts-firewall-rules.md) dans un réseau d’accès public.
- Découvrir les [réseaux d’accès privé avec Azure Database pour PostgreSQL - Serveur flexible](concepts-networking.md).
- Découvrir l’[authentification Azure Active Directory](../concepts-aad-authentication.md) dans Azure Database pour PostgreSQL.