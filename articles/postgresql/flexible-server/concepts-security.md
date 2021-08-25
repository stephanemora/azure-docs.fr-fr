---
title: Sécurité dans Azure Database pour PostgreSQL - Serveur flexible
description: Découvrez la sécurité avec l’option de déploiement Serveur flexible dans Azure Database pour PostgreSQL - Serveur flexible
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 489ae3e9593ed5fa0865fb6f3fbe2eb617113d71
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181491"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Sécurité dans Azure Database pour PostgreSQL - Serveur flexible

Il existe plusieurs couches de sécurité qui permettent de protéger les données de votre serveur Azure Database pour PostgreSQL. Cet article décrit ces différentes options de sécurité.

## <a name="information-protection-and-encryption"></a>Protection et chiffrement des informations

### <a name="in-transit"></a>En transit
 Azure Database pour PostgreSQL sécurise vos données en chiffrant les données en transit à l’aide du protocole TLS (Transport Layer Security). Le chiffrement (SSL/TLS) est appliqué par défaut.

### <a name="at-rest"></a>Au repos
Le service Azure Database pour PostgreSQL utilise le module de chiffrement conforme à la norme FIPS 140-2 pour chiffrer le stockage des données au repos. Toutes les données, notamment les sauvegardes, sont chiffrées sur le disque, y compris les fichiers temporaires créés durant l’exécution des requêtes. Le service utilise le chiffrement AES 256 bits inclus dans le chiffrement de stockage Azure, et les clés sont gérées par le système. Ceci est similaire à d’autres technologies de chiffrement au repos comme Transparent Data Encryption (TDE) dans les bases de données SQL Server ou Oracle. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé.


## <a name="network-security"></a>Sécurité du réseau

Vous pouvez choisir parmi deux options réseau principales lorsque vous exécutez Azure Database pour PostgreSQL - Serveur flexible. Il s’agit de l’accès privé (intégration au réseau virtuel) et de l’accès public (adresses IP autorisées) . Si vous utilisez l’accès privé, votre serveur flexible sera déployé dans votre réseau virtuel Azure. Les réseaux virtuels Azure offrent des communications réseau privées et sécurisées. Les ressources incluses sur un réseau virtuel peuvent communiquer par le biais d’adresses IP privées.
Avec l’accès public, le serveur flexible est accessible par le biais d’un point de terminaison public. Le point de terminaison public est une adresse DNS pouvant être résolue publiquement, accessible via un pare-feu qui bloque par défaut toutes les connexions. 



### <a name="ip-firewall-rules"></a>Règles de pare-feu IP
Les règles de pare-feu IP octroient l’accès aux serveurs en fonction de l’adresse IP d’origine de chaque requête. Pour plus d’informations, consultez la [vue d’ensemble des règles de pare-feu](concepts-firewall-rules.md).


### <a name="private-vnet-access"></a>Accès au réseau virtuel privé
Vous pouvez déployer votre serveur flexible sur votre réseau virtuel Azure. Les réseaux virtuels Azure offrent des communications réseau privées et sécurisées. Pour plus d’informations, consultez [Serveur flexible](concepts-networking.md)

### <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Les règles de sécurité dans les groupes de sécurité réseau permettent de filtrer le type de trafic réseau qui peut circuler vers et depuis les interfaces réseau et les sous-réseaux de réseau virtuel.  Pour plus d’informations, consultez [Vue d’ensemble des groupes de sécurité réseau](../../virtual-network/network-security-groups-overview.md).

## <a name="access-management"></a>Gestion de l’accès

Quand vous créez un serveur Azure Database pour PostgreSQL, vous fournissez les informations d’identification d’un rôle Administrateur. Ce rôle Administrateur permet de créer d’autres [rôles PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html).

Vous pouvez aussi vous connecter au serveur en utilisant l’[authentification Azure Active Directory (AAD)](../concepts-aad-authentication.md).


### <a name="azure-defender-protection"></a>Protection Azure Defender

 Azure Database pour PostgreSQL - Serveur flexible ne prend pas en charge la [protection Azure Defender](../../security-center/azure-defender.md). 


La fonctionnalité d’[enregistrement d’audit](../concepts-audit.md) vous permet de suivre l’activité dans vos bases de données. 


## <a name="next-steps"></a>Étapes suivantes
  - Activer les règles de pare-feu pour les [adresses IP](concepts-firewall-rules.md) pour un réseau d’accès public
  - En savoir plus sur les [réseaux d’accès privé avec Azure Database pour PostgreSQL - Serveur flexible](concepts-networking.md)
  - Découvrir l’[authentification Azure Active Directory](../concepts-aad-authentication.md) dans Azure Database pour PostgreSQL