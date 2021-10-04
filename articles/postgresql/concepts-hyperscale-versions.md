---
title: Versions prises en charge - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Versions PostgreSQL disponibles dans Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 8dd0929d56bac0b2f825d976ba07bd0956c0d063
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755197"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Versions de base de données prises en charge dans Azure Database pour PostgreSQL - Hyperscale (Citus)

## <a name="postgresql-versions"></a>Versions de PostgreSQL

La version de PostgreSQL s’exécutant dans un groupe de serveurs hyperscale (Citus) est personnalisable lors de la création. Hyperscale (Citus) prend actuellement en charge les versions principales suivantes :

### <a name="postgresql-version-13"></a>PostgreSQL version 13

La version mineure actuelle est 13.4. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/13/release-13-4.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

### <a name="postgresql-version-12"></a>PostgreSQL version 12

La version mineure actuelle est 12.8. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/12/release-12-8.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

### <a name="postgresql-version-11"></a>PostgreSQL Version 11

La version mineure actuelle est 11.13. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/11/release-11-13.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL version 10 et antérieures

Nous ne prenons pas en charge PostgreSQL version 10 (et versions antérieures) pour Azure Database pour PostgreSQL - Hyperscale (Citus).

## <a name="citus-and-other-extension-versions"></a>Citus et autres versions d’extension

Selon la version de PostgreSQL qui s’exécute dans un groupe de serveurs, différentes [versions des extensions postgres](concepts-hyperscale-extensions.md) sont également installées.  En particulier, Postgres 13 est fourni avec Citus 10, et les versions antérieures de Postgres sont fournies avec Citus 9.5.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez quelles [extensions](concepts-hyperscale-extensions.md) sont installées dans quelles versions.
* Apprenez à [créer un groupe de serveurs Hyperscale (Citus)](quickstart-create-hyperscale-portal.md).
