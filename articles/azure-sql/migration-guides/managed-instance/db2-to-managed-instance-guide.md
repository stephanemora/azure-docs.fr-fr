---
title: 'Db2 vers Azure SQL Managed Instance : Guide de migration'
description: Ce guide vous apprend à migrer vos bases de données IBM Db2 vers Azure SQL Managed Instance à l’aide de l’Assistant Migration SQL Server pour Db2.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 15d94e17bea6a49bb5bf6fdb6c48252365676042
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228161"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-managed-instance"></a>Guide de migration : IBM Db2 vers Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Ce guide vous apprend à migrer vos bases de données IBM Db2 vers Azure SQL Managed Instance à l’aide de l’Assistant Migration SQL Server pour Db2. 

Pour obtenir d’autres guides de migration, consultez les [Guides de migration de base de données Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prérequis 

Pour migrer votre base de données Db2 vers SQL Managed Instance, vous devez effectuer les opérations suivantes :

- Vérifier que votre [environnement source est pris en charge](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- Télécharger l’[Assistant Migration SQL Server (SSMA) pour Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- Disposer d’une instance cible d’[Azure SQL Managed Instance](../../managed-instance/instance-create-quickstart.md).
- Connectivité et autorisations suffisantes pour accéder à la fois à la source et à la cible. 

## <a name="pre-migration"></a>Prémigration

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre migration. 

### <a name="assess-and-convert"></a>Évaluer et convertir

Créez une évaluation à l’aide de l’Assistant Migration SQL Server. 

Pour créer une évaluation, effectuez les étapes suivantes :

1. Ouvrez [SSMA pour Db2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Sélectionnez **Fichier** > **Nouveau projet**. 
1. Indiquez un nom de projet et un emplacement où enregistrer votre projet. Sélectionnez ensuite Azure SQL Managed Instance en tant que cible de migration dans la liste déroulante, puis sélectionnez **OK**.

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Capture d’écran montrant les informations de projet à spécifier.":::


1. Dans **Se connecter à Db2**, entrez les informations relatives à la connexion Db2. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Capture d’écran montrant les options de connexion à votre instance de Db2.":::


1. Cliquez-droit sur le schéma DB2 à migrer, puis choisissez **Créer un rapport**. Cette opération génère un rapport HTML. Vous pouvez également choisir **Créer un rapport** à partir de la barre de navigation après avoir sélectionné le schéma.

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Capture d’écran montrant comment créer un rapport.":::

1. Examinez le rapport HTML pour comprendre les statistiques de conversion et les erreurs ou avertissements. Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets DB2 et de l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans *SSMAProjects* est l’emplacement par défaut du rapport.

   Par exemple : `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Capture d’écran du rapport que vous passez en revue pour identifier les erreurs ou les avertissements":::


### <a name="validate-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction des besoins, si nécessaire. Pour ce faire, procédez comme suit : 

1. Sélectionnez **Outils** dans le menu. 
1. Sélectionnez **Paramètres du projet**. 
1. Sélectionnez l’onglet **Mappage de types**.

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Capture d’écran montrant comment sélectionner le mappage de schéma et de type.":::

1. Vous pouvez changer le mappage de type pour chaque table en sélectionnant la table dans **l’Explorateur de métadonnées Db2**. 

### <a name="convert-schema"></a>Convertir le schéma 

Pour convertir le schéma, effectuez les étapes suivantes :

1. (Facultatif) Ajoutez des requêtes dynamiques ou ad hoc à des instructions. Cliquez avec le bouton droit sur le nœud, puis choisissez **Ajouter des instructions**. 
1. Sélectionnez **Se connecter à Azure SQL Managed Instance**. 
    1. Entrez les informations relatives à la connexion pour vous connecter à Azure SQL Managed Instance. 
    1. Choisissez votre base de données cible dans la liste déroulante, ou indiquez un nouveau nom. Dans ce cas, une base de données est créée sur le serveur cible. 
    1. Fournissez des informations d’authentification. 
    1. Sélectionnez **Connecter**.

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Capture d’écran montrant les informations nécessaires pour se connecter à SQL Server.":::


1. Cliquez avec le bouton droit sur le schéma, puis choisissez **Convertir le schéma**. Vous pouvez également choisir **Convertir le schéma** à partir de la barre de navigation supérieure après avoir sélectionné votre schéma.

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Capture d’écran montrant comment sélectionner le schéma et le convertir.":::

1. Une fois la conversion effectuée, comparez et examinez la structure du schéma afin d’identifier les problèmes potentiels. Résolvez les problèmes en fonction des recommandations.

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Capture d’écran montrant la comparaison et l’examen de la structure du schéma pour identifier les problèmes potentiels.":::

1. Dans le volet **Sortie**, sélectionnez **Examiner les résultats**. Dans le volet **Liste d’erreurs**, passez en revue les erreurs. 
1. Enregistrez le projet localement pour un exercice de correction de schéma hors connexion. Dans le menu **Fichier**, sélectionnez **Enregistrer le projet**. Cela vous permet d’évaluer les schémas source et cible hors connexion, et d’apporter les corrections nécessaires avant de publier le schéma sur SQL Managed Instance.

## <a name="migrate"></a>Migrer

Une fois que vous avez terminé l’évaluation de vos bases de données et que vous traité toutes les anomalies, l’étape suivante consiste à exécuter le processus de migration.

Pour publier votre schéma et migrer vos données, effectuez les étapes suivantes :

1. Publiez le schéma. Dans **l’Explorateur de métadonnées Azure SQL Managed Instance**, dans le nœud **Bases de données**, cliquez avec le bouton droit sur la base de données. Sélectionnez ensuite **Synchroniser avec la base de données**.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Capture d’écran montrant l’option de synchronisation avec la base de données.":::

1. Migrez les données. Cliquez avec le bouton droit sur la base de données ou l’objet que vous souhaitez migrer dans **l’Explorateur de métadonnées Db2**, puis choisissez **Migrer des données**. Vous pouvez également sélectionner **Migrer des données** dans la barre de navigation. Pour migrer des données pour une base de données entière, cochez la case à côté du nom de la base de données. Pour migrer des données à partir de tables individuelles, développez la base de données, développez **Tables**, puis cochez la case à côté de la table. Pour omettre certaines données des tables individuelles, décochez la case.

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Capture d’écran montrant comment sélectionner le schéma et choisir de migrer les données.":::

1. Fournissez les informations de connexion pour Db2 et SQL Managed Instance. 
1. Une fois la migration effectuée, visualisez le **Rapport de migration des données**.  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Capture d’écran montrant où passer en revue le rapport de migration des données.":::

1. Connectez-vous à votre instance d’Azure SQL Managed Instance via [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Validez la migration en passant en revue les données et le schéma :

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Capture d’écran montrant la comparaison du schéma dans SQL Server Management Studio.":::

## <a name="post-migration"></a>Postmigration 

Une fois la migration effectuée, vous devez exécuter une série de tâches postmigration pour vérifier que tout fonctionne de manière fluide et efficace.

### <a name="remediate-applications"></a>Corriger les applications 

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, l’accomplissement de cette tâche nécessitera d’apporter des changements aux applications.


### <a name="perform-tests"></a>Effectuer des tests

Les tests comprennent les activités suivantes :

1. **Développer des tests de validation** : pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Vous devez créer les requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.
1. **Configurer l’environnement de test** : l’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.
1. **Exécuter des tests de validation** : exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.
1. **Exécuter des tests de performances** : Exécutez des tests de performances sur la source et sur la cible, puis analysez et comparez les résultats.

## <a name="advanced-features"></a>Fonctionnalités avancées 

Veillez à tirer parti des fonctionnalités informatiques avancées offertes par Azure SQL Managed Instance, telles que la [haute disponibilité intégrée](../../database/high-availability-sla.md), la [détection des menaces](../../database/azure-defender-for-sql.md) et la [surveillance et le paramétrage de votre charge de travail](../../database/monitor-tune-overview.md). 

Certaines fonctionnalités SQL Server ne sont disponibles qu’une fois que vous avez fait passer le [niveau de compatibilité de la base de données](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) au dernier niveau. 

## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire, consultez les ressources suivantes, qui ont été développées dans le cadre d’un engagement de projet de migration réel :

|Asset  |Description  |
|---------|---------|
|[Outil et modèle d’évaluation d’une charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Cet outil fournit les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une génération de rapports et des calculs simples en un clic qui permettent d’accélérer les évaluations d’un vaste domaine en fournissant un processus de décision de plateforme cible automatisé et uniforme.|
|[Package de découverte et d’évaluation de ressources de données DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Après avoir exécuté le script SQL sur une base de données, vous pouvez exporter les résultats vers un fichier sur le système de fichiers. Plusieurs formats de fichier sont pris en charge, notamment *.csv, afin que vous puissiez capturer les résultats dans des outils externes, comme des feuilles de calcul. Cette méthode peut être utile si vous voulez partager facilement des résultats avec des équipes pour lesquelles le banc d’essai n’est pas installé.|
|[Artefacts et scripts d’inventaire IBM DB2 LUW](https://github.com/microsoft/DataMigrationTeam/blob/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Cette ressource comprend une requête SQL qui accède à des tables système IBM Db2 LUW version 11.1 et fournit un nombre d’objets par schéma et type d’objet, une estimation des « données brutes » dans chaque schéma et le dimensionnement des tables présentes dans chaque schéma, avec les résultats stockés au format CSV.|
|[Échelle pure DB2 LUW sur Azure - Guide de configuration](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Ce guide sert de point de départ pour un plan d’implémentation de DB2. Même si les besoins métier sont différents, le même modèle de base s’applique. Ce modèle d’architecture peut également être utilisé pour les applications OLAP sur Azure.|

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme Azure Data de Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir les services et outils Microsoft et tiers qui peuvent vous aider dans les différents scénarios de migration des données et de base de données, consultez [Services et outils disponibles pour les scénarios de migration des données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL Managed Instance, consultez :
   - [Vue d’ensemble de SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Outil de calcul du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Pour en savoir plus sur l’infrastructure et le cycle d’adoption des migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour plus d’informations sur l’exécution des tests A/B de la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).
