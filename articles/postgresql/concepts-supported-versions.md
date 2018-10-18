---
title: Versions prises en charge dans Azure Database pour PostgreSQL
description: Décrit les versions prises en charge des bases de données Azure pour PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630576"
---
# <a name="supported-postgresql-database-versions"></a>Versions prises en charge de la base de données PostgreSQL
Microsoft entend prendre en charge les versions n-2 du moteur PostgreSQL dans le service Azure Database pour PostgreSQL, autrement dit la version principale actuellement publiée (n) et les deux principales versions antérieures (-2).

La base de données Azure pour PostgreSQL prend actuellement en charge les versions suivantes :

## <a name="postgresql-version-104"></a>PostgreSQL version 10.4
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-4.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-969"></a>PostgreSQL version 9.6.9
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-9513"></a>PostgreSQL Version 9.5.13
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
La base de données Azure pour PostgreSQL gère automatiquement les correctifs pour les mises à jour de version mineures. Actuellement, la mise à niveau de version principale n’est pas prise en charge. Par exemple, la mise à niveau de PostgreSQL 9.5 à PostgreSQL 9.6 n’est pas prise en charge. Si vous souhaitez mettre à niveau vers la version principale suivante, effectuez une [sauvegarde et une restauration](./howto-migrate-using-dump-and-restore.md) vers un serveur créé avec la nouvelle version du moteur.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la prise en charge des différentes extensions PostgreSQL, consultez [Extensions PostgreSQL](concepts-extensions.md).
