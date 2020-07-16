---
title: Versions prises en charge - Azure Database pour PostgreSQL - Serveur unique
description: Décrit les versions principales et mineures de PostgreSQL prises en charge dans Azure Database pour PostgreSQL - Serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/17/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 61d9712026daab63a002793adf2e17b9db943f94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976895"
---
# <a name="supported-postgresql-major-versions"></a>Versions principales de PostgreSQL prises en charge
Microsoft entend prendre en charge les versions n-2 du moteur PostgreSQL dans Azure Database pour PostgreSQL - Serveur unique. Autrement dit, la version principale actuellement publiée (n) et les deux principales versions antérieures (-2).

Azure Database pour PostgreSQL prend actuellement en charge les versions principales suivantes :

## <a name="postgresql-version-11"></a>PostgreSQL Version 11
La version mineure actuelle est 11.6. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-10"></a>PostgreSQL Version 10
La version mineure actuelle est 10.11. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-96"></a>PostgreSQL version 9.6
La version mineure actuelle est 9.6.16. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="postgresql-version-95"></a>PostgreSQL Version 9.5
La version mineure actuelle est 9.5.20. Consultez la [documentation PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) pour en savoir plus sur les améliorations et les correctifs de cette version mineure.

## <a name="managing-upgrades"></a>Gestion des mises à niveau
Le projet PostgreSQL publie régulièrement des versions mineures pour corriger les bogues signalés. Azure Database pour PostgreSQL répare automatiquement les serveurs avec des versions mineures pendant les déploiements mensuels du service. 

La mise à niveau automatique des versions principales n’est pas prise en charge. Par exemple, il n’existe pas de mise à niveau automatique de PostgreSQL 9.5 vers PostgreSQL 9.6. Pour effectuer une mise à niveau vers la version principale suivante, créez une [base de données de sauvegarde et restaurez-la](./howto-migrate-using-dump-and-restore.md) sur un serveur qui a été créé avec la nouvelle version du moteur.

### <a name="version-syntax"></a>Syntaxe de version
Avant PostgreSQL version 10, la [stratégie de gestion de version PostgreSQL](https://www.postgresql.org/support/versioning/) considérait une _mise à niveau principale_ comme une augmentation du premier _ou_ du deuxième nombre. Par exemple, une mise à niveau de la version 9.5 vers la version 9.6 était considérée comme une mise à niveau _principale_. Depuis la version 10, seule une modification du premier numéro est considérée comme une mise à niveau principale. Par exemple, une mise à niveau de la version 10.0 vers la version 10.1 est une mise à niveau _mineure_. Une mise à niveau de la version 10 à 11 est une mise à niveau _principale_.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les extensions PostgrSQL prises en charge, consultez [le document Extensions](concepts-extensions.md).
