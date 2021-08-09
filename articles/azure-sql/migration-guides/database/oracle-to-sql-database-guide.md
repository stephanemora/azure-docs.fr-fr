---
title: 'Oracle vers Azure SQL Database : Guide de migration'
description: Ce guide explique comment migrer un schéma Oracle vers Azure SQL Database à l’aide de l’Assistant Migration SQL Server pour Oracle.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: cawrites
ms.author: chadam
ms.date: 08/25/2020
ms.openlocfilehash: 85f65fa17e1fac05bd9c9040e77b2fbde104b176
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961956"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Guide de migration : Oracle vers Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ce guide explique [comment migrer](https://azure.microsoft.com/migration/migration-journey) des schémas Oracle vers Azure SQL Database à l’aide de l’Assistant [Migration SQL Server](https://azure.microsoft.com/en-us/migration/sql-server/) pour Oracle (SSMA pour Oracle).

Pour obtenir d’autres guides de migration, consultez les [Guides de migration de base de données Azure](/data-migration).

## <a name="prerequisites"></a>Prérequis

Avant de commencer la migration de votre schéma Oracle vers SQL Database :

- Vérifiez que votre environnement source est pris en charge.
- Téléchargez [SSMA pour Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
- Identifiez un instance [SQL Database](../../database/single-database-create-quickstart.md) cible.
- Obtenez les [autorisations nécessaires pour SSMA pour Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) et un [fournisseur](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Prémigration

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre [migration vers le cloud Azure](https://azure.microsoft.com/migration). Cette partie du processus inclut de dresser l’inventaire des bases de données que vous devez migrer, d’évaluer celles-ci en lien avec des problèmes de migration ou des blocages potentiels, et de résoudre les problèmes que vous pourriez avoir découverts.

### <a name="assess"></a>Évaluer

L’Assistant SSMA pour Oracle vous permet d’examiner les données et objets de base de données, d’évaluer les bases de données en vue de leur migration, de migrer des objets de base de données vers SQL Database et, enfin, de migrer des données vers la base de données.

Pour créer une évaluation :

1. Ouvrez [SSMA pour Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
1. Sélectionnez **Fichier**, puis **Nouveau projet**.
1. Entrez un nom de projet et un emplacement où enregistrer votre projet. Sélectionnez ensuite **Azure SQL Database** en tant que cible de migration dans la liste déroulante, puis sélectionnez **OK**.

   ![Capture d’écran montrant la boîte de dialogue Connexion à Oracle.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Sélectionnez **Se connecter à Oracle**. Dans la boîte de dialogue **Connexion à Oracle**, entrez des valeurs pour les détails de connexion à Oracle.

1. Sélectionnez les schémas Oracle à migrer.

   ![Capture d’écran montrant la sélection d’un schéma Oracle.](./media/oracle-to-sql-database-guide/select-schema.png)

1. Dans l’**explorateur de métadonnées Oracle**, cliquez avec le bouton droit sur le schéma Oracle à migrer, puis sélectionnez **Créer un rapport** pour générer un rapport HTML. Vous pouvez également sélectionner une base de données, puis sélectionner l’onglet **Créer un rapport**.

   ![Capture d’écran montrant l’onglet Créer un rapport.](./media/oracle-to-sql-database-guide/create-report.png)

1. Examinez le rapport HTML pour comprendre les statistiques de conversion et les erreurs ou avertissements. Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets Oracle et de l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans SSMAProjects est l’emplacement par défaut du rapport.

   Par exemple, consultez `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Capture d’écran montrant un rapport d’évaluation.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction des exigences, si nécessaire. Pour ce faire, procédez comme suit :

1. Dans SSMA pour Access, sélectionnez **Outils**, puis **Paramètres du projet**.
1. Sélectionnez l’onglet **Mappage de types**.

   ![Capture d’écran montrant l’onglet Mappage de types.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Vous pouvez modifier le mappage des types pour chaque table en sélectionnant celle-ci dans **Oracle Metadata Explorer** (Explorateur de métadonnées Oracle).

### <a name="convert-the-schema"></a>Convertir le schéma

Pour convertir le schéma :

1. (Facultatif) Ajoutez des requêtes dynamiques ou ad hoc à des instructions. Cliquez avec le bouton droit sur le nœud, puis sélectionnez **Ajouter des instructions**.
1. Sélectionnez l’onglet **Connexion à Azure SQL Database**.
    1. Dans **SQL Database**, entrez les détails de connexion pour connecter votre base de données.
    1. Choisissez votre instance SQL Database cible dans la liste déroulante ou entrez un nouveau nom. Dans ce cas, une base de données est créée sur le serveur cible.
    1. Entrez les détails de l’authentification, puis sélectionnez **Connecter**.

    ![Capture d’écran montrant la connexion à Azure SQL Database.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. Dans l’**explorateur de métadonnées Oracle**, cliquez avec le bouton droit sur le schéma Oracle, puis sélectionnez **Convertir le schéma**. Vous pouvez également sélectionner votre schéma, puis sélectionner l’onglet **Convertir le schéma**.

   ![Capture d’écran montrant Convertir le schéma.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Une fois la conversion terminée, comparez et examinez les objets convertis aux objets d’origine afin d’identifier les problèmes potentiels et de les traiter en fonction des recommandations.

   ![Capture d’écran montrant Examiner le schéma des recommandations.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Comparez le texte Transact-SQL converti aux procédures stockées d’origine, puis passez en revue les recommandations.

   ![Capture d’écran montrant Passer en revue les recommandations.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. Dans le volet de sortie, sélectionnez **Passer en revue les recommandations**, puis examinez les erreurs dans le volet **Liste d’erreurs**.
1. Enregistrez le projet localement pour un exercice de correction de schéma hors connexion. Dans le menu **Fichier**, sélectionnez **Enregistrer le projet**. Cela vous permet d’évaluer les schémas source et cible hors connexion et d’apporter une correction avant de publier le schéma sur votre base de données SQL Database.

## <a name="migrate"></a>Migrer

Une fois que vous avez évalué vos bases de données et résolu les incohérences, l’étape suivante consiste à exécuter le processus de migration. La migration comprend deux étapes : la publication du schéma et la migration des données.

Pour publier votre schéma et migrer vos données :

1. Publiez le schéma en cliquant avec le bouton droit sur la base de données dans le nœud **Bases de données** de l’**Explorateur de métadonnées Azure SQL Database**, puis en sélectionnant **Synchroniser avec la base de données**.

   ![Capture d’écran montrant Synchroniser avec la base de données.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Révisez le mappage entre votre projet source et votre cible.

   ![Capture d’écran montrant la révision de Synchroniser avec la base de données.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Migrez les données en cliquant avec le bouton droit sur la base de données ou l’objet que vous souhaitez migrer dans l’**explorateur de métadonnées Oracle**, puis en sélectionnant **Migrer les données**. Vous pouvez également sélectionner l’onglet **Migrer les données**. Pour migrer des données pour une base de données entière, activez la case à cocher en regard du nom de la base de données. Pour migrer des données à partir de tables individuelles, développez la base de données, développez **Tables**, puis cochez les cases en regard des tables. Pour omettre certaines données de tables individuelles, décochez les cases.

   ![Capture d’écran montrant l’onglet Migrer les données.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Entrez les informations de connexion pour Oracle et SQL Database.
1. Une fois la migration terminée, affichez le **Rapport de migration des données**.

   ![Capture d’écran montrant le Rapport de migration des données.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Connectez-vous à votre instance SQL Database à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms), puis validez la migration en révisant les données et le schéma.

   ![Capture d’écran montrant la validation dans SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

Vous pouvez également utiliser SQL Server Integration Services pour effectuer la migration. Pour en savoir plus, consultez :

- [Prise en main de SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services pour le déplacement de données Azure et hybrides](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Post-migration

Une fois la phase de *Migration* terminée, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de la manière la plus fluide et efficace possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, l’exécution de cette tâche requiert d’apporter des modifications aux applications.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) est une extension pour Visual Studio Code qui vous permet d’analyser votre code source Java et de détecter les appels et les requêtes d’API d’accès aux données. Elle fournit une vue à volet unique des éléments à corriger pour prendre en charge le nouveau serveur principal de la base de données. Pour en savoir plus, consultez le billet de blog sur la [migration de vos applications Java à partir d’Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727).

### <a name="perform-tests"></a>Effectuer des tests

L’approche de test pour la migration de base de données comprend les activités suivantes :

1. **Développer des tests de validation** : pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Vous devez créer les requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.
1. **Configurer un environnement de test** : L’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.
1. **Exécuter des tests de validation** : Exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.
1. **Exécuter des tests de performances** : Exécutez des tests de performances sur la source et sur la cible, puis analysez et comparez les résultats.

### <a name="optimize"></a>Optimiser

La phase postmigration est cruciale pour résoudre les problèmes de justesse et d’exhaustivité des données ainsi que pour gérer les problèmes de performances liés à la charge de travail.

> [!NOTE]
> Pour plus d’informations sur ces problèmes et les étapes spécifiques pour les atténuer, consultez le [Guide de validation et d’optimisation post-migration](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Ressources de migration

Pour plus d’informations sur l’exécution de ce scénario de migration, consultez les ressources suivantes. Elles ont été développées pour soutenir un engagement de projet de migration réel.

| **Titre/lien**                                                                                                                                          | **Description**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Outil et modèle d’évaluation de charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil fournit des suggestions pour les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données qui sont les mieux adaptés pour une charge de travail donnée. Il propose des fonctionnalités de génération de rapports et de calculs simples en un clic qui permettent d’accélérer les évaluations d’un vaste domaine en fournissant un processus de décision de plateforme cible automatisé et uniforme.                                                          |
| [Artefacts de script d’inventaire Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Cette ressource inclut une requête PL/SQL qui interroge les tables système Oracle et fournit un nombre d’objets par type de schéma, type d’objet et état. Elle fournit également une estimation approximative des « données brutes », ainsi que du dimensionnement des tables dans chaque schéma, avec les résultats enregistrés dans un fichier au format CSV.                                                                                                               |
| [Automatiser la collecte et la consolidation d’évaluation Oracle pour l’outil SSMA](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Cet ensemble de ressources utilise un fichier .csv en tant qu’entrée (sources.csv dans les dossiers de projet) pour produire les fichiers xml nécessaires à l’exécution d’une évaluation SSMA en mode console. Le fichier source.csv est fourni par le client sur la base d’un inventaire des instances Oracle existantes. Les fichiers de sortie sont AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml et VariableValueFile.xml.|
| [Erreurs courantes et corrections pour SSMA pour Oracle](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle vous permet d’attribuer une condition non scalaire dans la clause WHERE. Toutefois, SQL Server ne prend pas en charge ce type de condition. Par conséquent, SSMA pour Oracle ne convertit pas les requêtes avec une condition non scalaire dans la clause WHERE. Au lieu de cela, il génère l’erreur O2SS0001. Ce livre blanc fournit des informations supplémentaires sur le problème et les manières de le résoudre.          |
| [Guide de migration d’Oracle vers SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ce document se concentre sur les tâches associées à la migration d’un schéma Oracle vers la dernière version de Base de données SQL Server. Si la migration nécessite des modifications de fonctions ou de fonctionnalités, il convient de prendre soigneusement en considération l’impact possible de chacune d’elles sur les applications qui utilisent la base de données.                                                     |
|[Oracle vers SQL DB - Utilitaire Comparaison de bases de données](https://www.microsoft.com/download/details.aspx?id=103016)|SSMA pour Oracle Tester est l’outil recommandé pour valider automatiquement la conversion des objets de base de données et la migration des données, et il s’agit d’un sur-ensemble de fonctionnalités de l’utilitaire Comparaison de bases de données.<br /><br />Si vous recherchez une autre option de validation des données, vous pouvez utiliser l’utilitaire Comparaison de bases de données pour comparer des données au niveau des lignes ou des colonnes dans l’ensemble des tables, des lignes et des colonnes sélectionnées.|

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme Azure Data de Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez [Services et outils disponibles pour les scénarios de migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur SQL Database, consultez :
  - [Vue d’ensemble d’Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [Outil de calcul du coût total de possession (TCO) Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Pour en savoir plus sur l’infrastructure et le cycle d’adoption des migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Ressources de migration vers le cloud](https://azure.microsoft.com/migration/resources)

- Pour le contenu vidéo, consultez :
    - [Vue d’ensemble du parcours de migration, ainsi que des outils et services recommandés pour effectuer l’évaluation et la migration](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
