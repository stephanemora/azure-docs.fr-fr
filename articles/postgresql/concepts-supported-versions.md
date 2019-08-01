---
title: Versions prises en charge dans Azure Database pour PostgreSQL - Serveur unique
description: Décrit les versions prises en charge dans Azure Database pour PostgreSQL - Serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551369"
---
# <a name="supported-postgresql-database-versions"></a>Versions prises en charge de la base de données PostgreSQL
Microsoft entend prendre en charge les versions n-2 du moteur PostgreSQL dans Azure Database pour PostgreSQL - Serveur unique. Autrement dit, la version principale actuellement publiée (n) et les deux principales versions antérieures (-2).

La base de données Azure pour PostgreSQL prend actuellement en charge les versions suivantes :

## <a name="postgresql-version-112"></a>PostgreSQL Version 11.2
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-107"></a>PostgreSQL Version 10.7
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-9612"></a>PostgreSQL Version 9.6.12
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-9516"></a>PostgreSQL Version 9.5.16
Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
Azure Database pour PostgreSQL gère automatiquement les correctifs pour les versions mineures. Actuellement, la mise à niveau de version principale n’est pas prise en charge. Par exemple, la mise à niveau de PostgreSQL 9.5 à PostgreSQL 9.6 n’est pas prise en charge. Si vous souhaitez mettre à niveau vers la version principale suivante, créez une base de données de [sauvegarde et restaurez-la](./howto-migrate-using-dump-and-restore.md) vers un serveur créé avec la nouvelle version du moteur.

> Notez qu’avant PostgreSQL version 10, la [stratégie de gestion de versions PostgreSQL](https://www.postgresql.org/support/versioning/) considérait une mise à niveau _principale_ comme une augmentation du premier _ou_ du deuxième nombre (par exemple, 9.5 à 9.6 était considéré comme une mise à niveau de version _principale_).
> À compter de la version 10, seule une modification du premier nombre est considérée comme une mise à niveau de version principale (par exemple, 10.0 à 10.1 est une mise à niveau de version _mineure_ et 10 à 11 est une mise à niveau de version _principale_).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la prise en charge des différentes extensions PostgreSQL, consultez [Extensions PostgreSQL](concepts-extensions.md).
