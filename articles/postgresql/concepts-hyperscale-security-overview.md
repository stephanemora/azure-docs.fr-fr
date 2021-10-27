---
title: Présentation de la sécurité - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Protection des informations et sécurité réseau pour Azure Database pour PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: eedad96af5baa6fe588788247c4f8a3a44bee0a0
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075091"
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
* En savoir plus sur les [points de terminaison privés](/azure/private-link/private-endpoint-overview)
* En savoir plus sur les [réseaux virtuels](/azure/virtual-network/concepts-and-best-practices)
* En savoir plus sur les [zones DNS privées](/azure/dns/private-dns-overview)
