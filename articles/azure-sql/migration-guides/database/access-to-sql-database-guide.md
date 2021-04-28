---
title: 'Access vers Azure SQL Database : Guide de migration'
description: Dans ce guide, vous découvrirez comment migrer vos bases de données Microsoft Access vers une base de données Azure SQL à l’aide de l’outil Assistant Migration SQL Server pour Access (SSMA pour Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 741a47120b9410f91ef7f6de21879aab796e9a5e
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108136725"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guide de migration : Access vers Azure SQL Database

Dans ce guide, vous découvrirez [comment migrer](https://azure.microsoft.com/migration/migration-journey) votre base de données Microsoft Access vers une base de données Azure SQL à l’aide de l’outil Assistant [Migration SQL Server](https://azure.microsoft.com/en-us/migration/sql-server/) pour Access (SSMA pour Access).

Pour d’autres guides sur la migration, consultez [Guide sur la migration de bases de données Azure](/data-migration). 

## <a name="prerequisites"></a>Prérequis

Avant de commencer à migrer votre base de données Access vers une base de données SQL, procédez comme suit :

- Vérifiez que votre environnement source est pris en charge. 
- Téléchargez et installez [Assistant Migration SQL Server pour Access](https://www.microsoft.com/download/details.aspx?id=54255).
- Veillez à avoir de la connectivité et les autorisations suffisantes pour accéder à la source et à la cible.

## <a name="pre-migration"></a>Prémigration

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre [migration vers le cloud Azure](https://azure.microsoft.com/migration).


### <a name="assess"></a>Évaluer 

Utilisez SSMA pour Access afin d’examiner des données et des objets de base de données, ainsi que pour évaluer vos bases de données pour la migration. 

Pour créer une évaluation, procédez comme suit : 

1. Ouvrez [SSMA pour Access](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Sélectionnez **Fichier**, puis **Nouveau projet**. 
1. Fournissez un nom de projet et un emplacement pour votre projet, puis sélectionnez **Azure SQL Database** dans la liste déroulante comme cible de migration. 
1. Sélectionnez **OK**. 

   ![Capture d’écran du volet « Nouveau projet » vous permettant de saisir le nom et l’emplacement de votre projet de migration.](./media/access-to-sql-database-guide/new-project.png)

1. Sélectionnez **Ajouter des bases de données**, puis sélectionnez les bases de données à ajouter à votre nouveau projet. 

   ![Capture d’écran de l’onglet « Ajouter des bases de données » dans SSMA pour Access.](./media/access-to-sql-database-guide/add-databases.png)

1. Dans le volet **Explorateur de métadonnées Access**, cliquez avec le bouton droit sur une base de données, puis sélectionnez **Créer un rapport**. Vous pouvez également sélectionner l’onglet **Créer un rapport** en haut à droite.

   ![Capture d’écran de la commande « Créer un rapport » dans l’Explorateur de métadonnées Access.](./media/access-to-sql-database-guide/create-report.png)

1. Examinez le rapport HTML pour comprendre les statistiques de conversion et les erreurs ou avertissements. Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets Access et comprendre l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans SSMAProjects est l’emplacement par défaut du rapport. Par exemple :

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Capture d’écran d’un exemple d’évaluation de rapport de base de données dans SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction de vos exigences, si nécessaire. Pour cela, procédez de la façon suivante :

1. Dans SSMA pour Access, sélectionnez **Outils**, puis **Paramètres du projet**. 
1. Sélectionnez l’onglet **Mappage de types**. 

   ![Capture d’écran du volet « Mappage de type » dans SSMA pour Access.](./media/access-to-sql-database-guide/type-mappings.png)

1. Vous pouvez changer le mappage de type pour chaque table en sélectionnant le nom de la table dans le volet **Explorateur de métadonnées Access**.


### <a name="convert-the-schema"></a>Convertir le schéma

Pour convertir des objets de base de données, procédez comme suit : 

1. Sélectionnez l’onglet **Se connecter à Azure SQL Database**, puis procédez comme suit :

   a. Entrez les informations de connexion à votre base de données SQL.  
   b. Dans la liste déroulante, sélectionnez votre base de données SQL cible. Vous pouvez aussi entrer un nouveau nom afin de créer une base de données sur le serveur cible.  
   c. Fournissez des informations d’authentification.   
   d. Sélectionnez **Connecter**.

   ![Capture d’écran du volet « Se connecter à Azure SQL Database » pour entrer les détails de connexion.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Dans le volet **Explorateur de métadonnées Access**, cliquez avec le bouton droit sur la base de données, puis sélectionnez **Convertir le schéma**. Vous pouvez également sélectionner votre base de données, puis sélectionner l’onglet **Convertir le schéma**.

   ![Capture d’écran de la commande « Convertir le schéma » dans le volet « Explorateur de métadonnées Access ».](./media/access-to-sql-database-guide/convert-schema.png)

1. Une fois la conversion terminée, comparez les objets convertis aux objets d’origine afin d’identifier les problèmes potentiels et de traiter les problèmes en fonction des recommandations.

   ![Capture d’écran montrant une comparaison des objets convertis aux objets sources.](./media/access-to-sql-database-guide/table-comparison.png)

    Comparez le texte Transact-SQL converti au code d’origine et passez en revue les recommandations.

   ![Capture d’écran montrant une comparaison des requêtes converties en code source.](./media/access-to-sql-database-guide/query-comparison.png) 

1. (Facultatif) Pour convertir un objet individuel, cliquez avec le bouton droit sur l’objet, puis sélectionnez **Convertir le schéma**. Les objets convertis apparaissent en texte gras dans l’**Explorateur de métadonnées Access** : 

   ![Capture d’écran montrant que les objets dans l’Explorateur de métadonnées Access sont convertis.](./media/access-to-sql-database-guide/converted-items.png)
 
1. Dans le volet **Sortie**, sélectionnez l’icône **Examiner les résultats**, puis examinez les erreurs dans le volet **Liste d’erreurs**. 
1. Enregistrez le projet localement pour un exercice de correction de schéma hors connexion. Pour ce faire, sélectionnez **Fichier** > **Enregistrer le projet**. Cela vous permet d’évaluer les schémas source et cible hors connexion et d’apporter une correction avant de les publier sur votre base de données SQL.

## <a name="migrate-the-databases"></a>Migrer les bases de données

Une fois que vous avez évalué vos bases de données et résolu les incohérences, vous pouvez exécuter le processus de migration. La migration des données est une opération de chargement en masse qui déplace des lignes de données vers une base de données Azure SQL dans des transactions. Le nombre de lignes à charger dans votre base de données SQL dans chaque transaction est configuré dans les paramètres du projet.

Pour publier votre schéma et migrer les données à l’aide de SSMA pour Access, procédez comme suit : 

1. Si ce n’est pas déjà fait, sélectionnez **Se connecter à Azure SQL Database** et fournissez les détails de connexion. 

1. Publiez le schéma. Dans le volet **Explorateur de métadonnées Azure SQL Database**, cliquez avec le bouton droit sur la base de données que vous utilisez, puis sélectionnez **Synchroniser avec la base de données**. Cette action publie le schéma MySQL sur la base de données SQL.

1. Dans le volet **Synchroniser avec la base de données**, examinez le mappage entre votre projet source et votre cible :

   ![Capture d’écran du volet « Synchroniser avec la base de données » pour vérifier la synchronisation avec la base de données.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Dans le volet **Explorateur de métadonnées Access**, sélectionnez les cases à cocher à côté des éléments que vous souhaitez migrer. Pour migrer toute la base de données, cochez la case à côté de la base de données. 

1. Migrez les données. Cliquez avec le bouton droit sur la base de données ou l’objet que vous souhaitez migrer, puis sélectionnez **Migrer les données**. Vous pouvez également sélectionner l’onglet **Migrer les données** en haut à droite.  

   Pour migrer des données pour une base de données entière, cochez la case à côté du nom de la base de données. Pour migrer des données à partir de tables individuelles, développez la base de données, développez **Tables**, puis cochez la case à côté de la table. Pour omettre certaines données des tables individuelles, décochez la case.

    ![Capture d’écran de la commande « Migrer les données » dans le volet « Explorateur de métadonnées Access ».](./media/access-to-sql-database-guide/migrate-data.png)

1. Une fois la migration terminée, affichez le **Rapport de migration des données**.  

    ![Capture d’écran du volet « Migrer le rapport de données » montrant un exemple de rapport à consulter.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Connectez-vous à votre base de données Azure SQL à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) et validez la migration en examinant les données et le schéma.

   ![Capture d’écran de l’Explorateur d’objets SQL Server Management Studio pour valider votre migration dans SSMA.](./media/access-to-sql-database-guide/validate-data.png)

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
| [Outil et modèle d’évaluation d’une charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Fournit des suggestions pour les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données qui sont les mieux adaptés pour des charges de travail spécifiées. Il propose des fonctionnalités de génération de rapports et de calculs simples en un clic qui permettent d’accélérer les évaluations d’un vaste domaine en fournissant un processus de décision de plateforme cible automatisé et uniforme. |

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme Azure Data de Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers disponibles pour vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez [Service et outils disponibles pour la migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL Database, consultez :
   - [Vue d’ensemble de SQL Database](../../database/sql-database-paas-overview.md)
   - [Outil de calcul du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Pour en savoir plus sur l’infrastructure et le cycle d’adoption des migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Ressources de migration vers le cloud](https://azure.microsoft.com/migration/resources)


- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour plus d’informations sur la façon d’effectuer un test A/B Data Access Layer, consultez [Vue d’ensemble de l’Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).