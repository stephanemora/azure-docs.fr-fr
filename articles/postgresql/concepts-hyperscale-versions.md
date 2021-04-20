---
title: Versions prises en charge - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Versions PostgreSQL disponibles dans Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023811"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Versions de base de données prises en charge dans Azure Database pour PostgreSQL - Hyperscale (Citus)

## <a name="postgresql-versions"></a>Versions de PostgreSQL

> [!IMPORTANT]
> Les versions personnalisables de PostgreSQL dans Hyperscale (Citus) sont actuellement en préversion.  Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Vous pouvez consulter la liste complète des nouvelles fonctionnalités dans les [fonctionnalités d’évaluation pour Hyperscale (Citus)](hyperscale-preview-features.md).

La version de PostgreSQL s’exécutant dans un groupe de serveurs hyperscale (Citus) est personnalisable lors de la création. Le choix d’un élément autre que la version 11 est actuellement une fonctionnalité en préversion.

Hyperscale (Citus) prend actuellement en charge les versions principales suivantes :

### <a name="postgresql-version-13-preview"></a>PostgreSQL version 13 (préversion)

La version mineure actuelle est 13.2. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/13/static/release-13-2.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

### <a name="postgresql-version-12-preview"></a>PostgreSQL version 12 (préversion)

La version mineure actuelle est 12.6. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-6.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

### <a name="postgresql-version-11"></a>PostgreSQL Version 11

La version mineure actuelle est 11.11. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-11.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL version 10 et antérieures

Nous ne prenons pas en charge PostgreSQL version 10 et antérieures pour Azure Database pour PostgreSQL - Hyperscale (Citus).

## <a name="citus-and-other-extension-versions"></a>Citus et autres versions d’extension

Selon la version de PostgreSQL qui s’exécute dans un groupe de serveurs, différentes [versions des extensions postgres](concepts-hyperscale-extensions.md) sont également installées.  En particulier, Postgres 13 est fourni avec Citus 10, et les versions antérieures de Postgres sont fournies avec Citus 9.5.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez quelles [extensions](concepts-hyperscale-extensions.md) sont installées dans quelles versions.
* Apprenez à [créer un groupe de serveurs Hyperscale (Citus)](quickstart-create-hyperscale-portal.md).
