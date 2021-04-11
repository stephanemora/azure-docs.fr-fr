---
title: 'SAP ASE vers Azure SQL Database : Guide de migration'
description: Ce guide vous apprend à migrer vos bases de données SAP ASE vers Azure SQL Database à l’aide de l’outil Assistant Migration SQL Server pour SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564608"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guide de migration : SAP ASE vers Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ce guide vous apprend à migrer vos bases de données SAP ASE vers Azure SQL Database à l’aide de l’outil Assistant Migration SQL Server pour SAP Adapter Server Enterprise.

Pour obtenir d’autres guides de migration, consultez [Migration de base de données](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prérequis 

Pour migrer votre base de données SAP ASE vers Azure SQL Database, vous devez :

- vérifier que votre environnement source est pris en charge. 
- [Assistant Migration SQL Server pour SAP Adaptive Server Enterprise (anciennement SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>Prémigration

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre migration.

### <a name="assess"></a>Évaluer

Utilisez [l’outil Assistant Migration SQL Server (SSMA) pour SAP Adaptive Server Enterprise (anciennement SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) pour examiner les données et les objets de base de données, évaluer les bases de données en vue de leur migration, migrer les objets de base de données Sybase vers Azure SQL Database, puis migrer les données vers Azure SQL Database. Pour plus d’informations, consultez [Assistant Migration SQL Server pour Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Pour créer une évaluation, effectuez les étapes suivantes : 

1. Ouvrez **SSMA pour Sybase**. 
1. Sélectionnez **Fichier**, puis choisissez **Nouveau projet**. 
1. Fournissez un nom de projet et un emplacement d’enregistrement de votre projet, puis sélectionnez Azure SQL Database comme cible de migration dans la liste déroulante. Sélectionnez **OK**.
1. Entrez des valeurs pour les informations de connexion à SAP dans la boîte de dialogue **Se connecter à Sybase**. 
1. Cliquez avec le bouton droit sur la base de données SAP que vous souhaitez migrer, puis choisissez **Créer un rapport**. Cette action génère un rapport HTML.
1. Examinez le rapport HTML pour comprendre les statistiques de conversion et les erreurs ou avertissements. Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets DB2 et de l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans SSMAProjects est l’emplacement par défaut du rapport.

   Par exemple : `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Valider les mappages de type

Avant d’effectuer la conversion de schéma, validez les mappages de type de données par défaut ou modifiez-les en fonction des exigences. Pour ce faire, vous pouvez accéder au menu **Outils** et choisir **Paramètres du projet** ou vous pouvez modifier le mappage de type pour chaque table en sélectionnant la table dans **l’Explorateur de métadonnées SAP ASE**.


### <a name="convert-schema"></a>Convertir le schéma

Pour convertir le schéma, effectuez les étapes suivantes :

1. (Facultatif) Pour convertir des requêtes dynamiques ou ad hoc, cliquez avec le bouton droit sur le nœud et choisissez **Ajouter une instruction**. 
1. Sélectionnez **Se connecter à Azure SQL Database** dans la barre de navigation supérieure et fournissez les détails du compte Azure SQL Database. Vous pouvez vous connecter à une base de données existante ou fournir un nouveau nom afin de créer une base de données sur le serveur cible.
1. Cliquez avec le bouton droit sur le schéma SAP ASE dans **l’Explorateur de métadonnées SAP ASE** et choisissez **Convertir le schéma**. Vous pouvez également sélectionner **Convertir le schéma** dans la barre de navigation supérieure. 
1. Comparez et examinez la structure du schéma pour identifier les problèmes potentiels. 

   Après la conversion de schéma, vous pouvez enregistrer ce projet localement pour effectuer un exercice de correction de schéma hors connexion. Dans le menu **Fichier**, sélectionnez **Enregistrer le projet**. Cela vous permet d’évaluer les schémas source et cible hors connexion et d’apporter une correction avant de pouvoir publier le schéma sur Azure SQL Database.

Pour plus d’informations, consultez [Convertir le schéma](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql)


## <a name="migrate"></a>Migrate 

Une fois les conditions préalables réunies et les tâches associées à la phase **Prémigration** accomplies, vous êtes prêt à effectuer la migration du schéma et des données.

Pour publier le schéma et migrer les données, procédez comme suit : 

1. Cliquez avec le bouton droit sur la base de données dans **Explorateur de métadonnées Azure SQL Database** et choisissez **Synchroniser avec la base de données**.  Cette action publie le schéma SAP ASE sur l’instance Azure SQL Database.
1. Cliquez avec le bouton droit sur le schéma SAP ASE dans **l’Explorateur de métadonnées SAP ASE** et choisissez **Migrer les données**.  Vous pouvez également sélectionner **Migrer les données** dans la barre de navigation supérieure.  
1. Une fois la migration terminée, affichez le **Rapport de migration des données** : 
1. Validez la migration en examinant les données et le schéma sur l’instance Azure SQL Database à l’aide d’Azure SQL Database Management Studio (SSMS).


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

> [!NOTE]
> Pour plus d’informations sur ces problèmes et les étapes spécifiques pour les atténuer, consultez le [Guide de validation et d’optimisation post-migration](/sql/relational-databases/post-migration-validation-and-optimization-guide).


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
