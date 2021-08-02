---
title: Guide de migration de MySQL local vers Azure Database pour MySQL – Plans de test
description: WWI a créé un plan de test qui comprenait un ensemble de tâches informatiques et commerciales. Pour que les migrations réussissent, tous les tests doivent être exécutés.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: ad8a0ef111368f81075acaf68fec9e653534f7e4
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082772"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-test-plans"></a>Guide de migration de MySQL local vers Azure Database pour MySQL – Plans de test

## <a name="prerequisites"></a>Prérequis

[Méthodes de migration](05-migration-methods.md)

## <a name="overview"></a>Vue d’ensemble

WWI a créé un plan de test qui comprenait un ensemble de tâches informatiques et commerciales. Pour que les migrations réussissent, tous les tests doivent être exécutés.

Tests :

  - Assurez-vous que la base de données migrée est cohérente (même nombre d’enregistrements et mêmes résultats de requêtes) avec les tables locales.

  - Assurez-vous que les performances sont acceptables (elles doivent correspondre aux mêmes performances que si elles étaient exécutées localement).

  - Assurez-vous que les performances des requêtes cibles répondent aux spécifications indiquées.

  - Assurez-vous que la connectivité entre le réseau local et le réseau Azure est acceptable.

  - Vérifiez que toutes les applications et tous les utilisateurs identifiés peuvent se connecter à l’instance de données migrée.

WWI a identifié un week-end et une fenêtre temporelle de migration qui a commencé à 22 h et s’est terminée à 2 h (heure du Pacifique). Si la migration ne s’est pas terminée avant l’objectif de 2 h (temps d’arrêt ciblé de 4 heures) avec tous les tests réussis, les procédures de restauration ont été démarrées. Des problèmes ont été documentés pour les futures tentatives de migration. Toutes les fenêtres de migration ont été avancées et replanifiées en fonction de délais commerciaux acceptables.

## <a name="sample-queries"></a>Exemples de requêtes

Une série de requêtes a été exécutée sur la source et la cible pour vérifier la réussite de la migration de la base de données. Les requêtes et scripts suivants permettent de déterminer si les actions de migration ont déplacé tous les objets de base de données requis de la source vers la cible.

### <a name="table-rows"></a>Lignes de table

Vous pouvez utiliser cette requête pour obtenir toutes les tables et une estimation du nombre de lignes :

```
SELECT SUM(TABLE_ROWS)
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '{SchemaName}';
```

> [!NOTE]
> La table `INFORMATION_SCHEMA` fournit un ensemble estimé de valeurs dans les tables. Pour obtenir une vue plus précise des métriques telles que la taille de la table, augmentez la taille de l’échantillon de pages à l’aide du paramètre de serveur `innodb_stats_transient_sample_pages`. L’augmentation de cette valeur de serveur force l’analyse de davantage de pages et donne des résultats plus précis.

Exécutez l’instruction SQL `count(*)` sur chaque table pour obtenir un compte précis des lignes. L’exécution de cette commande peut prendre beaucoup de temps sur les tables volumineuses. Le script suivant génère un ensemble d’instructions SQL qui peuvent être exécutées pour obtenir les comptes exacts :

```
SELECT CONCAT( 
    'SELECT "', 
    table_name, 
    '" AS table_name, COUNT(*) AS exact_row_count FROM `', 
    table_schema, 
    '`.`', 
    table_name, 
    '` UNION ' 
)  
FROM INFORMATION_SCHEMA.TABLES 
WHERE table_schema = '**my_schema**';
```

### <a name="table-fragmentation"></a>Fragmentation des tables

Les tables de données sont susceptibles de continuer à croître avec l’utilisation continue de l’application. Dans certains cas, les données peuvent ne pas croître, mais elles changent constamment par le biais de suppressions et de mises à jour. Si c’est le cas, il est possible qu’il y ait de nombreuses fragmentations dans les fichiers de la base de données. L’instruction MySQL OPTIMIZE TABLE peut réduire les besoins en espace de stockage physique et améliorer l’efficacité des E/S.

Vous pouvez [optimiser les tables MySQL](https://dev.mysql.com/doc/refman/8.0/en/optimize-table.html) en exécutant la commande suivante :

`optimize table TABLE_NAME`

### <a name="database-objects"></a>Objets de base de données

Utilisez la requête suivante pour vous assurer que tous les autres objets de base de données sont pris en compte. Bien que ces requêtes puissent calculer le nombre de lignes de table, elles peuvent ne pas tenir compte de la version de l’objet de base de données donné. Par exemple, même si une procédure stockée existe, elle peut avoir été modifiée entre le début et la fin de la migration. Il est recommandé de geler le développement des objets de base de données pendant la migration.

**Utilisateurs**

```sql
SELECT DISTINCT 
        USER 
