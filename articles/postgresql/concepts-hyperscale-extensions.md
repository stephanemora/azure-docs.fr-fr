---
title: Extensions PostgreSQL dans Azure Database pour PostgreSQL - Hyperscale (Citus) (version préliminaire)
description: Décrit la capacité à étendre les fonctionnalités d’une base de données à l’aide des extensions de la base de données Azure pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4022c95bfda8cbdaed75876793bfbba4254a5c54
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410248"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>Extensions PostgreSQL dans Azure Database pour PostgreSQL - Hyperscale (Citus) (version préliminaire)

PostgreSQL offre la possibilité d’étendre les fonctionnalités d’une base de données à l’aide des extensions. Les extensions permettent de regrouper plusieurs objets SQL liés dans un package unique ; elles peuvent être chargées ou supprimées de votre base de données d’une seule commande. Une fois chargées dans la base de données, les extensions peuvent fonctionner comme des fonctionnalités intégrées. Pour plus d’informations sur les extensions PostgreSQL, consultez la page  [Empaqueter des objets liés dans une extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Guide pratique pour utiliser les extensions PostgreSQL

Les extensions PostgreSQL doivent être installées dans votre base de données pour être utilisables. Pour installer une extension donnée, exécutez la commande  [CRÉER UNE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html)  dans l’outil psql pour charger les objets empaquetés dans votre base de données.

Azure Database pour PostgreSQL prend actuellement en charge un sous-ensemble d’extensions de clé, comme indiqué ici. Les extensions qui ne sont pas mentionnées ici ne sont pas prises en charge ; vous ne pouvez pas créer votre propre extension avec le service de base de données Azure pour PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensions prises en charge par la base de données Azure pour PostgreSQL

Les tables suivantes répertorient les extensions PostgreSQL standard actuellement prises en charge par la base de données Azure pour PostgreSQL. Ces informations sont également disponibles en exécutant `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensions de types de données

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fournit un type de chaîne de caractères avec respect de la casse. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fournit un type de données pour les cubes multidimensionnels. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fournit un type de données permettant de stocker des ensembles de paires clé/valeur. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fournit des types de données pour les standards internationaux de numérotation de produits. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Maintenance des objets volumineuse. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fournit un type de données pour les structures hiérarchiques de type arborescence. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Type de données pour représenter les segments de ligne ou des intervalles à virgule flottante. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Type pour les n premiers JSONB. |

### <a name="functions-extensions"></a>Extensions de fonctions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Fonctions pour les champs incrémentés automatiquement. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fournit un moyen de calculer les distances de grands cercles à la surface de la Terre. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fournit plusieurs fonctions permettant de déterminer les ressemblances et la distance entre des chaînes. |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Fonctions de suivi qui a modifié une table. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agrégateur d’entier et que l’énumérateur (obsolète). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fournit des fonctions et des opérateurs permettant de manipuler des tableaux d’entiers non Null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Fonctions pour le suivi de la dernière heure de modification. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fournit des fonctions de chiffrement. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gère les tables partitionnées par date ou par ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fournit des fonctions et des opérateurs permettant de déterminer la similarité entre des textes alphanumériques par rapprochement de trigrammes. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Fonctions pour l’implémentation de l’intégrité référentielle (obsolète). |
> | session\_analytique | Fonctions permettant d’interroger des tableaux de hstore. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fournit des fonctions qui manipulent des tables entières, y compris les tables croisées. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Déclenche des notifications de modification. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Fonctions pour l’implémentation de voyage. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Génère des identificateurs uniques universels (UUID). |

### <a name="full-text-search-extensions"></a>Extensions de recherche en texte intégral

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fournit un modèle de dictionnaire de recherche de texte pour les entiers. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modèle de type dictionnaire recherche de texte pour le traitement de l’étendue de synonyme. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Dictionnaire de recherche de texte qui supprime les accents (signes diacritiques) des lexèmes. |

### <a name="index-types-extensions"></a>Extensions de types d’index

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Méthode d’accès Bloom - fichier de signature de l’index de base. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fournit des exemples de classes d’opérateurs GIN qui implémentent des comportements de type arbre B pour certains types de données. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fournit des classes d’opérateurs d’index GiST qui implémentent l’arbre B. |

### <a name="language-extensions"></a>Extensions de langage

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Langage procédural chargeable PL/pgSQL. |

### <a name="hyperscale-extensions"></a>Extensions de très grande échelle

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus de base de données distribuée. |
> | partition\_rebalancer | Rééquilibrer en toute sécurité des données dans un groupe de serveurs en cas d’ajout de nœud ou la suppression. |

### <a name="miscellaneous-extensions"></a>Extensions diverses

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Fonctions d’administration pour PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Fonctions de vérification d’intégrité de la relation. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper de données externes pour l’accès de fichier plat. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecter le contenu des pages de base de données de bas niveau. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fournit un moyen d’examiner ce qui se passe dans le cache partagé des tampons en temps réel. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Planificateur de travaux pour PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examinez le mappage d’espace libre (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fournit un moyen de charger les données de relation dans le cache des tampons. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fournit un moyen de suivre les statistiques d’exécution de toutes les instructions SQL exécutées par un serveur. (Voir ci-dessous pour une remarque sur cette extension). |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Examinez le mappage de visibilité (VM) et les informations de visibilité de niveau de la page. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fournit un moyen d’afficher des informations de verrouillage au niveau des lignes. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fournit un moyen d’afficher les statistiques au niveau du tuple. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de données externes permettant d’accéder aux données stockées dans des serveurs externes PostgreSQL. (Voir ci-dessous pour une remarque sur cette extension).|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informations sur les certificats SSL. |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Méthode TABLESAMPLE qui accepte le nombre de lignes en tant que limite. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | Méthode TABLESAMPLE qui accepte la durée en millisecondes, en tant que limite. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Fournit un moyen de créer des index hypothétiques qui ne consomment pas de ressources processeur ou disque. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Module prenant en charge les connexions aux autres bases de données PostgreSQL à partir d’une session de base de données. (Voir ci-dessous pour une remarque sur cette extension). |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Interrogation de XPath et XSLT. |


### <a name="postgis-extensions"></a>Extensions PostGIS

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objets spatiaux et géographiques pour PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Utilisé pour analyser une adresse et la décomposer en éléments constitutifs, Utilisé pour prendre en charge l’étape de normalisation des adresses par géocodage. |
> | postgis\_sfcgal | Fonctions de PostGIS SFCGAL. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder et geocoder inverse. |
> | postgis\_topologie | Types de données spatiales PostGIS topologie et les fonctions. |


## <a name="pgstatstatements"></a>pg_stat_statements
L’extension [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) est préchargée sur chaque serveur Azure Database pour PostgreSQL afin de vous fournir un moyen de suivre les statistiques d’exécution des instructions SQL.
Le paramètre `pg_stat_statements.track`, qui contrôle quelles instructions sont comptées par l’extension, a la valeur par défaut `top`, ce qui signifie que toutes les instructions exécutées directement par les clients sont suivies. Les deux autres niveaux de suivi sont `none` et `all`. Ce paramètre peut être configuré comme paramètre de serveur via le [portail Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Un compromis existe entre les informations d’exécution des requêtes fournies par pg_stat_statements et l’impact sur les performances du serveur en raison de la journalisation de chaque instruction SQL. Si vous n’utilisez pas l’extension pg_stat_statements de façon active, nous vous recommandons de définir `pg_stat_statements.track` sur `none`. Notez que certains services de surveillance tiers peuvent s’appuyer sur pg_stat_statements pour fournir des insights sur les performances des requêtes : vérifiez donc si c’est ou non le cas pour vous.

## <a name="dblink-and-postgresfdw"></a>dblink et postgres_fdw
dblink et postgres_fdw vous permettent de vous connecter à partir d’un serveur PostgreSQL à un autre ou à une autre base de données dans le même serveur. Le serveur de réception doit autoriser les connexions à partir du serveur d’envoi via son pare-feu. Quand vous utilisez ces extensions pour établir une connexion entre des serveurs Azure Database pour PostgreSQL, vous pouvez effectuer cette opération en définissant « Autoriser l’accès aux services Azure » sur Activé. Vous devez faire de même si vous souhaitez utiliser les extensions pour établir la connexion vers le même serveur. Le paramètre « Autoriser l’accès aux services Azure » se trouve dans la page du portail Azure dédiée au serveur Postgres, sous Sécurité de la connexion. L’activation du paramètre « Autoriser l’accès aux services Azure » place toutes les adresses IP Azure sur liste verte.

Actuellement, les connexions sortantes à partir de la base de données Azure pour PostgreSQL ne sont pas pris en charge, à l’exception des connexions à une autre base de données Azure pour les serveurs PostgreSQL.
