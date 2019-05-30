---
title: Versions prises en charge dans Azure Database pour PostgreSQL - serveur unique
description: Décrit les versions prises en charge dans Azure Database pour PostgreSQL - serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4dcbaf159fce6b5f5495a6a25a2a3420cad9e5e8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067257"
---
# <a name="supported-postgresql-database-versions"></a>Versions prises en charge de la base de données PostgreSQL
Microsoft a pour but de prendre en charge des versions n-2 du moteur PostgreSQL dans la base de données Azure pour PostgreSQL - serveur unique. Autrement dit, la version principale actuellement publiée (n) et les deux principales versions antérieures (-2).

La base de données Azure pour PostgreSQL prend actuellement en charge les versions suivantes :

## <a name="postgresql-version-107"></a>PostgreSQL Version 10.7
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-9612"></a>PostgreSQL Version 9.6.12
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-9516"></a>PostgreSQL Version 9.5.16
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
Azure Database pour PostgreSQL gère automatiquement les correctifs pour les versions mineures. Actuellement, la mise à niveau de version principale n’est pas prise en charge. Par exemple, la mise à niveau de PostgreSQL 9.5 à PostgreSQL 9.6 n’est pas prise en charge. Si vous souhaitez mettre à niveau vers la version principale suivante, créez une base de données de [sauvegarde et restaurez-la](./howto-migrate-using-dump-and-restore.md) vers un serveur créé avec la nouvelle version du moteur.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la prise en charge des différentes extensions PostgreSQL, consultez [Extensions PostgreSQL](concepts-extensions.md).