FROM 
        mysql.user 
WHERE 
        user <> '' order by user
```

**Index**

```sql
SELECT DISTINCT 
        INDEX_NAME 
FROM 
        information_schema.STATISTICS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**Contraintes**

```sql
SELECT DISTINCT 
        CONSTRAINT_NAME 
FROM 
        information_schema.TABLE_CONSTRAINTS 
WHERE 
        CONSTRAINT_SCHEMA = '{SchemaName}'
```

**Views**

```sql
SELECT 
        TABLE_NAME 
FROM 
        information_schema.VIEWS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**Procédures stockées**

```sql
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'FUNCTION' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**Fonctions**

```sql
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'PROCEDURE' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**Événements**

```sql
SELECT 
        EVENT_NAME 
FROM 
        INFORMATION_SCHEMA.EVENTS 
WHERE 
        EVENT_SCHEMA = '{SchemaName}'
```

## <a name="rollback-strategies"></a>Stratégies de restauration

Les requêtes ci-dessus fournissent une liste de noms et de nombres d’objets utilisés dans une décision de restauration. Les utilisateurs de la migration peuvent effectuer la première étape de vérification des objets en vérifiant le nombre d’objets source et cible. Une différence dans le nombre d’objets ne signifie pas nécessairement qu’une restauration est nécessaire. Une évaluation approfondie peut montrer que la différence est légère et facilement récupérable. La migration manuelle de quelques objets en échec peut être la solution. Par exemple, si toutes les tables et lignes de données ont été migrées, mais que seules quelques fonctions ont été manquées, corrigez ces objets en échec et finalisez la migration. Si la base de données est relativement petite, il est possible d’effacer l’instance Azure Database pour MySQL et de recommencer la migration. Les bases de données volumineuses peuvent avoir besoin de plus de temps que celui disponible dans la fenêtre de migration pour déterminer les objets manquants. La migration doit être arrêtée et restaurée.

L’identification des objets de base de données manquants doit se faire rapidement pendant la fenêtre de migration. Chaque minute compte. Une option pourrait être d’exporter les noms des objets de l’environnement dans un fichier et d’utiliser un outil de comparaison de données pour identifier rapidement les objets manquants. Une autre solution consiste à exporter les noms des objets de base de données sources et à importer les données dans une table temporaire de l’environnement de base de données cible. Comparez les données à l’aide d’une instruction SQL **scriptée** et **testée**. La vitesse et la précision de la vérification des données sont essentielles au processus de migration. Ne vous fiez pas à la lecture et à la vérification manuelles d’une longue liste d’objets de base de données pendant une fenêtre de migration. Le risque d’erreur humaine est trop important. Il serait préférable de gérer par exception à l’aide d’outils.

## <a name="wwi-scenario"></a>Scénario WWI

Le DSI de WWI a reçu un rapport de confirmation indiquant que tous les objets de base de données ont été migrés de la base de données locale vers l’instance Azure Database pour MySQL. L’équipe chargée de la base de données a exécuté les requêtes ci-dessus sur la base de données avant le début de la migration et a enregistré tous les résultats dans une feuille de calcul.

Les informations sur le schéma de la base de données source ont été utilisées pour vérifier la fidélité des objets de migration cibles.

## <a name="test-plans-checklist"></a>Liste de vérification des plans de test

  - Ayez des requêtes de test scriptées, testées et prêtes à être exécutées.

  - Connaissez la durée d’exécution des requêtes de test et intégrez-la dans la chronologie de migration documentée.

  - Préparez une stratégie d’atténuation des risques et de restauration prête pour différents résultats potentiels.

  - Disposez d’une chronologie bien définie des événements pour la migration.  


> [!div class="nextstepaction"]
> [Lignes de base des performances](./07-performance-baselines.md)