---
title: 'MySQL vers Azure SQL Database : Guide de migration'
description: Ce guide vous apprend à migrer vos bases de données MySQL vers Azure SQL Database à l’aide de l’outil Assistant Migration SQL Server pour MySQL (SSMA pour MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: ff7b2115b396bf42cdeffa9c58bffb1802e980d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721883"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Guide de migration : MySQL vers Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ce guide vous apprend à migrer votre base de données MySQL vers Azure SQL Database à l’aide de l’outil Assistant Migration SQL Server pour MySQL (SSMA pour MySQL). 

Pour obtenir d’autres guides de migration, consultez [Migration de base de données](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prérequis

Pour migrer votre base de données MySQL vers Azure SQL Database, vous devez :

- vérifier que votre environnement source est pris en charge. Actuellement, les versions 5.6 et 5.7 de MySQL sont prises en charge. 
- [Assistant de migration SQL Server pour MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>Prémigration 

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre migration.

### <a name="assess"></a>Évaluer 

En utilisant l’[outil Assistant Migration SQL Server pour MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257), vous pouvez examiner les données et les objets de base de données et évaluer les bases de données en vue de leur migration.

Pour créer une évaluation, procédez comme suit.

1. Ouvrez l’Assistant Migration SQL Server pour MySQL. 
1. Sélectionnez **Fichier** dans le menu, puis choisissez **Nouveau projet**. Indiquez le nom du projet et un emplacement où enregistrer votre projet. Choisissez **Azure SQL Database** comme cible de migration. 

   ![Nouveau projet](./media/mysql-to-sql-database-guide/new-project.png)

1. Choisissez **Se connecter à MySQL** et fournissez les détails de connexion pour connecter votre serveur MySQL. 

   ![Se connecter à MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Cliquez avec le bouton droit sur le schéma MySQL dans **Explorateur de métadonnées MySQL** et choisissez **Créer un rapport**. Vous pouvez également sélectionner **Créer un rapport** dans la barre de navigation supérieure. 

   ![Créer un rapport](./media/mysql-to-sql-database-guide/create-report.png)

1. Dans le rapport HTML, examinez les statistiques de conversion, ainsi que les erreurs et avertissements. Analysez-les pour comprendre les problèmes de conversion et les solutions. 

   Ce rapport est également accessible à partir du dossier de projets SSMA sélectionné dans le premier écran. Dans l’exemple ci-dessus, localisez le fichier report.xml dans :
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   et ouvrez-le dans Excel pour obtenir un inventaire des objets MySQL et une estimation de l’effort requis pour effectuer les conversions de schémas.

    ![Rapport de conversion](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction des exigences, si nécessaire. Pour ce faire, procédez comme suit : 

1. Sélectionnez **Outils** dans le menu. 
1. Sélectionnez **Paramètres du projet**. 
1. Sélectionnez l’onglet **Mappage de types**. 

   ![Mappages de types](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Vous pouvez changer le mappage de type pour chaque table en sélectionnant la table dans l’**Explorateur de métadonnées MySQL**. 

### <a name="convert-schema"></a>Convertir le schéma 

Pour convertir le schéma, effectuez les étapes suivantes : 

1. (Facultatif) Pour convertir des requêtes dynamiques ou ad hoc, cliquez avec le bouton droit sur le nœud, puis choisissez **Ajouter une instruction**. 
1. Choisissez **Se connecter à Azure SQL Database** dans la barre de navigation supérieure et fournissez les détails de la connexion. Vous pouvez vous connecter à une base de données existante ou fournir un nouveau nom afin de créer une base de données sur le serveur cible.

   ![Se connecter à SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Cliquez avec le bouton droit sur le schéma, puis choisissez **Convertir le schéma**. 

   ![Convertir le schéma](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Une fois la conversion du schéma terminée, comparez le code converti au code d’origine pour identifier les problèmes potentiels. 

   Comparez les objets convertis aux objets d’origine : 

   ![ Comparer et examiner l’objet ](./media/mysql-to-sql-database-guide/table-comparison.png)

   Comparez les procédures converties aux procédures d’origine : 

   ![Comparer et examiner le code objet](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate"></a>Migrer 

Une fois que vous avez terminé l’évaluation de vos bases de données et que vous traité toutes les anomalies, l’étape suivante consiste à exécuter le processus de migration. La migration comprend deux étapes : la publication du schéma et la migration des données. 

Pour publier le schéma et migrer les données, procédez comme suit : 

1. Cliquez avec le bouton droit sur la base de données dans l’**explorateur de métadonnées Azure SQL Database** et choisissez **Synchroniser avec la base de données**. Cette action publie le schéma MySQL sur Azure SQL Database.

   ![Synchroniser avec la base de données](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Passez en revue le mappage entre votre projet source et votre cible :

   ![Examen de la synchronisation avec la base de données](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Cliquez avec le bouton droit sur le schéma MySQL dans **Explorateur de métadonnées MySQL** et choisissez **Migrer les données**. Vous pouvez également sélectionner **Migrer les données** dans la barre de navigation supérieure. 

   ![Migration des données](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Une fois la migration terminée, affichez le rapport **Migration des données** : 

   ![Rapport de migration des données](./media/mysql-to-sql-database-guide/data-migration-report.png)

1.  Validez la migration en examinant les données et le schéma sur Azure SQL Database en utilisant SQL Server Management Studio (SSMS).

    ![Valider dans SSMA](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>Postmigration 

Une fois la phase de **Migration** terminée, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de la manière la plus fluide et efficace possible.

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

| Titre/lien     | Description    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Outil et modèle d’évaluation de charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil fournit les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une création de rapports et des calculs simples en un clic qui permettent d’accélérer considérablement les évaluations d’un vaste domaine en fournissant un processus de décision de plateforme cible automatisé et uniforme. |

Ces ressources ont été développées dans le cadre du programme Data SQL Ninja, qui est sponsorisé par l’équipe d’ingénierie Groupe de données Azure. La charte fondamentale du programme Data SQL Ninja a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux opportunités de migration de plateforme de données vers la plateforme de données Azure de Microsoft. Si vous pensez que votre organisation aimerait participer au programme Data SQL Ninja, contactez votre équipe en charge des compte et demandez-lui de soumettre une candidature.

## <a name="next-steps"></a>Étapes suivantes 

- N’oubliez pas de consulter l’[Outil de calcul du coût total de possession (TCO) Azure](https://aka.ms/azure-tco) pour vous aider à estimer les économies que vous pouvez réaliser en migrant vos charges de travail vers Azure.

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez l’article [Services et outils disponibles pour la migration de données](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- Pour obtenir d’autres guides de migration, consultez [Migration de base de données](https://datamigration.microsoft.com/). 

Pour les vidéos, consultez : 
- [Vue d’ensemble du parcours de migration et des outils/services recommandés pour effectuer l’évaluation et la migration](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
