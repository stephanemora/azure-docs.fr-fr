---
title: Migrer à partir d’Oracle
titleSuffix: Azure Database for PostgreSQL
description: Ce guide vous apprend à migrer votre schéma Oracle vers Azure Database pour PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: ec6cf87b3fd326c905b4843dc30ae6ce15379305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608477"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrer Oracle vers Azure Database pour PostgreSQL

Ce guide vous apprend à migrer votre schéma Oracle vers Azure Database pour PostgreSQL. 

Pour obtenir des conseils de migration détaillés et complets, consultez les [Ressources du guide de migration](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Prérequis

Pour migrer votre schéma Oracle vers Azure Database pour PostgreSQL, vous devez : 

- Vérifiez que votre environnement source est pris en charge. 
- Téléchargez la dernière version d’[ora2pg](https://ora2pg.darold.net/). 
- Avoir la dernière version du [module DBD](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Vue d’ensemble

PostgreSQL est l’une des bases de données open source les plus évoluées au monde. Cet article explique comment utiliser l’utilitaire gratuit ora2pg pour migrer une base de données Oracle vers PostgreSQL. Vous pouvez utiliser ora2pg, un outil gratuit, pour migrer une base de données Oracle ou MySQL vers un schéma compatible PostgreSQL. L’utilitaire connecte votre base de données Oracle, l’analyse automatiquement et extrait sa structure ou ses données. Ensuite, ora2pg génère des scripts SQL que vous pouvez charger dans votre base de données PostgreSQL. ora2pg peut être utilisé pour diverses tâches, dont l’ingénierie à rebours d’une base de données Oracle, la migration d’une base de données d’entreprise volumineuse ou la simple réplication de données Oracle dans une base de données PostgreSQL. Il est facile à utiliser et ne nécessite aucune connaissance en bases de données Oracle autre que la possibilité de fournir les paramètres nécessaires pour se connecter à la base de données Oracle.

> [!NOTE]
> Pour plus d’informations sur l’utilisation de la dernière version d’ora2pg, consultez la [documentation d’ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Architecture de migration ora2pg classique

![Capture d’écran de l’architecture de migration ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Après l’approvisionnement de la machine virtuelle et de la base de données Azure pour PostgreSQL, deux configurations sont nécessaires pour activer la connectivité entre elles : **autoriser les services Azure** et **appliquer la connexion SSL**, représentée comme suit :

- Panneau **Sécurité de la connexion** -> **Autoriser l’accès aux services Azure** -> Activé

- Panneau **Sécurité de la connexion** -> **Paramètres SSL** -> **Appliquer la connexion SSL** -> Désactivé

### <a name="recommendations"></a>Recommandations

- Pour améliorer les performances des opérations d’évaluation ou d’exportation sur le serveur Oracle, collectez les statistiques en procédant ainsi :

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Exportez les données à l’aide de la commande COPY au lieu d’INSERT.

- Évitez d’exporter des tables avec leurs clés étrangères, contraintes et index. Cela rend l’importation des données dans PostgreSQL plus lente.

- Créez des vues matérialisées à l’aide de la **clause no data** et actualisez-les ultérieurement.

- Si possible, implémentez des index uniques dans des vues matérialisées. Cela rendra l’actualisation plus rapide avec la syntaxe `REFRESH MATERIALIZED VIEW CONCURRENTLY`.



## <a name="pre-migration"></a>Prémigration 

Après avoir vérifié que votre environnement source est pris en charge et vérifié que les conditions préalables sont satisfaites, vous êtes prêt à démarrer l’étape de prémigration. Cette partie du processus inclut de dresser l’inventaire des bases de données que vous devez migrer, d’évaluer celles-ci en lien avec des problèmes de migration ou des blocages potentiels, et de résoudre les problèmes que vous pourriez avoir découverts. Pour les migrations hétérogènes telles qu’Oracle vers Azure Database pour PostgreSQL, cette étape implique également la conversion du ou des schémas des bases de données sources pour être compatibles avec l’environnement cible.

### <a name="discover"></a>Découvrez

L’objectif de la phase de découverte est d’identifier les sources de données existantes, ainsi que divers détails concernant les fonctionnalités que votre entreprise utilise afin d’acquérir une meilleure compréhension de la migration et de planifier celle-ci. Ce processus implique une analyse du réseau pour identifier toutes les instances Oracle de votre organisation, ainsi que la version et les fonctionnalités utilisées.

Les scripts de pré-évaluation Microsoft Oracle s’exécutent sur la base de données Oracle. Les scripts de pré-évaluation sont un ensemble de requêtes qui atteignent les métadonnées Oracle et fournissent les éléments suivants :

- Inventaire de base de données, y compris le nombre d’objets par schéma, leur type et leur état.

- Une estimation approximative des données brutes dans chaque schéma (en fonction des statistiques).

- Le dimensionnement des tables dans chaque schéma.

- Le nombre de lignes de code par package, fonction, procédure, etc.

Téléchargez les scripts associés à partir du [site web ora2pg](http://ora2pg.darold.net/).

### <a name="assess"></a>Évaluer

À l’issue de l’inventaire de la ou des bases de données Oracle pour avoir une idée de la taille de la base de données et des difficultés potentielles, l’étape suivante consiste à exécuter l’évaluation.

Il n’est pas facile d’estimer le coût d’un processus de migration d’Oracle vers PostgreSQL. Pour obtenir une bonne évaluation de ce coût de migration, ora2pg inspecte tous les objets de base de données, toutes les fonctions et les procédures stockées pour détecter s’il existe encore des objets et du code PL/SQL qui ne peuvent pas être convertis automatiquement par ora2pg.

ora2pg dispose d’un mode d’analyse de contenu qui inspecte la base de données Oracle pour générer un rapport texte sur ce que la base de données Oracle contient et ce qui ne peut pas être exporté.

Pour activer le mode **analyse et rapport**, utilisez le type exporté `SHOW_REPORT` comme indiqué dans la commande suivante :

```
ora2pg -t SHOW_REPORT
```

Une fois la base de données analysée, ora2pg, avec sa capacité à convertir du code SQL et PL/SQL d’une syntaxe Oracle en PostgreSQL, peut aller plus loin en estimant les problèmes de code et le temps nécessaire pour effectuer une migration complète de la base de données.

Pour estimer le coût de la migration en jours-hommes, ora2pg vous permet d’utiliser une directive de configuration appelée ESTIMATE_COST, qui peut également être activée en ligne de commande :

```
ora2pg -t SHOW_REPORT --estimate_cost
```

L’unité de migration par défaut représente environ cinq minutes pour un expert PostgreSQL. S’il s’agit de votre première migration, vous pouvez augmenter cette valeur à l’aide de la directive de configuration COST_UNIT_VALUE ou de l’option de ligne de commande --cost_unit_value.

La dernière ligne du rapport affiche le code de migration estimé total en jours-hommes après le nombre d’unités de migration estimé pour chaque objet.

Cette unité de migration représente environ cinq minutes pour un expert PostgreSQL. S’il s’agit de votre première migration, vous pouvez augmenter la valeur par défaut à l’aide de la directive de configuration COST_UNIT_VALUE ou de l’option de ligne de commande --cost_unit_value. Vous trouverez ci-dessous des variantes de l’évaluation a) évaluation des tables b) évaluation des colonnes c) évaluation du schéma avec la valeur cost_unit par défaut (5 min) d) évaluation du schéma avec 10 minutes comme unité de coût.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

La sortie de l’évaluation de schéma est illustrée ci-dessous :

**Niveau de migration : B-5**

Niveaux de migration :

A - Une migration qui peut être exécutée automatiquement

B - Une migration avec réécriture du code et un coût inférieur ou égal à 5 jours-hommes

C - Une migration avec réécriture du code et un coût supérieur à 5 jours-hommes

Niveaux techniques :

1 = trivial : aucune fonction stockée et aucun déclencheur

2 = facile : aucune fonction stockée, mais avec des déclencheurs, aucune réécriture manuelle

3 = simple : fonctions stockées et/ou déclencheurs, aucune réécriture manuelle

4 = manuel : aucune fonction stockée, mais des déclencheurs ou des vues avec réécriture du code

5 = difficile : fonctions stockées et/ou déclencheurs avec réécriture du code

L’évaluation se compose d’une lettre (A ou B) pour indiquer si la migration a besoin d’une réécriture manuelle ou non, et d’un nombre compris entre 1 et 5 pour indiquer le niveau de difficulté technique. Vous disposez d’une option supplémentaire -human_days_limit pour spécifier le nombre de jours-hommes à partir duquel le niveau de migration doit être défini sur C, pour indiquer si la migration nécessite une grande quantité de travail et une gestion de projet complète avec support pour la migration. La valeur par défaut est 10 jours-hommes. Vous pouvez utiliser la directive de configuration HUMAN_DAYS_LIMIT pour modifier cette valeur par défaut de façon permanente.

Cette fonctionnalité a été développée pour vous aider à décider de quelle base de données pourrait être migrée en premier et quelle est l’équipe qui doit être mobilisée.

### <a name="convert"></a>Convertir

Avec les migrations à temps d’arrêt minimal, la source que vous migrez continue de changer, en dérivant de la cible en matière de données et de schéma après la migration ponctuelle. Lors de la phase de **synchronisation des données**, vous devez vous assurer que toutes les modifications apportées à la source sont capturées et appliquées à la cible quasiment en temps réel. Après avoir vérifié que toutes les modifications de la source ont été appliquées à la cible, vous pouvez basculer vers l’environnement cible.

Lors de cette étape de la migration, la conversion ou la traduction du code Oracle et des DDL en PostgreSQL se produit. L’outil ora2pg exporte automatiquement les objets Oracle dans un format PostgreSQL. Pour ces objets générés, certains ne sont pas compilés dans la base de données PostgreSQL sans modification manuelle.  
Le processus de compréhension des éléments qui nécessitent une intervention manuelle consiste à compiler les fichiers générés par ora2pg sur la base de données PostgreSQL, à vérifier le journal et à apporter les modifications nécessaires jusqu’à ce que toute la structure de schéma soit compatible avec la syntaxe PostgreSQL.


#### <a name="create-migration-template"></a>Créer un modèle de migration 

Tout d’abord, il est recommandé de créer le modèle de migration prêt à l’emploi fourni avec ora2pg. Les deux options, --project_base et --init_project, quand elles sont utilisées, indiquent à ora2pg qu’il doit créer un modèle de projet avec une arborescence de travail, un fichier de configuration et un script pour exporter tous les objets de la base de données Oracle. Pour plus d’informations, consultez la [documentation de d’ora2pg](https://ora2pg.darold.net/documentation.html).

   Utilisez la commande suivante : 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Exemple de sortie : 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

Le répertoire sources/ contient le code Oracle, le répertoire schema/ contient le code porté en PostgreSQL. Le répertoire reports/ contient les rapports HTML avec l’évaluation des coûts de migration.


Une fois la structure de projet créée, un fichier de configuration générique est créé. Définissez la connexion à la base de données Oracle ainsi que les paramètres de configuration appropriés dans la configuration.  Reportez-vous à la documentation ora2pg pour comprendre ce qui peut être configuré dans le fichier de configuration et comment.


#### <a name="export-oracle-objects"></a>Exporter des objets Oracle

Ensuite, exportez les objets Oracle en tant qu’objets PostgreSQL en exécutant le fichier export_schema.sh.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
   SET namespace="/app/migration/mig_project"
   
   ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
   %namespace%/config/ora2pg.conf
   ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
   %namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
   ora2pg -p -t PACKAGE -o packages.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
   ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
   ora2pg -p -t PROCEDURE -o procs.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
   ora2pg -t SEQUENCE -o sequences.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
   ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
   %namespace%/config/ora2pg.conf
   ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
   %namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
   ```

   Utilisez la commande suivante pour extraire les données :

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Compiler des fichiers

Enfin, compilez tous les fichiers sur le serveur Azure Database pour PostgreSQL. Il est désormais possible de choisir de charger les fichiers DDL générés manuellement ou d’utiliser le deuxième script import_all.sh pour importer ces fichiers de manière interactive.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   Commande d’importation de données :

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

Pendant la compilation des fichiers, vérifiez les journaux et corrigez les syntaxes nécessaires qu’ora2pg n’a pas pu convertir directement.

Pour plus d’aide sur le contournement des problèmes, consultez le livre blanc [Solutions de contournement pour la migration d’Oracle vers Azure Database pour PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf).

## <a name="migrate"></a>Migrate 

Une fois les conditions préalables réunies et les tâches associées à la phase **Prémigration** accomplies, vous êtes prêt à effectuer la migration du schéma et des données.

### <a name="migrate-schema-and-data"></a>Migrer le schéma et les données

Une fois les correctifs en place, une version stable de la base de données est prête pour le déploiement.

À ce stade, il vous suffit d’exécuter les commandes d’importation *psql*, en pointant vers les fichiers contenant le code modifié afin de compiler les objets de base de données sur la base de données PostgreSQL et d’importer les données.

Au cours de cette étape, il est possible d’implémenter un certain niveau de parallélisme lors de l’importation des données.

### <a name="data-sync-and-cutover"></a>Synchronisation des données et basculement

Avec les migrations en ligne (à temps d’arrêt minimal), la source que vous migrez continue de changer, en dérivant de la cible en matière de données et de schéma après la migration ponctuelle. Lors de la phase de **synchronisation des données**, vous devez vous assurer que toutes les modifications apportées à la source sont capturées et appliquées à la cible quasiment en temps réel. Après avoir vérifié que toutes les modifications de la source ont été appliquées à la cible, vous pouvez basculer vers l’environnement cible.

Après le mars 2019, si vous souhaitez effectuer une migration en ligne, envisagez d’utiliser Attunity Replicate pour Microsoft Migrations ou Striim.

Pour la migration *différentielle/incrémentielle* à l’aide d’ora2pg, la technique consiste à appliquer à chaque table une requête qui applique un filtre (cut) par date ou heure, etc., et, après avoir finalisé la migration, à appliquer une deuxième requête qui migre les données restantes.

Dans la table de données source, migrez tout d’abord toutes les données d’historique. Voici un exemple :

```
select * from table1 where filter_data < 01/01/2019
```

Vous pouvez interroger les modifications apportées depuis la migration initiale en exécutant une commande semblable à la suivante :

```
select * from table1 where filter_data >= 01/01/2019
```

Dans ce cas, il est recommandé d’améliorer la validation en vérifiant la parité des données des deux côtés, source et cible.

## <a name="post-migration"></a>Post-migration 

Une fois la phase de **migration** accomplie, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de manière aussi fluide et efficace que possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, l’accomplissement de cette tâche nécessitera d’apporter des changements aux applications.

### <a name="perform-tests"></a>Effectuer des tests

Une fois les données migrées vers la cible, effectuez des tests sur les bases de données pour vérifier que les applications s’exécutent correctement sur la cible après la migration.

Pour garantir que la source et la cible sont correctement migrées, exécutez les scripts de validation de données manuels sur les bases de données Oracle (source) et PostgreSQL (cible).

Idéalement, si les bases de données source et cible ont un chemin d’accès réseau, ora2pg doit être utilisé pour la validation des données. L’utilisation de l’action TEST vous permet de vérifier que tous les objets de la base de données Oracle ont été créés sous PostgreSQL. Exécutez la commande comme indiqué :

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimiser

La phase postmigration est cruciale pour résoudre les problèmes de justesse et d’exhaustivité des données ainsi que pour gérer les problèmes de performances liés à la charge de travail.

## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire concernant ce scénario de migration, consultez les ressources suivantes qui ont été développées dans le cadre d’un engagement de projet de migration réel.

| **Lien du titre** | **Description**    |
| -------------- | ------------------ |
| [Livre de recettes sur la migration d’Oracle vers Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | L’objectif de ce document est de fournir aux architectes, consultants, administrateurs de bases de données et rôles associés un guide pour migrer rapidement les charges de travail d’Oracle vers Azure Database pour PostgreSQL à l’aide de l’outil ora2pg. |
| [Solutions de contournement pour la migration d’Oracle vers Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | L’objectif de ce document est de fournir aux architectes, consultants, administrateurs de base de données et rôles associés un guide pour la correction rapide et le contournement des problèmes lors de la migration de charges de travail d’Oracle vers Azure Database pour PostgreSQL. |
| [Procédure d’installation d’ora2pg sur Windows ou Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Ce document est destiné à être utilisé comme guide d’installation rapide pour permettre la migration de schémas et de données d’Oracle vers Azure Database pour PostgreSQL à l’aide de l’outil ora2pg sur Windows ou Linux. Pour plus d’informations sur l’outil, consultez http://ora2pg.darold.net/documentation.html. |

Ces ressources ont été développées dans le cadre du programme Data SQL Ninja, qui est sponsorisé par l’équipe d’ingénierie Groupe de données Azure. La charte fondamentale du programme Data SQL Ninja a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux opportunités de migration de plateforme de données vers la plateforme de données Azure de Microsoft. Si vous pensez que votre organisation aimerait participer au programme Data SQL Ninja, contactez votre équipe en charge des compte et demandez-lui de soumettre une candidature.


### <a name="contact-support"></a>Contacter le support technique

Si vous avez besoin d’aide pour vos migrations au-delà des outils ora2pg, contactez l’alias [@Ask Azure DB pour PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) pour plus d’informations sur les autres options de migration.

## <a name="next-steps"></a>Étapes suivantes

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez l’article [Services et outils disponibles pour la migration de données](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Pour plus d'informations, consultez les rubriques suivantes : 
- [Documentation relative à Azure Database pour PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [Documentation relative à ora2pg](https://ora2pg.darold.net/documentation.html)
- [Site web de PostgreSQL](https://www.postgresql.org/)
- [Prise en charge des transactions autonomes dans PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

Pour le contenu vidéo : 
- [Vue d’ensemble du parcours de migration et des outils/services recommandés pour effectuer l’évaluation et la migration](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).