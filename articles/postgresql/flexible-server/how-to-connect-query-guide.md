---
title: Connexion et interrogation – Serveur flexible PostgreSQL
description: Liens vers des guides de démarrage rapide montrant comment se connecter à un serveur flexible Azure Database pour PostgreSQL et exécuter des requêtes.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364525"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Vue d’ensemble de la connexion et des requêtes avec Azure Database pour PostgreSQL – Serveur flexible

Le document suivant comprend des liens vers des exemples montrant comment se connecter et exécuter des requêtes avec un serveur unique Azure Database pour PostgreSQL. Ce guide comprend également une extension et des recommandations TLS que vous pouvez utiliser pour vous connecter au serveur dans les langages pris en charge ci-dessous.

>[!IMPORTANT]
> Le serveur flexible Azure Database pour PostgreSQL est en **préversion**.

## <a name="quickstarts"></a>Démarrages rapides

| Démarrage rapide | Description |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|Vous pouvez utiliser pgadmin pour vous connecter au serveur et simplifier la création, la maintenance et l’utilisation des objets de base de données.|
|[psql dans Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|Cet article montre comment exécuter [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) dans [Azure Cloud Shell](../../cloud-shell/overview.md) pour vous connecter à votre serveur, puis exécuter des instructions afin d’interroger la base de données, la mettre à jour, y insérer des données et en supprimer. Vous pouvez exécuter **psql** si elle est installée dans votre environnement de développement|
|[Python](connect-python.md)|Ce guide de démarrage rapide explique comment utiliser Python pour se connecter à une base de données et utiliser des objets de base de données pour interroger des données. |
|[Django avec App Service](tutorial-django-app-service-postgres.md)|Ce tutoriel montre comment utiliser Ruby afin de créer un programme permettant de se connecter à une base de données et d’utiliser des objets de base de données pour interroger des données.|

## <a name="tls-considerations-for-database-connectivity"></a>Considérations relatives au protocole TLS pour la connectivité des bases de données

Le protocole TLS (Transport Layer Security) est utilisé par tous les pilotes fournis ou pris en charge par Microsoft pour la connexion à des bases de données dans Azure Database pour PostgreSQL. Aucune configuration spéciale n’est nécessaire, mais veillez à mettre en œuvre le protocole TLS 1.2 pour les serveurs récemment créés. Si vous utilisez les protocoles TLS 1.0 et 1.1, nous vous recommandons de mettre à jour ces versions sur vos serveurs. Voir [Comment configurer le protocole TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>Extensions PostgreSQL

PostgreSQL offre la possibilité d’étendre les fonctionnalités d’une base de données à l’aide des extensions. Les extensions regroupent plusieurs objets SQL associés au sein d’un package qui peut être chargé ou supprimé de votre base de données à l’aide d’une seule commande. Une fois chargées dans la base de données, les extensions fonctionnent comme des fonctionnalités intégrées.

- [Extensions Postgres 12](./concepts-extensions.md#postgres-12-extensions)
- [Extensions Postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [dblink et postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

Pour plus d’informations, consultez [Guide pratique pour utiliser des extensions PostgreSQL sur un serveur flexible](concepts-extensions.md).

## <a name="next-steps"></a>Étapes suivantes

- [Migrer des données en utilisant l’image mémoire et la restauration](../howto-migrate-using-dump-and-restore.md)
- [Migrer des données par importation et exportation](../howto-migrate-using-export-and-import.md)
