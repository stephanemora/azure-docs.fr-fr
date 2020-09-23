---
title: Extensions - Azure Database pour PostgreSQL - Serveur flexible
description: Découvrez les extensions Postgres disponibles dans Azure Database pour PostgreSQL - Serveur flexible
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1126c218f8e80b7d89183746890a3fae1357d29d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930063"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Extensions PostgreSQL dans Azure Database pour PostgreSQL - Serveur flexible

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en préversion

PostgreSQL offre la possibilité d’étendre les fonctionnalités d’une base de données à l’aide des extensions. Les extensions regroupent plusieurs objets SQL associés au sein d’un package qui peut être chargé ou supprimé de votre base de données à l’aide d’une commande. Une fois chargées dans la base de données, les extensions fonctionnent comme des fonctionnalités intégrées.

## <a name="how-to-use-postgresql-extensions"></a>Guide pratique pour utiliser les extensions PostgreSQL
Les extensions PostgreSQL doivent être installées dans votre base de données pour être utilisables. Pour installer une extension en particulier, exécutez la commande [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html). Cette commande charge les objets empaquetés dans votre base de données.

Azure Database pour PostgreSQL prend en charge un sous-ensemble d’extensions clés, comme indiqué ci-dessous. Ces informations sont également disponibles en exécutant `SHOW azure.extensions;`. Les extensions non répertoriées dans ce document ne sont pas prises en charge sur Azure Database pour PostgreSQL - Serveur flexible. Vous ne pouvez pas créer ni charger votre propre extension dans Azure Database pour PostgreSQL.


## <a name="postgres-12-extensions"></a>Extensions Postgres 12

Les extensions suivantes sont disponibles dans les serveurs Azure Database pour PostgreSQL - Serveur flexible qui disposent de la version 12 de Postgres. 

> [!div class="mx-tableFixed"]
> | **Extension**| **Version d’extension** | **Description** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | Fonctions permettant de vérifier l’intégrité de la relation|
> |[bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | Méthode d’accès Bloom, index basé sur un fichier de signature|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | Prise en charge de l’indexation des types de données communs dans GIN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | Prise en charge de l’indexation des types de données communs dans GiST|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | Type de données des chaînes de caractères sans respect de la casse|
> |[cube](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | Type de données pour les cubes multidimensionnels|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | Se connecte à d’autres bases de données PostgreSQL à partir d’une base de données|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | Modèle de dictionnaire de recherche de texte pour les entiers|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | modèle de dictionnaire de recherche de texte pour le traitement de synonyme étendu|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | Calcule les distances orthodromiques à la surface de la Terre|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | Détermine les ressemblances et la distance entre les chaînes|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | Type de données permettant de stocker des paires clé/valeur|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | agrégateur et énumérateur d’entier. (Obsolète)|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | Fonctions, opérateurs et prise en charge d’index pour les tableaux d’entiers 1D|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | Types de données pour les standards internationaux de numérotation de produits|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | Type de données pour les structures hiérarchiques de type arborescence|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | inspectez le contenu de pages de base de données de bas niveau|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | Examine le cache des tampons partagé|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | examinez le mappage d’espace libre (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | Préchauffe les données de relation|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | Suit les statistiques d’exécution de toutes les instructions SQL exécutées|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | Mesure de la similarité du texte et recherche d’index sur la base de trigrammes|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | examinez le mappage de visibilité (machine virtuelle) et les informations de visibilité au niveau des pages|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | fournit des fonctionnalités d’audit|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | Fonctions de chiffrement|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | Affiche les informations de verrouillage au niveau des lignes|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Affiche les statistiques au niveau du tuple|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | Langage procédural PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | Géométrie, géographie et types et fonctions spatiaux raster PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | Wrapper de données externes pour les serveurs PostgreSQL distants|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | informations sur les certificats SSL|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le nombre de lignes en tant que limite|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le temps en millisecondes en tant que limite|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | Dictionnaire de recherche de texte qui supprime les accents|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | Génère des identificateurs uniques universels (UUID)|

## <a name="postgres-11-extensions"></a>Extensions Postgres 11

Les extensions suivantes sont disponibles dans les serveurs Azure Database pour PostgreSQL - Serveur flexible qui disposent de la version 11 de Postgres. 

> [!div class="mx-tableFixed"]
> | **Extension**| **Version d’extension** | **Description** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | Fonctions permettant de vérifier l’intégrité de la relation|
> |[bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | Méthode d’accès Bloom, index basé sur un fichier de signature|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Prise en charge de l’indexation des types de données communs dans GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Prise en charge de l’indexation des types de données communs dans GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | Type de données des chaînes de caractères sans respect de la casse|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | Type de données pour les cubes multidimensionnels|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | Se connecte à d’autres bases de données PostgreSQL à partir d’une base de données|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | Modèle de dictionnaire de recherche de texte pour les entiers|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | modèle de dictionnaire de recherche de texte pour le traitement de synonyme étendu|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | Calcule les distances orthodromiques à la surface de la Terre|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Détermine les ressemblances et la distance entre les chaînes|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | Type de données permettant de stocker des paires clé/valeur|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | agrégateur et énumérateur d’entier. (Obsolète)|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | Fonctions, opérateurs et prise en charge d’index pour les tableaux d’entiers 1D|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | Types de données pour les standards internationaux de numérotation de produits|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | Type de données pour les structures hiérarchiques de type arborescence|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | inspectez le contenu de pages de base de données de bas niveau|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Examine le cache des tampons partagé|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | examinez le mappage d’espace libre (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | Préchauffe les données de relation|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | Suit les statistiques d’exécution de toutes les instructions SQL exécutées|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | Mesure de la similarité du texte et recherche d’index sur la base de trigrammes|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | examinez le mappage de visibilité (machine virtuelle) et les informations de visibilité au niveau des pages|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fournit des fonctionnalités d’audit|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | Fonctions de chiffrement|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Affiche les informations de verrouillage au niveau des lignes|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Affiche les statistiques au niveau du tuple|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Langage procédural PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | Géométrie, géographie et types et fonctions spatiaux raster PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | Wrapper de données externes pour les serveurs PostgreSQL distants|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | informations sur les certificats SSL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | Fonctions qui manipulent des tables entières, y compris les tables d’analyse croisée|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le nombre de lignes en tant que limite|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  méthode TABLESAMPLE, qui accepte le temps en millisecondes en tant que limite|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | Dictionnaire de recherche de texte qui supprime les accents|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Génère des identificateurs uniques universels (UUID)|


## <a name="pg_prewarm"></a>pg_prewarm

L’extension pg_prewarm charge des données relationnelles dans le cache. Le préchauffage de vos caches signifie que vos requêtes ont de meilleurs temps de réponse lors de leur première exécution après un redémarrage. La fonctionnalité de préchauffage automatique n’est actuellement pas disponible dans Azure Database pour PostgreSQL - Serveur flexible.


## <a name="next-steps"></a>Étapes suivantes

Si vous ne voyez pas une extension que vous souhaitez utiliser, faites-le-nous savoir. Votez pour les demandes existantes ou créez de nouvelles demandes dans notre [Forum de commentaires](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).