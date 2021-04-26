---
title: Magasin des requêtes - Azure Database pour PostgreSQL - Serveur flexible
description: Cet article décrit la fonctionnalité Magasin des requêtes dans Azure Database pour PostgreSQL - Serveur flexible.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558591"
---
# <a name="monitor-performance-with-query-store"></a>Superviser les performances avec le Magasin des requêtes
**S’applique à :** Azure Database pour PostgreSQL - Serveur flexible versions 11 et ultérieures

La fonctionnalité Magasin des requêtes dans Azure Database pour PostgreSQL fournit un moyen de suivre les performances des requêtes dans le temps. Le Magasin des requêtes simplifie la résolution des problèmes de performances en vous aidant à identifier rapidement les requêtes dont l’exécution est la plus longue et qui consomment le plus de ressources. Le Magasin des requêtes capture automatiquement un historique des requêtes et des statistiques d’exécution, et les conserve pour que vous les passiez en revue. Il découpe les données par heure de façon à ce que vous puissiez voir des modèles d’utilisation temporelle. Les données de tous les utilisateurs, des bases de données et des requêtes sont stockées dans une base de données nommée **azure_sys** dans l’instance Azure Database pour PostgreSQL.

> [!IMPORTANT]
> Ne modifiez pas la base de données **azure_sys** ou ses schémas. Si vous le faites, le Magasin des requêtes et les fonctionnalités de performances associées ne fonctionneront pas correctement.
## <a name="enabling-query-store"></a>Activation du Magasin des requêtes
Le Magasin des requêtes étant une fonctionnalité avec option d’adhésion, elle n’est pas activée par défaut sur un serveur. Le Magasin des requêtes est activé ou désactivé de façon globale pour toutes les bases de données se trouvant sur un serveur donné, et ne peut pas être activé ou désactivé par base de données.
### <a name="enable-query-store-using-the-azure-portal"></a>Activer le Magasin des requêtes à l’aide du portail Azure
1. Connectez-vous au portail Azure, puis sélectionnez votre serveur Azure Database pour PostgreSQL.
2. Sélectionnez **Paramètres du serveur** dans la section **Paramètres** du menu.
3. Recherchez le paramètre `pg_qs.query_capture_mode`.
4. Définissez la valeur sur `TOP` ou `ALL` et cliquez sur **Enregistrer**.
Autoriser jusqu’à 20 minutes de conservation pour le premier lot de données dans la base de données azure_sys.
## <a name="information-in-query-store"></a>Informations dans le Magasin des requêtes
Le Magasin des requêtes contient un magasin :
- Un magasin des statistiques d’exécution pour conserver les informations sur les statistiques d’exécution des requêtes.

Les scénarios courants pour l’utilisation du Magasin des requêtes sont notamment les suivants :
- Détermination du nombre de fois où une requête a été exécutée dans une fenêtre de temps donnée
- Comparaison de la durée d’exécution moyenne d’une requête sur des fenêtres de temps pour voir les deltas importants
- Identification des requêtes dont l’exécution est la plus longue au cours des dernières heures Pour réduire l’utilisation de l’espace, les statistiques d’exécution du runtime dans le magasin des statistiques d’exécution sont agrégées pendant une fenêtre de temps configurable fixe. Les informations contenues dans ces magasins peuvent être interrogées à l’aide de vues.
## <a name="access-query-store-information"></a>Accéder aux informations du Magasin des requêtes
Les données du Magasin des requêtes sont stockées dans la base de données azure_sys sur votre serveur Postgres. La requête suivante retourne des informations sur les requêtes du Magasin des requêtes :
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Options de configuration
Quand le Magasin des requêtes est activé, il enregistre les données dans des fenêtres d’agrégation de 15 minutes, avec jusqu’à 500 requêtes distinctes par fenêtre. Les options suivantes sont disponibles pour la configuration des paramètres du Magasin des requêtes.

| **Paramètre** | **Description** | **Par défaut** | **Plage**|
|---|---|---|---|
| pg_qs.query_capture_mode | Définit les instructions qui sont suivies. | Aucun | none, top, all |
| pg_qs.max_query_text_length | Définit la longueur maximale de requête qui peut être enregistrée. Les requêtes les plus longues sont tronquées. | 6000 | 100 - 10 000 |
| pg_qs.retention_period_in_days | Définit la période de conservation. | 7 | 1 - 30 |
| pg_qs.track_utility | Définit si les commandes d’utilitaire sont suivies | sur | on, off |

Utilisez le [portail Azure](howto-configure-server-parameters-using-portal.md) pour obtenir ou définir une valeur différente pour un paramètre.

