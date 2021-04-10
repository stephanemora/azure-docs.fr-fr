---
title: 'Access vers Azure SQL Database : Guide de migration'
description: Ce guide explique comment migrer des bases de données Microsoft Access vers Azure SQL Database à l’aide de l’outil Assistant Migration SQL Server pour Access (SSMA pour Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e323b1c15d78da4e8c1a82ae8848df7f59b0dd87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657271"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guide de migration : Access vers Azure SQL Database

Ce guide de migration explique comment migrer des bases de données Microsoft Access vers Azure SQL Database à l’aide de l’outil Assistant Migration SQL Server pour Access.

Pour obtenir d’autres guides de migration, consultez [Migration de base de données](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prérequis

Pour migrer votre base de données Access vers Azure SQL Database, vous devez :

- vérifier que votre environnement source est pris en charge. 
- [Assistant Migration SQL Server pour Access](https://www.microsoft.com/download/details.aspx?id=54255). 

## <a name="pre-migration"></a>Prémigration

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre migration.


### <a name="assess"></a>Évaluer 

Créez une évaluation à l’aide de l’[outil Assistant Migration SQL Server pour Access](https://www.microsoft.com/download/details.aspx?id=54255). 

Pour créer une évaluation, effectuez les étapes suivantes : 

1. Ouvrez l’outil Assistant Migration SQL Server pour Access. 
1. Sélectionnez **Fichier**, puis choisissez **Nouveau projet**. Entrez un nom pour votre projet de migration. 

   ![Choisir Nouveau projet](./media/access-to-sql-database-guide/new-project.png)

1. Sélectionnez **Ajouter des bases de données**, puis choisissez les bases de données à ajouter à votre nouveau projet. 

   ![Choisir Ajouter des bases de données](./media/access-to-sql-database-guide/add-databases.png)

1. Dans **Explorateur de métadonnées Access**, cliquez avec le bouton droit sur la base de données, puis choisissez **Créer un rapport**. 

   ![Cliquer avec le bouton droit sur la base de données, puis choisir Créer un rapport](./media/access-to-sql-database-guide/create-report.png)

1. Examinez l’exemple d’évaluation. Par exemple : 

   ![Examiner l’évaluation de l’exemple de rapport](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction des exigences, si nécessaire. Pour ce faire, procédez comme suit :

1. Sélectionnez **Outils** dans le menu. 
1. Sélectionnez **Paramètres du projet**. 
1. Sélectionnez l’onglet **Mappage de types**. 

   ![Mappages de types](./media/access-to-sql-database-guide/type-mappings.png)

1. Vous pouvez changer le mappage de type pour chaque table en la sélectionnant dans l’**Explorateur de métadonnées Access**.


### <a name="convert-schema"></a>Convertir le schéma

Pour convertir des objets de base de données, procédez comme suit : 

1. Sélectionnez **Se connecter à Azure SQL Database** et fournissez les détails de la connexion.

   ![Connexion à Azure SQL Database](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Cliquez avec le bouton droit sur la base de données dans **l’Explorateur de métadonnées Access**, puis choisissez **Convertir le schéma**. Vous pouvez également choisir **Convertir le schéma** à partir de la barre de navigation supérieure après avoir sélectionné votre base de données.

   ![Cliquer avec le bouton droit sur la base de données, puis choisir Convertir le schéma](./media/access-to-sql-database-guide/convert-schema.png)

   Comparer les requêtes converties aux requêtes d’origine : 

   ![Les requêtes converties peuvent être comparées au code source](./media/access-to-sql-database-guide/query-comparison.png)

   Comparer les objets convertis aux objets d’origine : 

   ![Les objets convertis peuvent être comparés à la source](./media/access-to-sql-database-guide/table-comparison.png)

1. (Facultatif) Pour convertir un objet individuel, cliquez avec le bouton droit sur l’objet et choisissez **Convertir le schéma**. Les objets convertis apparaissent en gras dans l’**Explorateur de métadonnées Access** : 

   ![Les objets en gras dans l’Explorateur de métadonnées ont été convertis](./media/access-to-sql-database-guide/converted-items.png)
 
1. Sélectionnez **Passer en revue les résultats** dans le volet de Sortie, puis examinez les erreurs dans le volet **Liste d’erreurs**. 


## <a name="migrate"></a>Migrer

Une fois que vous avez terminé l’évaluation de vos bases de données et que vous traité toutes les anomalies, l’étape suivante consiste à exécuter le processus de migration. La migration des données est une opération de chargement en masse qui déplace des lignes de données vers Azure SQL Database dans des transactions. Le nombre de lignes à charger dans Azure SQL Database dans chaque transaction est configuré dans les paramètres du projet.

Pour migrer des données à l’aide de SSMA pour Access, procédez comme suit : 

1. Si ce n’est déjà fait, sélectionnez **Se connecter à Azure SQL Database** et fournissez les détails de la connexion. 
1. Cliquez avec le bouton droit sur la base de données dans l’**explorateur de métadonnées Azure SQL Database** et choisissez **Synchroniser avec la base de données**. Cette action publie le schéma MySQL sur Azure SQL Database.

   ![Synchroniser avec la base de données](./media/access-to-sql-database-guide/synchronize-with-database.png)

   Passez en revue le mappage entre votre projet source et votre cible :

   ![Examiner la synchronisation avec la base de données](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Utilisez **l’Explorateur de métadonnées Access** pour cocher les cases à côté des éléments que vous souhaitez migrer. Si vous souhaitez migrer l’intégralité de la base de données, cochez la case à côté de la base de données. 
1. Cliquez avec le bouton droit sur la base de données ou l’objet que vous souhaitez migrer, puis choisissez **Migrer les données**. 
   Pour migrer des données pour une base de données entière, cochez la case à côté du nom de la base de données. Pour migrer des données à partir de tables individuelles, développez la base de données, développez Tables, puis cochez la case à côté de la table. Pour omettre certaines données des tables individuelles, décochez la case.

    ![Migration des données](./media/access-to-sql-database-guide/migrate-data.png)

    Supprimer les données migrées : 

    ![Migrer l’examen des données](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Connectez-vous à votre compte Azure SQL Database à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) et validez la migration en examinant les données et le schéma.

   ![Valider dans SSMA](./media/access-to-sql-database-guide/validate-data.png)



## <a name="post-migration"></a>Post-migration 

Une fois la phase de **migration** accomplie, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de manière aussi fluide et efficace que possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, l’accomplissement de cette tâche nécessitera d’apporter des changements aux applications.

### <a name="perform-tests"></a>Effectuer des tests

L’approche de test d’une migration de base de données comprend les activités suivantes :

  1. **Développer des tests de validation**. pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Vous devez créer les requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.

  2. **Configurer un environnement de test**. l’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.

  3. **Exécuter des tests de validation**. exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.

  4. **Exécuter des tests de performances**. exécutez un test de performances sur la source et sur la cible, puis analysez et comparez les résultats.

### <a name="optimize"></a>Optimiser

La phase postmigration est cruciale pour résoudre les problèmes de justesse et d’exhaustivité des données ainsi que pour gérer les problèmes de performances liés à la charge de travail.

Pour plus d’informations sur ces problèmes et les étapes spécifiques pour les atténuer, consultez le [Guide de validation et d’optimisation post-migration](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire concernant ce scénario de migration, consultez les ressources suivantes qui ont été développées dans le cadre d’un engagement de projet de migration réel.

| **Titre/lien**                                                                                                                                          | **Description**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Outil et modèle d’évaluation de charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil suggère les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une génération de rapports et des calculs simples en un clic, qui permettent d’accélérer considérablement les évaluations d’un vaste domaine en fournissant un processus de décision automatisé et uniforme concernant le choix de la plateforme cible. |


Ces ressources ont été développées dans le cadre du programme Data SQL Ninja, qui est sponsorisé par l’équipe d’ingénierie Groupe de données Azure. La charte fondamentale du programme Data SQL Ninja a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux opportunités de migration de plateforme de données vers la plateforme de données Azure de Microsoft. Si vous pensez que votre organisation aimerait participer au programme Data SQL Ninja, contactez votre équipe en charge des compte et demandez-lui de soumettre une candidature.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans les différents scénarios de migration de données et de base de données ainsi que leurs tâches spécialisées, consultez [Services et outils de migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL Database, consultez :
   - [Vue d’ensemble de SQL Database](../../database/sql-database-paas-overview.md)
   - [Calculatrice du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Pour plus d’informations sur l’infrastructure et le cycle d’adoption pour les migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour savoir comment exécuter un test A/B sur la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).

