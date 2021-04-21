---
title: Extensions - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Décrit la capacité à étendre les fonctionnalités d’une base de données à l’aide des extensions Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 221d8b1d9fdd40a71bcfdeed57c02451e44052f2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012760"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Extensions PostgreSQL dans Azure Database pour PostgreSQL - Hyperscale (Citus)

PostgreSQL offre la possibilité d’étendre les fonctionnalités d’une base de données à l’aide des extensions. Les extensions permettent de regrouper plusieurs objets SQL liés dans un package unique ; elles peuvent être chargées ou supprimées de votre base de données d’une seule commande. Une fois chargées dans la base de données, les extensions peuvent fonctionner comme des fonctionnalités intégrées. Pour plus d'informations sur les extensions PostgreSQL, consultez [Empaqueter des objets liés dans une extension](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Utiliser les extensions PostgreSQL

Les extensions PostgreSQL doivent être installées dans votre base de données pour être utilisables. Pour installer une extension donnée, exécutez la commande [CRÉER UNE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) dans l'outil psql afin de charger les objets empaquetés dans votre base de données.

Azure Database pour PostgreSQL - Hyperscale (Citus) prend actuellement en charge un sous-ensemble d’extensions de clé, comme indiqué ici. Les extensions autres que celles répertoriées ne sont pas prises en charge. Vous ne pouvez pas créer votre propre extension avec Azure Database pour PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensions prises en charge par la base de données Azure pour PostgreSQL

Les tables suivantes répertorient les extensions PostgreSQL standard actuellement prises en charge par la base de données Azure pour PostgreSQL. Ces informations sont également disponibles en exécutant `SELECT * FROM pg_available_extensions;`.

Les versions de chaque extension installée dans un groupe de serveurs diffèrent parfois selon la version de PostgreSQL (11, 12 ou 13). Les tables répertorient les versions d’extension par version de base de données.

### <a name="citus-extension"></a>Extension Citus

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Base de données distribuée Citus. | 9.5-1 | 9.5-1 | 10.0-2 |

