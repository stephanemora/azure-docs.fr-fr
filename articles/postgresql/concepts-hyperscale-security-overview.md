---
title: Présentation de la sécurité - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Protection des informations et sécurité réseau pour Azure Database pour PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: dd3d359143185eb4d33ddce6396b7c69288fe841
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084328"
---
# <a name="security-in-azure-database-for-postgresql--hyperscale-citus"></a>Sécurité dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Cette page décrit les différentes couches de sécurité disponibles pour protéger les données de votre groupe de serveurs Hyperscale (Citus). 

## <a name="information-protection-and-encryption"></a>Protection et chiffrement des informations

### <a name="in-transit"></a>En transit

Chaque fois que les données sont ingérées dans un nœud, Hyperscale (Citus) sécurise vos données en les chiffrant en transit avec Transport Layer Security 1.2. Le chiffrement (SSL/TLS) est toujours appliqué et ne peut pas être désactivé.

### <a name="at-rest"></a>Au repos

Le service Hyperscale (Citus) utilise le module de chiffrement conforme à la norme FIPS 140-2 pour chiffrer le stockage des données au repos. Toutes les données, notamment les sauvegardes, sont chiffrées sur le disque, y compris les fichiers temporaires créés durant l’exécution des requêtes.
Le service utilise le chiffrement AES 256 bits inclus dans le chiffrement de stockage Azure, et les clés sont gérées par le système. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé.

## <a name="network-security"></a>Sécurité du réseau

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

## <a name="limits-and-limitations"></a>Limites et limitations

Consultez la page sur [les limites et les limitations](concepts-hyperscale-limits.md) de Hyperscale (Citus).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [activer et gérer l’accès privé](howto-hyperscale-private-access.md) (préversion)
* En savoir plus sur les [points de terminaison privés](../private-link/private-endpoint-overview.md)
* En savoir plus sur les [réseaux virtuels](../virtual-network/concepts-and-best-practices.md)
* En savoir plus sur les [zones DNS privées](../dns/private-dns-overview.md)