## <a name="views-and-functions"></a>Vues et fonctions
Affichez et gérez le Magasin des requêtes à l’aide des fonctions et vues suivantes. Quiconque dans le rôle public PostgreSQL peut utiliser ces vues pour afficher les données du Magasin des requêtes. Ces vues sont disponibles uniquement dans la base de données **azure_sys**.
Les requêtes sont normalisées en examinant leur structure après la suppression des littéraux et des constantes. Si deux requêtes sont identiques à l’exception des valeurs littérales, elle ont le même queryId.
### <a name="query_storeqs_view"></a>query_store.qs_view
Cette vue retourne toutes les données du Magasin des requêtes. Il existe une ligne pour chaque ID de base de données distinct, ID d’utilisateur et ID de requête. 

|**Nom**   |**Type** | **Informations de référence**  | **Description**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID de la table runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |OID de l’utilisateur qui a exécuté l’instruction|
|db_id  |oid    |pg_database.oid    |OID de la base de données dans laquelle l’instruction a été exécutée|
|query_id   |bigint  || Code de hachage interne, calculé à partir de l’arborescence d’analyse de l’instruction|
|query_sql_text |Varchar(10000)  || Texte d’une instruction représentative. Différentes requêtes ayant la même structure sont regroupées en clusters ; ce texte est le texte de la première des requêtes du cluster.|
|plan_id    |bigint |   |ID du plan correspondant à cette requête, pas encore disponible|
|start_time |timestamp  ||  Les requêtes sont agrégées par intervalle de planification : la durée d’un compartiment est de 15 minutes par défaut. Il s’agit de l’heure de début correspondant à l’intervalle de planification pour cette entrée.|
|end_time   |timestamp  ||  Heure de fin correspondant à l’intervalle de planification pour cette entrée.|
|calls  |bigint  || Nombre de fois où la requête s’est exécutée|
|total_time |double précision   ||  Durée totale d’exécution de la requête, en millisecondes|
|min_time   |double précision   ||  Durée minimale d’exécution de la requête, en millisecondes|
|max_time   |double précision   ||  Durée maximale d’exécution de la requête, en millisecondes|
|mean_time  |double précision   ||  Durée moyenne d’exécution de la requête, en millisecondes|
|stddev_time|   double précision    ||  Écart type de la durée d’exécution de la requête, en millisecondes |
|rows   |bigint ||  Nombre total de lignes récupérées ou affectées par l’instruction|
|shared_blks_hit|   bigint  ||  Nombre total d’accès au cache de blocs partagés par l’instruction|
|shared_blks_read|  bigint  ||  Nombre total de blocs partagés lus par l’instruction|
|shared_blks_dirtied|   bigint   || Nombre total de blocs partagés modifiés par l’instruction |
|shared_blks_written|   bigint  ||  Nombre total de blocs partagés écrits par l’instruction|
|local_blks_hit|    bigint ||   Nombre total d’accès au cache de blocs locaux par l’instruction|
|local_blks_read|   bigint   || Nombre total de blocs locaux lus par l’instruction|
|local_blks_dirtied|    bigint  ||  Nombre total de blocs locaux modifiés par l’instruction|
|local_blks_written|    bigint  ||  Nombre total de blocs locaux écrits par l’instruction|
|temp_blks_read |bigint  || Nombre total de blocs temporaires lus par l’instruction|
|temp_blks_written| bigint   || Nombre total de blocs temporaires écrits par l’instruction|
|blk_read_time  |double précision    || Durée totale passée par l’instruction à lire des blocs, en millisecondes (si track_io_timing est activé ; sinon, zéro)|
|blk_write_time |double précision    || Durée totale passée par l’instruction à écrire des blocs, en millisecondes (si track_io_timing est activé ; sinon, zéro)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Cette vue retourne les données du texte des requêtes du Magasin des requêtes. Il existe une ligne pour chaque valeur query_text distincte.

| **Nom** | **Type** | **Description** |
|--|--|--|
| query_text_id | bigint | ID de la table query_texts |
| query_sql_text | Varchar(10000) | Texte d’une instruction représentative. Différentes requêtes ayant la même structure sont regroupées en clusters ; ce texte est le texte de la première des requêtes du cluster. |

### <a name="functions"></a>Fonctions
`qs_reset` ignore toutes les statistiques collectées jusqu’à présent par le Magasin des requêtes. Cette fonction peut uniquement être exécutée par le rôle d’administrateur de serveur.

`staging_data_reset` ignore toutes les statistiques collectées en mémoire par le Magasin des requêtes (autrement dit, les données en mémoire qui n’ont pas encore été vidées dans la base de données). Cette fonction peut uniquement être exécutée par le rôle d’administrateur de serveur.

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus
- Si un serveur PostgreSQL a le paramètre default_transaction_read_only activé, le Magasin des requêtes ne capture aucune donnée.
## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus en détail les [scénarios où le Magasin des requêtes peut être particulièrement utile](concepts-query-store-scenarios.md).
- Découvrez plus en détail les [bonnes pratiques relatives à l’utilisation du Magasin des requêtes](concepts-query-store-best-practices.md).