### <a name="data-types-extensions"></a>Extensions de types de données

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Fournit un type de chaîne de caractères avec respect de la casse. | 1.5 | 1.6 | 1.6 |
> | [cube](https://www.postgresql.org/docs/current/static/cube.html) | Fournit un type de données pour les cubes multidimensionnels. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Fournit une structure de données HyperLogLog. | 2.14 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Fournit un type de données permettant de stocker des ensembles de paires clé-valeur. | 1.5 | 1.6 | 1.7 |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | Fournit des types de données pour les standards internationaux de numérotation de produits. | 1.2 | 1.2 | 1.2 |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Maintenance des objets volumineux (Large Object). | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Fournit un type de données pour les structures hiérarchiques de type arborescence. | 1.1 | 1,1 | 1,2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Type de données pour représenter des segments de ligne ou des intervalles à virgule flottante. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Type de données pour l’accumulation en ligne de statistiques basées sur le classement, telles que les quantiles et les moyennes tronquées. | 1.0 | 1.0 | 1.0 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Type pour les n premiers JSONB. | 2.2.2 | 2.3.1 | 2.3.1 |

### <a name="full-text-search-extensions"></a>Extensions de recherche en texte intégral

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Fournit un modèle de dictionnaire de recherche de texte pour les entiers. | 1.0 | 1.0 | 1.0 |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modèle de dictionnaire de recherche de texte pour le traitement de synonyme étendu. | 1.0 | 1.0 | 1.0 |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Dictionnaire de recherche de texte qui supprime les accents (signes diacritiques) des lexèmes. | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>Extensions de fonctions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Fonctions pour les champs incrémentés automatiquement. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Fournit un moyen de calculer les distances de grands cercles à la surface de la Terre. | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Fournit plusieurs fonctions permettant de déterminer les ressemblances et la distance entre des chaînes. | 1.1 | 1.1 | 1.1 |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Fonctions permettant de suivre qui a modifié une table. | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agrégateur et énumérateur d’entier (obsolète). | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Fournit des fonctions et des opérateurs permettant de manipuler des tableaux d’entiers non Null. | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Fonctions permettent de suivre la dernière heure de modification. | 1.0 | 1.0 | 1.0 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gère les tables partitionnées par date ou par ID. | 4,1 | 4.4.1 | 4.4.1 |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Fournit des fonctions et des opérateurs permettant de déterminer la similarité entre des textes alphanumériques par rapprochement de trigrammes. | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Fournit des fonctions de chiffrement. | 1.3 | 1.3 | 1.3 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Fonctions permettant d’implémenter l’intégrité référentielle (obsolète). | 1.0 | 1.0 | 1.0 |
> | session\_analytics | Fonctions permettant d’interroger des tables hstore. | | | |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Fournit des fonctions qui manipulent des tables entières, y compris les tables croisées. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Notifications de modification déclenchées. | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Fonctions permettant d’implémenter le voyage dans le temps. | 1.0 | | |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Génère des identificateurs uniques universels (UUID). | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>Extensions de types d’index

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Méthode d’accès Bloom, index basé sur un fichier de signature. | 1.0 | 1.0 | 1.0 |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Fournit des exemples de classes d’opérateurs GIN qui implémentent des comportements de type arbre B pour certains types de données. | 1.3 | 1.3 | 1.3 |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Fournit des classes d’opérateurs d’index GiST qui implémentent l’arbre B. | 1.5 | 1.5 | 1.5 |

### <a name="language-extensions"></a>Extensions de langage

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Langage procédural chargeable PL/pgSQL. | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>Extensions diverses

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Fonctions d’administration pour PostgreSQL. | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Fonctions permettant de vérifier l’intégrité de la relation. | 1,1 | 1,2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Module prenant en charge les connexions aux autres bases de données PostgreSQL à partir d’une session de base de données. Pour plus d'informations sur cette extension, consultez la section « dblink et postgres_fdw ». | 1.2 | 1.2 | 1.2 |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper de données externes pour l’accès de fichier plat. | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspectez le contenu de pages de base de données de bas niveau. | 1.7 | 1.7 | 1.8 |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Fournit un moyen d’examiner ce qui se passe dans le cache partagé des tampons en temps réel. | 1.3 | 1.3 | 1.3 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Planificateur de travaux pour PostgreSQL. | 1.1 | 1.3 | 1.3 |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examinez le mappage d’espace libre (FSM). | 1.2 | 1.2 | 1.2 |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Fournit un moyen de charger les données de relation dans le cache des tampons. | 1.2 | 1.2 | 1.2 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Fournit un moyen de suivre les statistiques d’exécution de toutes les instructions SQL exécutées par un serveur. Pour plus d’informations sur cette extension, consultez la section « pg_stat_statements ». | 1.6 | 1.7 | 1.8 |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Examinez le mappage de visibilité (machine virtuelle) et les informations de visibilité au niveau des pages. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Fournit un moyen d’afficher des informations de verrouillage au niveau des lignes. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Fournit un moyen d’afficher les statistiques au niveau du tuple. | 1.5 | 1.5 | 1.5 |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Wrapper de données externes permettant d’accéder aux données stockées dans des serveurs externes PostgreSQL. Pour plus d'informations sur cette extension, consultez la section « dblink et postgres_fdw ».| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informations sur les certificats TLS/SSL. | 1.2 | 1.2 | 1.2 |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Méthode TABLESAMPLE, qui accepte le nombre de lignes en tant que limite. | 1.0 | 1.0 | 1.0 |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | Méthode TABLESAMPLE, qui accepte le temps en millisecondes en tant que limite. | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Interrogation de XPath et XSLT. | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>Extensions PostGIS

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objets spatiaux et géographiques pour PostgreSQL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | address\_standardizer, address\_standardizer\_data\_us | Utilisé pour analyser une adresse et la décomposer en éléments constitutifs, Utilisé pour prendre en charge l’étape de normalisation des adresses par géocodage. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis\_sfcgal | Fonctions SFCGAL PostGIS. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder et geocoder inverse. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis\_topology | Types et fonctions spatiaux de topologie PostGIS. | 2.5.1 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
L’extension [pg\_stat\_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) est préchargée sur chaque serveur Azure Database pour PostgreSQL afin de vous fournir un moyen de suivre les statistiques d’exécution des instructions SQL.

Le paramètre `pg_stat_statements.track` contrôle les instructions comptées par l’extension. Sa valeur par défaut est `top`, ce qui signifie que toutes les instructions exécutées directement par les clients sont suivies. Les deux autres niveaux de suivi sont `none` et `all`. Ce paramètre peut être configuré comme paramètre de serveur via le [portail Azure](./howto-configure-server-parameters-using-portal.md) ou [Azure CLI](./howto-configure-server-parameters-using-cli.md).

Un compromis existe entre les informations d’exécution des requêtes fournies par pg_stat_statements et l'effet sur les performances du serveur en raison de la journalisation de chaque instruction SQL. Si vous n’utilisez pas l’extension pg_stat_statements de façon active, nous vous recommandons de définir `pg_stat_statements.track` sur `none`. Certains services de surveillance tiers peuvent s’appuyer sur pg_stat_statements pour fournir des insights sur les performances des requêtes et dès lors, vérifiez si cela vous concerne ou non.

## <a name="dblink-and-postgres_fdw"></a>dblink et postgres_fdw

Vous pouvez utiliser dblink et postgres\_fdw pour vous connecter à un serveur PostgreSQL à partir d’un autre serveur PostgreSQL, ou à une autre base de données du même serveur.  Le serveur de réception doit autoriser les connexions à partir du serveur d’envoi via son pare-feu.  Pour utiliser ces extensions pour la connexion entre des serveurs Azure Database pour PostgreSQL ou des groupes de serveurs Hyperscale (Citus), définissez **Autoriser les services et ressources Azure à accéder à ce groupe de serveurs (ou à ce serveur)** sur ACTIVÉ.  Vous devez également activer ce paramètre si vous souhaitez utiliser les extensions pour établir la connexion vers le même serveur.
Le paramètre **Autoriser les services et ressources Azure à accéder à ce groupe de serveurs** se trouve dans la page du portail Azure du groupe de serveurs Hyperscale (Citus) sous **Mise en réseau**.  Actuellement, les connexions sortantes d’un serveur unique Azure Database pour PostgreSQL et d’Hyperscale (Citus) ne sont pas prises en charge, à l’exception des connexions à d’autres serveurs Azure Database pour PostgreSQL et groupes de serveurs Hyperscale (Citus).
