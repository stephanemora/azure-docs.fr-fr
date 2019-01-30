---
title: Utiliser les extensions PostgreSQL dans Azure Database pour PostgreSQL
description: Décrit la capacité à étendre les fonctionnalités d’une base de données à l’aide des extensions de la base de données Azure pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/22/2019
ms.openlocfilehash: 6c6fec968efdd85eaf6249459f8e1a0384f2ea11
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462179"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Extensions PostgreSQL dans une base de données Azure pour PostgreSQL
PostgreSQL offre la possibilité d’étendre les fonctionnalités d’une base de données à l’aide des extensions. Les extensions permettent de regrouper plusieurs objets SQL liés dans un package unique ; elles peuvent être chargées ou supprimées de votre base de données d’une seule commande. Une fois chargées dans la base de données, les extensions peuvent fonctionner comme des fonctionnalités intégrées. Pour plus d’informations sur les extensions PostgreSQL, consultez la page  [Empaqueter des objets liés dans une extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Guide pratique pour utiliser les extensions PostgreSQL
Les extensions PostgreSQL doivent être installées dans votre base de données pour être utilisables. Pour installer une extension donnée, exécutez la commande  [CRÉER UNE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html)  dans l’outil psql pour charger les objets empaquetés dans votre base de données.

Azure Database pour PostgreSQL prend actuellement en charge un sous-ensemble d’extensions de clé, comme indiqué ici. Les extensions qui ne sont pas mentionnées ici ne sont pas prises en charge ; vous ne pouvez pas créer votre propre extension avec le service de base de données Azure pour PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensions prises en charge par la base de données Azure pour PostgreSQL
Les tables suivantes répertorient les extensions PostgreSQL standard actuellement prises en charge par la base de données Azure pour PostgreSQL. Ces informations sont également disponibles en exécutant `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensions de types de données

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Fournit un type de données pour les mots de passe chiffrés automatiquement. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fournit un type de chaîne de caractères avec respect de la casse. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fournit un type de données pour les cubes multidimensionnels. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fournit un type de données permettant de stocker des ensembles de paires clé/valeur. |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fournit des types de données pour les standards internationaux de numérotation de produits. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fournit un type de données pour les structures hiérarchiques de type arborescence. |

### <a name="functions-extensions"></a>Extensions de fonctions

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fournit un moyen de calculer les distances de grands cercles à la surface de la Terre. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fournit plusieurs fonctions permettant de déterminer les ressemblances et la distance entre des chaînes. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fournit des fonctions et des opérateurs permettant de manipuler des tableaux d’entiers non Null. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fournit des fonctions de chiffrement. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gère les tables partitionnées par date ou par ID. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fournit des fonctions et des opérateurs permettant de déterminer la similarité entre des textes alphanumériques par rapprochement de trigrammes. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fournit des fonctions qui manipulent des tables entières, y compris les tables croisées. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Génère des identificateurs uniques universels (UUID). |

### <a name="full-text-search-extensions"></a>Extensions de recherche en texte intégral

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fournit un modèle de dictionnaire de recherche de texte pour les entiers. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Dictionnaire de recherche de texte qui supprime les accents (signes diacritiques) des lexèmes. |

### <a name="index-types-extensions"></a>Extensions de types d’index

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fournit des exemples de classes d’opérateurs GIN qui implémentent des comportements de type arbre B pour certains types de données. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fournit des classes d’opérateurs d’index GiST qui implémentent l’arbre B. |

### <a name="language-extensions"></a>Extensions de langage

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Langage procédural chargeable PL/pgSQL. |
| [plv8](https://plv8.github.io/) | Une extension du langage Javascript pour PostgreSQL qui peut être utilisée pour des procédures stockées, des déclencheurs, etc. |

### <a name="miscellaneous-extensions"></a>Extensions diverses

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fournit un moyen d’examiner ce qui se passe dans le cache partagé des tampons en temps réel. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fournit un moyen de charger les données de relation dans le cache des tampons. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fournit un moyen de suivre les statistiques d’exécution de toutes les instructions SQL exécutées par un serveur. (Voir ci-dessous pour une remarque sur cette extension). |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fournit un moyen d’afficher des informations de verrouillage au niveau des lignes. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fournit un moyen d’afficher les statistiques au niveau du tuple. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de données externes permettant d’accéder aux données stockées dans des serveurs externes PostgreSQL. (Voir ci-dessous pour une remarque sur cette extension).|
| [hypopg](https://hypopg.readthedocs.io/en/latest/) | Fournit un moyen de créer des index hypothétiques qui ne consomment pas de ressources processeur ou disque. |
| [dblink](https://www.postgresql.org/docs/current/dblink.html) | Module prenant en charge les connexions aux autres bases de données PostgreSQL à partir d’une session de base de données. (Voir ci-dessous pour une remarque sur cette extension). |


### <a name="postgis-extensions"></a>Extensions PostGIS

> [!div class="mx-tableFixed"]
| **Extension** | **Description** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objets spatiaux et géographiques pour PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Utilisé pour analyser une adresse et la décomposer en éléments constitutifs, Utilisé pour prendre en charge l’étape de normalisation des adresses par géocodage. |
| [pgrouting](https://pgrouting.org/) | Étend la base de données géospatiale PostGIS / PostgreSQL pour offrir une fonctionnalité de routage géospatial. |


### <a name="using-pgstatstatements"></a>Utilisation de pg_stat_statements
L’extension [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) est préchargée sur chaque serveur Azure Database pour PostgreSQL afin de vous fournir un moyen de suivre les statistiques d’exécution des instructions SQL.
Le paramètre `pg_stat_statements.track`, qui contrôle quelles instructions sont comptées par l’extension, a la valeur par défaut `top`, ce qui signifie que toutes les instructions exécutées directement par les clients sont suivies. Les deux autres niveaux de suivi sont `none` et `all`. Ce paramètre peut être configuré comme paramètre de serveur via le [portail Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Un compromis existe entre les informations d’exécution des requêtes fournies par pg_stat_statements et l’impact sur les performances du serveur en raison de la journalisation de chaque instruction SQL. Si vous n’utilisez pas l’extension pg_stat_statements de façon active, nous vous recommandons de définir `pg_stat_statements.track` sur `none`. Notez que certains services de surveillance tiers peuvent s’appuyer sur pg_stat_statements pour fournir des insights sur les performances des requêtes : vérifiez donc si c’est ou non le cas pour vous.

### <a name="using-dblink-and-postgresfdw"></a>Utilisation de dblink et postgres_fdw
dblink et postgres_fdw vous permettent de vous connecter à partir d’un serveur PostgreSQL à un autre ou à une autre base de données dans le même serveur. Le serveur de réception doit autoriser les connexions à partir du serveur d’envoi via son pare-feu. Quand vous utilisez ces extensions pour établir une connexion entre des serveurs Azure Database pour PostgreSQL, vous pouvez effectuer cette opération en définissant « Autoriser l’accès aux services Azure » sur Activé. Vous devez faire de même si vous souhaitez utiliser les extensions pour établir la connexion vers le même serveur. Le paramètre « Autoriser l’accès aux services Azure » se trouve dans la page du portail Azure dédiée au serveur Postgres, sous Sécurité de la connexion. L’activation du paramètre « Autoriser l’accès aux services Azure » place toutes les adresses IP Azure sur liste verte.


## <a name="next-steps"></a>Étapes suivantes
Si vous ne voyez pas une extension que vous souhaitez utiliser, faites-le-nous savoir. Votez pour les demandes existantes ou envoyez de nouveaux commentaires et de nouvelles demandes dans notre [Forum de commentaires client](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
