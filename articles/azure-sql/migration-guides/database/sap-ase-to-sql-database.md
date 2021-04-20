---
title: 'SAP ASE vers Azure SQL Database : Guide de migration'
description: Dans ce guide, vous allez apprendre à migrer vos bases de données SAP ASE vers une base de données Azure SQL à l’aide de l’Assistant Migration SQL Server pour SAP Adapter Server Entreprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: c60d6ba1f4d3628f57b8149779318c3e049a9e24
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284236"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guide de migration : SAP ASE vers Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Dans ce guide, vous allez [apprendre à migrer](https://azure.microsoft.com/migration/migration-journey) vos bases de données SAP Adapter Server Enterprise (ASE) vers une base de données Azure SQL à l’aide de l’Assistant [Migration SQL Server](https://azure.microsoft.com/en-us/migration/sql-server/) pour SAP Adapter Server Entreprise.

Pour d’autres guides sur la migration, consultez [Guide sur la migration de bases de données Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prérequis 

Avant de commencer à migrer votre base de données SAP SE sur votre base de données SQL, suivez les étapes suivantes :

- Vérifiez que votre environnement source est pris en charge. 
- Téléchargez et installez l’[Assistant Migration SQL Server pour SAP Adaptive Server Enterprise (anciennement SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256).
- Veillez à avoir de la connectivité et les autorisations suffisantes pour accéder à la source et à la cible.

## <a name="pre-migration"></a>Prémigration

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre [migration vers le cloud Azure](https://azure.microsoft.com/migration).

### <a name="assess"></a>Évaluer

Si vous utilisez [l’outil Assistant Migration SQL Server (SSMA) pour SAP Adaptive Server Enterprise (anciennement SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256), vous pouvez examiner les données et les objets de base de données, évaluer les bases de données en vue de leur migration, migrer les objets de base de données Sybase vers votre base de données SQL, puis migrer les données vers la base de données SQL. Pour plus d’informations, consultez [Assistant Migration SQL Server pour Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Pour créer une évaluation, procédez comme suit : 

1. Ouvrez SSMA pour Sybase. 
1. Sélectionnez **Fichier**, puis **Nouveau projet**. 
1. Dans le volet **Nouveau projet**, entrez un nom et un emplacement pour votre projet, puis, dans la liste déroulante **Migrer vers**, sélectionnez **Azure SQL Database**. 
1. Sélectionnez **OK**.
1. Dans le volet **Connexion à Sybase**, entrez les détails de la connexion SAP. 
1. Cliquez avec le bouton droit sur la base de données SAP que vous voulez migrer, puis sélectionnez **Créer un rapport**. Cette action génère un rapport HTML. Vous pouvez également sélectionner l’onglet **Créer un rapport** en haut à droite.
1. Examinez le rapport HTML pour comprendre les statistiques de conversion et les erreurs ou avertissements. Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets SAP ASE et l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans SSMAProjects est l’emplacement par défaut du rapport. Exemple :

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Valider les mappages de type

Avant d’effectuer la conversion de schéma, validez les mappages de type de données par défaut ou modifiez-les en fonction des exigences. Pour ce faire, sélectionnez **Outils** > **Paramètres du projet**, ou modifiez le mappage de type pour chaque table en sélectionnant la table dans **l’Explorateur de métadonnées SAP ASE**.

### <a name="convert-the-schema"></a>Convertir le schéma

Pour convertir le schéma, procédez comme suit :

1. (Facultatif) Pour convertir des requêtes dynamiques ou spécialisées, cliquez avec le bouton droit sur le nœud, puis sélectionnez **Ajouter une instruction**. 
1. Sélectionnez l’onglet **Se connecter à Azure SQL Database**, puis entrez les détails de votre base de données SQL. Vous pouvez vous connecter à une base de données existante ou fournir un nouveau nom afin de créer une base de données sur le serveur cible.
1. Dans le volet de l’**Explorateur de métadonnées Sybase**, cliquez avec le bouton droit sur le schéma SAP ASE que vous utilisez, puis sélectionnez **Convertir le schéma**. 
1. Une fois le schéma converti, comparez et vérifiez la structure convertie dans la structure d’origine pour identifier les problèmes potentiels. 

   Après la conversion de schéma, vous pouvez enregistrer ce projet localement pour effectuer un exercice de correction de schéma hors connexion. Pour ce faire, sélectionnez **Fichier** > **Enregistrer le projet**. Cela vous permet d’évaluer les schémas source et cible hors connexion et d’apporter une correction avant de publier le schéma sur votre base de données SQL.

1. Dans le volet **Sortie**, sélectionnez **Vérifier les résultats** et examinez les erreurs dans le volet **Liste d’erreurs**. 
1. Enregistrez le projet localement pour un exercice de correction de schéma hors connexion. Pour ce faire, sélectionnez **Fichier** > **Enregistrer le projet**. Cela vous permet d’évaluer les schémas source et cible hors connexion et d’apporter une correction avant de publier le schéma sur votre base de données SQL.

## <a name="migrate-the-databases"></a>Migrer les bases de données 

Une fois que les prérequis sont réunis et que vous avez effectué les tâches associées à la phase de *prémigration*, vous êtes prêt à exécuter la migration du schéma et des données.

Pour publier le schéma et migrer les données, procédez comme suit : 

1. Publiez le schéma. Dans le volet **Explorateur de métadonnées Azure SQL Database**, cliquez avec le bouton droit sur la base de données, puis sélectionnez **Synchroniser avec la base de données**. Cette action publie le schéma SAP ASE sur votre base de données SQL.

1. Migrez les données. Dans le volet **Explorateur de métadonnées SAP ASE**, cliquez avec le bouton droit sur la base de données ou l’objet SAP ASE que vous souhaitez migrer, puis sélectionnez **Migrer les données**. Vous pouvez également sélectionner l’onglet **Migrer les données** en haut à droite. 

   Pour migrer des données pour une base de données entière, cochez la case à côté du nom de la base de données. Pour migrer des données à partir de tables individuelles, développez la base de données, développez **Tables**, puis cochez la case à côté de la table. Pour omettre certaines données des tables individuelles, décochez la case. 
1. Une fois la migration terminée, affichez le **Rapport de migration des données**. 
1. Validez la migration en vérifiant les données et le schéma. Pour ce faire, connectez-vous à votre base de données SQL à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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
- Pour savoir comment exécuter un test A/B sur la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).
