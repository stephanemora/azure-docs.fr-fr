---
title: 'Oracle vers Azure Database pour PostgreSQL : Guide de migration'
titleSuffix: Azure Database for PostgreSQL
description: Ce guide vous aide à migrer votre schéma Oracle vers Azure Database pour PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: c197f5200c3db60a63bfd8c8e4e01ab4881aa5b6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108756356"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrer Oracle vers Azure Database pour PostgreSQL

Ce guide vous aide à migrer votre schéma Oracle vers Azure Database pour PostgreSQL. 

Pour obtenir des conseils de migration détaillés et complets, consultez les [ressources du guide de migration](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Prérequis

Pour migrer votre schéma Oracle vers Azure Database pour PostgreSQL, vous devez : 

- Vérifiez que votre environnement source est pris en charge. 
- Téléchargez la dernière version d’[ora2pg](https://ora2pg.darold.net/). 
- Avoir la dernière version du [module DBD](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Vue d’ensemble

PostgreSQL est l’une des bases de données open source les plus évoluées au monde. Cet article explique comment utiliser l’outil gratuit ora2pg pour migrer une base de données Oracle vers PostgreSQL. Vous pouvez utiliser ora2pg pour migrer une base de données Oracle ou MySQL vers un schéma compatible PostgreSQL. 

L’outil ora2pg connecte votre base de données Oracle, l’analyse automatiquement et extrait sa structure ou ses données. Ensuite, ora2pg génère des scripts SQL que vous pouvez charger dans votre base de données PostgreSQL. Vous pouvez utiliser ora2pg pour des tâches telles que l’ingénierie à rebours d’une base de données Oracle, la migration d’une base de données d’entreprise volumineuse ou la simple réplication de données Oracle dans une base de données PostgreSQL. L’outil est facile à utiliser et ne nécessite aucune connaissance en bases de données Oracle, si ce n’est la capacité de fournir les paramètres nécessaires pour se connecter à la base de données Oracle.

> [!NOTE]
> Pour plus d’informations sur l’utilisation de la dernière version d’ora2pg, consultez la [documentation d’ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Architecture de migration ora2pg classique

![Capture d’écran de l’architecture de migration ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Après avoir approvisionné la machine virtuelle et Azure Database pour PostgreSQL, vous avez besoin de deux configurations pour activer la connectivité entre eux : **Autoriser l’accès aux services Azure** et **Appliquer la connexion SSL** : 

- Panneau **Sécurité de la connexion** > **Autoriser l’accès aux services Azure** > **ACTIVÉ**

- Panneau **Sécurité de la connexion** > **Paramètres SSL** > **Appliquer la connexion SSL** > **DÉSACTIVÉ**

### <a name="recommendations"></a>Recommandations

- Pour améliorer les performances des opérations d’évaluation ou d’exportation sur le serveur Oracle, collectez les statistiques :

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Exportez les données à l’aide de la commande `COPY` au lieu de `INSERT`.

- Évitez d’exporter des tables avec leurs clés étrangères, leurs contraintes et leurs index. Ces éléments ralentissent le processus d’importation des données dans PostgreSQL.

- Créez des vues matérialisées en utilisant la *clause no data*. Actualisez ensuite les vues ultérieurement.

- Si possible, utilisez des index uniques dans les vues matérialisées. Ces index peuvent accélérer l’actualisation lorsque vous utilisez la syntaxe `REFRESH MATERIALIZED VIEW CONCURRENTLY`.


## <a name="pre-migration"></a>Prémigration 

Après avoir vérifié que votre environnement source est pris en charge et que les conditions préalables sont satisfaites, vous êtes prêt à démarrer l’étape de prémigration. Pour commencer : 

1. **Découvrir** : Inventoriez les bases de données que vous devez migrer. 
2. **Évaluer** : Évaluez ces bases de données pour détecter les problèmes ou les obstacles potentiels à la migration.
3. **Convertir** : Résolvez tous les problèmes que vous avez découverts. 
 
Pour les migrations hétérogènes telles qu’Oracle vers Azure Database pour PostgreSQL, cette étape consiste également à rendre les schémas de la base de données source compatibles avec l’environnement cible.

### <a name="discover"></a>Découvrez

L’objectif de la phase de découverte est d’identifier les sources de données existantes, ainsi que divers détails concernant les fonctionnalités que votre entreprise utilise. Cette phase vous aide à mieux comprendre et à planifier la migration. Le processus implique une analyse du réseau pour identifier toutes les instances Oracle de votre organisation, ainsi que la version et les fonctionnalités utilisées.

Les scripts de pré-évaluation Microsoft pour Oracle s’exécutent sur la base de données Oracle. Les scripts de pré-évaluation interrogent les métadonnées Oracle. Les scripts fournissent :

- Un inventaire des bases de données, y compris le nombre d’objets par schéma, leur type et leur état.
- Une estimation approximative des données brutes dans chaque schéma, en fonction des statistiques.
- La taille des tables dans chaque schéma.
- Le nombre de lignes de code par package, fonction, procédure, etc.

Téléchargez les scripts associés à partir de [GitHub](https://github.com/microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts).

### <a name="assess"></a>Évaluer

Après avoir inventorié les bases de données Oracle, vous aurez une idée de la taille de la base de données et des possibles difficultés. L’étape suivante consiste à exécuter l’évaluation.

Il n’est pas facile d’estimer le coût d’une migration d’Oracle vers PostgreSQL. Pour évaluer le coût de la migration, ora2pg vérifie tous les objets de base de données, les fonctions et les procédures stockées pour détecter les objets et le code PL/SQL qu’il ne peut pas convertir automatiquement.

L’outil ora2pg dispose d’un mode d’analyse du contenu qui inspecte la base de données Oracle pour générer un rapport textuel. Le rapport décrit ce que contient la base de données Oracle et ce qui ne peut pas être exporté.

Pour activer le mode *analyse et rapport*, utilisez le type exporté `SHOW_REPORT` comme indiqué dans la commande suivante :

```
ora2pg -t SHOW_REPORT
```

L’outil ora2pg peut convertir le code SQL et PL/SQL de la syntaxe Oracle à PostgreSQL. Ainsi, une fois la base de données analysée, ora2pg peut estimer les difficultés liées au code et le temps nécessaire pour migrer une base de données complète.

Pour estimer le coût de la migration en jours-hommes, ora2pg vous permet d’utiliser une directive de configuration appelée `ESTIMATE_COST`. Vous pouvez également activer cette directive à partir d’une invite de commandes :

```
ora2pg -t SHOW_REPORT --estimate_cost
```

L’unité de migration par défaut représente environ cinq minutes pour un expert PostgreSQL. S’il s’agit de votre première migration, vous pouvez augmenter l’unité de migration par défaut à l’aide de la directive de configuration `COST_UNIT_VALUE` ou de l’option de ligne de commande `--cost_unit_value`.

La dernière ligne du rapport indique le code de migration total estimé en jours-hommes. L’estimation suit le nombre d’unités de migration estimé pour chaque objet.

Dans l’exemple de code suivant, vous voyez quelques variations d’évaluation : 
* Évaluation des tables
* Évaluation des colonnes
* Évaluation de schéma qui utilise une unité de coût par défaut de cinq minutes
* Évaluation de schéma qui utilise une unité de coût de dix minutes

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt 
ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Voici la sortie du niveau de migration B-5 de l’évaluation du schéma :

* Niveaux de migration :

  * A - Une migration qui peut être exécutée automatiquement
    
  * B - Une migration avec réécriture du code et un coût inférieur ou égal à 5 jours-hommes
    
  * C - Une migration avec réécriture du code et un coût supérieur à 5 jours-hommes
    
* Niveaux techniques :

   * 1 = Trivial : Aucune fonction stockée et aucun déclencheur

   * 2 = Facile : Aucune fonction stockée, mais des déclencheurs ; aucune réécriture manuelle

   * 3 = Simple : Fonctions stockées et/ou déclencheurs ; aucune réécriture manuelle

   * 4 = Manuel : Aucune fonction stockée, mais des déclencheurs ou des vues avec réécriture du code

   * 5 = Difficile : Fonctions stockées et/ou déclencheurs avec réécriture du code

L’évaluation se compose des éléments suivants : 
* Une lettre (A ou B) pour spécifier si la migration nécessite une réécriture manuelle.

* Un nombre compris entre 1 et 5 pour indiquer la difficulté technique. 

Une autre option, `-human_days_limit`, spécifie la limite de jours-hommes. Ici, définissez le niveau de migration sur C pour indiquer que la migration nécessite une grande quantité de travail, une gestion complète du projet et une assistance à la migration. La valeur par défaut est 10 jours-hommes. Vous pouvez utiliser la directive de configuration `HUMAN_DAYS_LIMIT` pour modifier cette valeur par défaut de façon permanente.

Cette évaluation de schéma a été développée pour aider les utilisateurs à choisir la base de données à migrer en premier et les équipes à mobiliser.

### <a name="convert"></a>Convertir

Dans les migrations avec un temps d’arrêt minimal, votre source de migration change. Elle s’éloigne de la cible en matière de données et de schéma après la migration ponctuelle. Lors de la phase de *synchronisation des données*, assurez-vous que toutes les modifications apportées à la source sont capturées et appliquées à la cible quasiment en temps réel. Après avoir vérifié que toutes les modifications ont été appliquées à la cible, vous pouvez *effectuer le basculement* de l’environnement source vers l’environnement cible.

Au cours de cette étape de la migration, le code Oracle et les scripts DDL sont convertis ou traduits en PostgreSQL. L’outil ora2pg exporte automatiquement les objets Oracle dans un format PostgreSQL. Certains objets générés ne peuvent pas être compilés dans la base de données PostgreSQL sans modification manuelle.  

Pour comprendre quels éléments nécessitent une intervention manuelle, commencez par compiler les fichiers générés par ora2pg sur la base de données PostgreSQL. Vérifiez le journal, puis apportez les modifications nécessaires jusqu’à ce que la structure du schéma soit compatible avec la syntaxe PostgreSQL.


#### <a name="create-a-migration-template"></a>Créer un modèle de migration 

Nous vous recommandons d’utiliser le modèle de migration fourni par ora2pg. Lorsque vous utilisez les options `--project_base` et `--init_project`, ora2pg crée un modèle de projet avec une arborescence de travail, un fichier config et un script pour exporter tous les objets de la base de données Oracle. Pour plus d’informations, consultez la [documentation de d’ora2pg](https://ora2pg.darold.net/documentation.html).

Utilisez la commande suivante : 

```
ora2pg --project_base /app/migration/ --init_project test_project
```

Voici l’exemple de sortie : 
   
```
ora2pg --project_base /app/migration/ --init_project test_project
        Creating project test_project.
        /app/migration/test_project/
                schema/
                        dblinks/
                        directories/
                        functions/
                        grants/
                        mviews/
                        packages/
                        partitions/
                        procedures/
                        sequences/
                        synonyms/
                        tables/
                        tablespaces/
                        triggers/
                        types/
                        views/
                sources/
                        functions/
                        mviews/
                        packages/
                        partitions/
                        procedures/
                        triggers/
                        types/
                        views/
                data/
                config/
                reports/

        Generating generic configuration file
        Creating script export_schema.sh to automate all exports.
        Creating script import_all.sh to automate all imports.
```

Le répertoire `sources/` contient le code Oracle. Le répertoire `schema/` contient le code porté sur PostgreSQL. Et le répertoire `reports/` contient les rapports HTML et l’évaluation des coûts de migration.


Une fois la structure de projet créée, un fichier de configuration générique est créé. Définissez la connexion de base de données Oracle et les paramètres de configuration appropriés dans le fichier config. Pour plus d’informations sur le fichier config, consultez la [documentation d’ora2pg](https://ora2pg.darold.net/documentation.html).


#### <a name="export-oracle-objects"></a>Exporter des objets Oracle

Ensuite, exportez les objets Oracle en tant qu’objets PostgreSQL en exécutant le fichier *export_schema.sh*.

```
cd /app/migration/mig_project
./export_schema.sh
```

Exécutez manuellement la commande suivante.

```
SET namespace="/app/migration/mig_project"

ora2pg -p -t DBLINK -o dblink.sql -b %namespace%/schema/dblinks -c %namespace%/config/ora2pg.conf
ora2pg -p -t DIRECTORY -o directory.sql -b %namespace%/schema/directories -c %namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c %namespace%/config/ora2pg.conf 
ora2pg -p -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf 
ora2pg -p -t MVIEW -o mview.sql -b %namespace%/schema/mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql -b %namespace%/schema/packages -c %namespace%/config/ora2pg.conf
ora2pg -p -t PARTITION -o partitions.sql -b %namespace%/schema/partitions -c %namespace%/config/ora2pg.conf
ora2pg -p -t PROCEDURE -o procs.sql -b %namespace%/schema/procedures -c %namespace%/config/ora2pg.conf
ora2pg -p -t SEQUENCE -o sequences.sql -b %namespace%/schema/sequences -c %namespace%/config/ora2pg.conf
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c %namespace%/config/ora2pg.conf
ora2pg -p -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf 
ora2pg -p -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c %namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c %namespace%/config/ora2pg.conf 
ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf 
ora2pg -p -t VIEW -o views.sql -b %namespace%/schema/views -c %namespace%/config/ora2pg.conf
```

Utilisez la commande suivante pour extraire les données.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Compiler des fichiers

Enfin, compilez tous les fichiers sur le serveur Azure Database pour PostgreSQL. Vous pouvez choisir de charger les fichiers DDL générés manuellement ou utiliser le deuxième script *import_all.sh* pour importer ces fichiers de manière interactive.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l %namespace%\schema\tables\create_table.log
```

Voici la commande d’importation de données :

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

Pendant que les fichiers sont compilés, vérifiez les journaux et corrigez toute syntaxe qu’ora2pg n’a pas pu convertir de manière autonome.

Pour plus d’informations, consultez les [solutions de contournement pour la migration d’Oracle vers Azure Database pour PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf).

## <a name="migrate"></a>Migrate 

Une fois que vous disposez de la configuration requise et que vous avez terminé les étapes de prémigration, vous pouvez commencer la migration du schéma et des données.

### <a name="migrate-schema-and-data"></a>Migrer le schéma et les données

Une fois que vous avez effectué les corrections nécessaires, une build stable de la base de données est prête à être déployée. Exécutez les commandes d’importation `psql`, en pointant vers les fichiers qui contiennent le code modifié. Cette tâche compile les objets de base de données sur la base de données PostgreSQL et importe les données.

Au cours de cette étape, vous pouvez implémenter un niveau de parallélisme sur l’importation des données.

### <a name="sync-data-and-cut-over"></a>Synchroniser les données et effectuer le basculement

Dans les migrations en ligne (temps d’arrêt minimal), la source de migration continue de changer. Elle s’éloigne de la cible en matière de données et de schéma après la migration ponctuelle. 

Lors de la phase de *synchronisation des données*, assurez-vous que toutes les modifications apportées à la source sont capturées et appliquées à la cible quasiment en temps réel. Après avoir vérifié que toutes les modifications ont été appliquées, vous pouvez effectuer le basculement de l’environnement source vers l’environnement cible.

Pour effectuer une migration en ligne, contactez le support à l’adresse AskAzureDBforPostgreSQL@service.microsoft.com.

Dans une migration *différentielle/incrémentielle* qui utilise ora2pg, pour chaque table, utilisez une requête qui filtre (*coupe*) par date, heure ou un autre paramètre. Terminez ensuite la migration à l’aide d’une deuxième requête qui migre les données restantes.

Dans la table de données source, migrez tout d’abord toutes les données d’historique. Voici un exemple :

```
select * from table1 where filter_data < 01/01/2019
```

Vous pouvez interroger les modifications depuis la migration initiale en exécutant une commande comme celle-ci :

```
select * from table1 where filter_data >= 01/01/2019
```

Dans ce cas, nous vous recommandons d’améliorer la validation en vérifiant la parité des données des deux côtés, source et cible.

## <a name="post-migration"></a>Post-migration 

Après l’étape *Migration*, effectuez les tâches de post-migration pour vous assurer que tout fonctionne de la manière la plus fluide et efficace possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Cette configuration nécessite parfois des modifications des applications.

### <a name="test"></a>Test

Une fois les données migrées vers la cible, exécutez des tests sur les bases de données pour vérifier que les applications fonctionnent bien avec la cible. Assurez-vous que la source et la cible sont correctement migrées en exécutant les scripts de validation manuelle des données sur les bases de données Oracle (source) et PostgreSQL (cible).

Idéalement, si les bases de données source et cible ont un chemin d’accès réseau, ora2pg doit être utilisé pour la validation des données. Vous pouvez utiliser l’action `TEST` pour vous assurer que tous les objets de la base de données Oracle ont été créés dans PostgreSQL. 

Exécutez cette commande :

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimiser

La phase de post-migration est cruciale pour résoudre les problèmes d’exactitude des données et vérifier leur exhaustivité. Au cours de cette phase, vous allez également résoudre les problèmes de performances de la charge de travail.

## <a name="migration-assets"></a>Ressources de migration 

Pour plus d’informations sur ce scénario de migration, consultez les ressources suivantes. Elles prennent en charge l’engagement d’un projet de migration concret.

| Ressource | Description    |
| -------------- | ------------------ |
| [Livre de recettes sur la migration d’Oracle vers Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Ce document aide les architectes, les consultants, les administrateurs de base de données et les autres personnes concernées à migrer rapidement les charges de travail d’Oracle vers Azure Database pour PostgreSQL à l’aide d’ora2pg. |
| [Solutions de contournement pour la migration d’Oracle vers Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Ce document aide les architectes, les consultants, les administrateurs de base de données et les autres personnes concernées à résoudre ou à contourner rapidement les problèmes lors de la migration des charges de travail d’Oracle vers Azure Database pour PostgreSQL. |
| [Procédure d’installation d’ora2pg sur Windows ou Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Ce document fournit un guide d’installation rapide pour la migration du schéma et des données d’Oracle vers Azure Database pour PostgreSQL en utilisant ora2pg sous Windows ou Linux. Pour plus d’informations, consultez la [documentation de d’ora2pg](http://ora2pg.darold.net/documentation.html). |

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme de données Microsoft Azure.

## <a name="more-support"></a>Plus de support

Pour une aide à la migration dépassant le cadre de l’outil ora2pg, contactez [ @Ask Azure DB pour PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une matrice des services et des outils pour la migration de bases de données et de données et pour les tâches spécialisées, consultez [Services et outils disponibles pour la migration de données](../dms/dms-tools-matrix.md).

Documentation : 
- [Documentation relative à Azure Database pour PostgreSQL](./index.yml)
- [Documentation relative à ora2pg](https://ora2pg.darold.net/documentation.html)
- [Site web de PostgreSQL](https://www.postgresql.org/)
- [Prise en charge des transactions autonomes dans PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 