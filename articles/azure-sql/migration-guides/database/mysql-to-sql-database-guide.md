---
title: 'MySQL vers Azure SQL Database : Guide de migration'
description: Dans ce guide, vous découvrez comment migrer vos bases de données MySQL vers une base de données Azure SQL à l’aide de l’Assistant Migration SQL Server pour MySQL (SSMA pour MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: cawrites
ms.author: chadam
ms.date: 03/19/2021
ms.openlocfilehash: 3b9f286796a832d075977cda2eda54f990bbb3c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524953"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Guide de migration : MySQL vers Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Dans ce guide, vous allez découvrir [comment migrer](https://azure.microsoft.com/migration/migration-journey) votre base de données MySQL vers une base de données Azure SQL à l’aide de l’Assistant [Migration SQL Server](https://azure.microsoft.com/migration/sql-server/) pour MySQL (SSMA pour MySQL). 

Pour d’autres guides sur la migration, consultez [Guide sur la migration de bases de données Azure](/data-migration). 

## <a name="prerequisites"></a>Prérequis

Avant de commencer à migrer votre base de données MySQL vers une base de données SQL, procédez comme suit :

- Vérifiez que votre environnement source est pris en charge. Actuellement, les versions 5.6 et 5.7 de MySQL sont prises en charge. 
- Téléchargez et installez [Assistant Migration SQL Server pour MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Vérifiez que vous disposez d’une connectivité et d’autorisations suffisantes pour accéder à la fois à la source et à la cible.

## <a name="pre-migration"></a>Prémigration 

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre [migration vers le cloud Azure](https://azure.microsoft.com/migration).

### <a name="assess"></a>Évaluer 

En utilisant l’Assistant Migration SQL Server (SSMA) pour MySQL afin d’examiner les données et les objets de base de données, et évaluer les bases de données pour la migration. 

Pour créer une évaluation, procédez comme suit :

1. Ouvrez [SSMA pour MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Sélectionnez **Fichier**, puis **Nouveau projet**. 
1. Dans le volet **Nouveau projet**, entrez un nom et un emplacement pour votre projet, puis, dans la liste déroulante **Migrer vers**, sélectionnez **Azure SQL Database**. 
1. Sélectionnez **OK**.

   ![Capture d’écran du volet « Nouveau projet » vous permettant de saisir le nom, l’emplacement et la cible de votre projet de migration.](./media/mysql-to-sql-database-guide/new-project.png)

1. Sélectionnez l’onglet **Se connecter à MySQL**, puis fournissez les détails de connexion de votre serveur MySQL. 

   ![Capture d’écran du volet « Se connecter à MySQL » permettant d’indiquer les connexions à la source.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Dans le volet **Explorateur de métadonnées MySQL**, cliquez avec le bouton droit sur le schéma MySQL, puis sélectionnez **Créer un rapport**. Vous pouvez également sélectionner l’onglet **Créer un rapport** en haut à droite.

   ![Capture d’écran des liens « Créer un rapport » dans SSMA pour MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Examinez le rapport HTML pour comprendre les statistiques, les erreurs et les avertissements relatifs à la conversion. Analysez-les pour comprendre les problèmes de conversion et les solutions. 
   Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets MySQL et comprendre l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans SSMAProjects est l’emplacement par défaut du rapport. Par exemple : 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Capture d’écran d’un exemple de rapport de conversion dans SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction des besoins, si nécessaire. Pour ce faire : 

1. Sélectionnez **Outils**, puis **Paramètres du projet**.  
1. Sélectionnez l’onglet **Mappage de types**. 

   ![Capture d’écran du volet « Mappage de types » dans SSMA pour MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Vous pouvez changer le mappage de type pour chaque table en sélectionnant le nom de la table dans le volet **Explorateur de métadonnées MySQL**. 

### <a name="convert-the-schema"></a>Convertir le schéma 

Pour convertir le schéma, procédez comme suit : 

1. (Facultatif) Pour convertir des requêtes dynamiques ou spécialisées, cliquez avec le bouton droit sur le nœud, puis sélectionnez **Ajouter une instruction**. 

1. Sélectionnez l’onglet **Se connecter à Azure SQL Database**, puis procédez comme suit :

   a. Entrez les informations de connexion à votre base de données SQL.  
   b. Dans la liste déroulante, sélectionnez votre base de données SQL cible. Vous pouvez aussi indiquer un nouveau nom afin de créer une base de données sur le serveur cible.  
   c. Fournissez des informations d’authentification.  
   d. Sélectionnez **Connecter**.

   ![Capture d’écran du volet « Se connecter à Azure SQL Database » dans SSMA pour MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Cliquez avec le bouton droit sur le schéma que vous utilisez, puis sélectionnez **Convertir le schéma**. Vous pouvez également sélectionner l’onglet **Convertir le schéma** en haut à droite.

   ![Capture d’écran de la commande « Convertir le schéma » dans le volet « Explorateur de métadonnées MySQL ».](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Une fois la conversion terminée, vérifiez et comparez les objets convertis aux objets d’origine afin d’identifier les problèmes potentiels et de les traiter en fonction des recommandations. 

   ![Capture d’écran montrant une comparaison des objets convertis aux objets d’origine.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Comparez le texte Transact-SQL converti au code d’origine et passez en revue les recommandations.

   ![Capture d’écran montrant une comparaison des requêtes converties en code source.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Dans le volet **Sortie**, sélectionnez **Vérifier les résultats**, puis examinez les erreurs dans le volet **Liste d’erreurs**. 
1. Enregistrez le projet localement pour un exercice de correction de schéma hors connexion. Pour ce faire, sélectionnez **Fichier** > **Enregistrer le projet**. Cela vous permet d’évaluer les schémas source et cible hors connexion et d’apporter une correction avant de publier le schéma sur votre base de données SQL.

   Comparez les procédures converties aux procédures d’origine, comme illustré ici : 

   ![Capture d’écran montrant une comparaison des procédures converties aux procédures d’origine.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Migrer les bases de données 

Une fois que vous avez évalué vos bases de données et résolu les incohérences, vous pouvez exécuter le processus de migration. La migration comprend deux étapes : la publication du schéma et la migration des données. 

Pour publier le schéma et migrer les données, procédez comme suit : 

1. Publiez le schéma. Dans le volet **Explorateur de métadonnées Azure SQL Database**, cliquez avec le bouton droit sur la base de données, puis sélectionnez **Synchroniser avec la base de données**. Cette action publie le schéma MySQL sur votre base de données SQL.

   ![Capture d’écran du volet « Synchroniser avec la base de données » pour la vérification du mappage de base de données.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrez les données. Dans le volet **Explorateur de métadonnées MySQL**, cliquez avec le bouton droit sur le schéma MySQL que vous souhaitez migrer, puis sélectionnez **Migrer les données**. Vous pouvez également sélectionner l’onglet **Migrer les données** en haut à droite.

   Pour migrer des données pour une base de données entière, cochez la case à côté du nom de la base de données. Pour migrer des données à partir de tables individuelles, développez la base de données, développez **Tables**, puis cochez la case à côté de la table. Pour omettre certaines données des tables individuelles, décochez la case.

   ![Capture d’écran de la commande « Migrer les données » dans le volet « Explorateur de métadonnées MySQL ».](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Une fois la migration terminée, affichez le **Rapport de migration des données**.
   
   ![Capture d’écran du rapport de migration des données.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Connectez-vous à votre base de données SQL à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) et validez la migration en examinant les données et le schéma.

   ![Capture d’écran de SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Post-migration 

Une fois la phase de *Migration* terminée, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de la manière la plus fluide et efficace possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, l’accomplissement de cette tâche nécessitera d’apporter des changements aux applications.

### <a name="perform-tests"></a>Effectuer des tests

L’approche de test pour la migration de base de données comprend les activités suivantes :

1. **Développer des tests de validation** : pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Vous devez créer les requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.

1. **Configurer un environnement de test** : L’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.

1. **Exécuter des tests de validation** : Exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.

1. **Exécuter des tests de performances** : Exécutez des tests de performances sur la source et sur la cible, puis analysez et comparez les résultats.

### <a name="optimize"></a>Optimiser

La phase postmigration est cruciale pour résoudre les problèmes de justesse et d’exhaustivité des données ainsi que pour gérer les problèmes de performances liés à la charge de travail.

Pour plus d’informations sur ces problèmes et les étapes spécifiques pour les atténuer, consultez le [Guide de validation et d’optimisation post-migration](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Ressources de migration

Pour plus d’informations sur l’exécution de ce scénario de migration, consultez la ressource suivante. Elle a été développée pour soutenir un engagement de projet de migration réel.

| Titre | Description |
| --- | --- |
| [Outil et modèle d’évaluation d’une charge de travail de données](https://www.microsoft.com/download/details.aspx?id=103130) | Fournit des suggestions pour les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données qui sont les mieux adaptés pour des charges de travail spécifiées. Il propose des fonctionnalités de génération de rapports et de calculs simples en un clic qui permettent d’accélérer les évaluations d’un vaste domaine en fournissant un processus de décision de plateforme cible automatisé et uniforme. |
|[MySQL vers SQL DB – Utilitaire Comparer les bases de données](https://www.microsoft.com/download/details.aspx?id=103016)|L’utilitaire Comparer les bases de données est une application console Windows permettant de vérifier que les données sont identiques sur les plateformes source et cible. Vous pouvez utiliser cet outil pour comparer efficacement les données jusqu’au niveau de la ligne ou de la colonne sur toutes les tables, lignes et colonnes, ou une partie d’entre elles.|

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme Azure Data de Microsoft.

## <a name="next-steps"></a>Étapes suivantes 

- Pour vous aider à estimer les économies que vous pouvez réaliser en migrant vos charges de travail vers Azure, consultez [l’outil de calcul du coût total de possession Azure](https://aka.ms/azure-tco).

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez [Service et outils disponibles pour les scénarios de migration de données](../../../dms/dms-tools-matrix.md).

- Pour obtenir d’autres guides de migration, consultez le [Guide de migration de base de données Azure](https://datamigration.microsoft.com/). 

- Pour obtenir des vidéos sur la migration, consultez [Vue d’ensemble du parcours de migration, outils et services de migration et d’évaluation recommandés](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).

- Pour plus de [ressources sur la migration cloud](https://azure.microsoft.com/migration/resources/), consultez [Solutions de migration cloud](https://azure.microsoft.com/migration).
