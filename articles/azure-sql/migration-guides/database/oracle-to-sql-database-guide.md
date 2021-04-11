---
title: 'Oracle vers SQL Database : Guide de migration'
description: Ce guide vous apprend à migrer votre schéma Oracle vers Azure SQL Database à l’aide de l’outil Assistant Migration SQL Server pour Oracle (SSMA pour Oracle).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: f00740de5a327858fd250a0cb561b07c32f3b726
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655486"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Guide de migration : Oracle vers Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ce guide vous apprend à migrer vos schémas Oracle vers Azure SQL Database à l’aide de l’outil Assistant Migration SQL Server pour Oracle.

Pour obtenir d’autres guides de migration, consultez [Migration de base de données](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prérequis

Pour migrer votre schéma Oracle vers SQL Database, vous avez besoin des éléments suivants : 

- un environnement source pris en charge ; 
- l’[Assistant Migration SQL Server (SSMA) pour Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) ; 
- une instance cible d’[Azure SQL Database](../../database/single-database-create-quickstart.md) ; 
- les [autorisations nécessaires pour SSMA pour Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) et un [fournisseur](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Prémigration

Une fois que vous avez rempli les prérequis, vous êtes prêt à découvrir la topologie de votre environnement et à évaluer la faisabilité de votre migration. Cette partie du processus inclut de dresser l’inventaire des bases de données que vous devez migrer, d’évaluer celles-ci en lien avec des problèmes de migration ou des blocages potentiels, et de résoudre les problèmes que vous pourriez avoir découverts.



### <a name="assess"></a>Évaluer 


Utilisez l’outil Assistant Migration SQL Server (SSMA) pour Oracle afin d’examiner les données et les objets de base de données, d’évaluer les bases de données en vue de leur migration, de migrer des objets de base de données vers Azure SQL Database et, enfin, de migrer des données vers la base de données. 


Pour créer une évaluation, effectuez les étapes suivantes : 


1. Ouvrez [Assistant Migration SQL Server pour Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Sélectionnez **Fichier**, puis choisissez **Nouveau projet**. 
1. Fournissez un nom de projet et un emplacement d’enregistrement de votre projet, puis sélectionnez Azure SQL Database comme cible de migration dans la liste déroulante. Sélectionnez **OK**.

   ![Nouveau projet](./media/oracle-to-sql-database-guide/new-project.png)


1. Sélectionnez **Se connecter à Oracle**. Dans la boîte de dialogue **Connect to Oracle** (Se connecter à Oracle), entrez des valeurs pour les détails de connexion à Oracle.

   ![Connexion à Oracle](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

   Sélectionnez le(s) schéma(s) Oracle que vous voulez migrer : 

   ![Sélectionner le schéma Oracle](./media/oracle-to-sql-database-guide/select-schema.png)

1. Dans l’**Oracle Metadata Explorer** (Explorateur de métadonnées Oracle), cliquez avec le bouton droit sur le schéma Oracle à migrer, puis choisissez **Create report** (Créer un rapport). Cette opération génère un rapport HTML. Vous pouvez également choisir **Create report** (Créer un rapport) dans la barre de navigation après avoir sélectionné la base de données.

   ![Créer un rapport](./media/oracle-to-sql-database-guide/create-report.png)

1. Examinez le rapport HTML pour comprendre les statistiques de conversion et les erreurs ou avertissements. Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets Oracle et de l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans SSMAProjects est l’emplacement par défaut du rapport.

   Par exemple : `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Rapport d’évaluation](./media/oracle-to-sql-database-guide/assessment-report.png) 



### <a name="validate-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction des exigences, si nécessaire. Pour ce faire, procédez comme suit :

1. Sélectionnez **Outils** dans le menu. 
1. Sélectionnez **Paramètres du projet**. 
1. Sélectionnez l’onglet **Mappage de types**. 

   ![Mappages de types](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Vous pouvez modifier le mappage de types pour chaque table en sélectionnant celle-ci dans l’**Oracle Metadata Explorer** (Explorateur de métadonnées Oracle).

### <a name="convert-schema"></a>Convertir le schéma

Pour convertir le schéma, effectuez les étapes suivantes : 

1. (Facultatif) Ajoutez des requêtes dynamiques ou ad hoc à des instructions. Cliquez avec le bouton droit sur le nœud, puis choisissez **Ajouter des instructions**.
1. Sélectionnez **Se connecter à Azure SQL Database**. 
    1. Entrez les détails de connexion pour connecter votre base de données dans Azure SQL Database.
    1. Choisissez votre compte SQL Database cible dans la liste déroulante.
    1. Sélectionnez **Connecter**.

    ![Connexion à la base de données SQL](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)


1. Dans **l’Explorateur de métadonnées Oracle**, cliquez avec le bouton droit sur le schéma Oracle, puis choisissez **Convertir le schéma**. Vous pouvez également choisir **Convertir le schéma** à partir de la barre de navigation supérieure après avoir sélectionné votre schéma.

   ![Convertir le schéma](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Une fois la conversion terminée, comparez et examinez les objets convertis aux objets d’origine afin d’identifier les problèmes potentiels et de les traiter en fonction des recommandations.

   ![Examiner le schéma des recommandations](./media/oracle-to-sql-database-guide/table-mapping.png)

   Comparez le texte Transact-SQL converti aux procédures stockées d’origine et examinez les recommandations. 

   ![Passer en revue les recommandations](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. Enregistrez le projet localement pour un exercice de correction de schéma hors connexion. Dans le menu **Fichier**, sélectionnez **Enregistrer le projet**.

## <a name="migrate"></a>Migrer

Une fois que vous avez terminé l’évaluation de vos bases de données et que vous traité toutes les anomalies, l’étape suivante consiste à exécuter le processus de migration. La migration comprend deux étapes : la publication du schéma et la migration des données. 

Pour publier votre schéma et migrer vos données, effectuez les étapes suivantes :

1. Publiez le schéma : cliquez avec le bouton droit sur la base de données dans le nœud **Bases de données** de l’**Explorateur de métadonnées Azure SQL Database**, puis choisissez **Synchroniser avec la base de données**.

   ![Synchroniser avec la base de données](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

   Passez en revue le mappage entre votre projet source et votre cible :

   ![Examen de la synchronisation avec la base de données](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)


1. Migrer les données : Cliquez avec le bouton droit sur le schéma dans l’**Oracle Metadata Explorer** (Explorateur de métadonnées Oracle), puis choisissez **Migrate Data** (Migrer les données). Vous pouvez également choisir **Migrer les données** dans la barre de navigation supérieure après avoir sélectionné le schéma. 

   ![Migration des données](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Fournissez les informations de connexion pour Oracle et Azure SQL Database.
1. Affichez le **Rapport de migration des données**.

   ![Rapport de migration des données](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Connectez-vous à votre compte Azure SQL Database à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) et validez la migration en examinant les données et le schéma.

   ![Valider dans SSMA](./media/oracle-to-sql-database-guide/validate-data.png)

Vous pouvez également utiliser SQL Server Integration Services (SSIS) pour effectuer la migration. Pour plus d'informations, consultez les rubriques suivantes : 

- [Assistant Migration SQL Server : Comment évaluer et migrer des données de plateformes de données non Microsoft vers SQL Server](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)
- [Prise en main de SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services : SSIS pour le déplacement des données Azure et hybrides](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)


## <a name="post-migration"></a>Post-migration 

Une fois la phase de **migration** accomplie, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de manière aussi fluide et efficace que possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, l’accomplissement de cette tâche nécessitera d’apporter des changements aux applications.

Le kit de ressources [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) est une extension pour Visual Studio Code qui vous permet d’analyser votre code source Java, ainsi que de détecter les requêtes et appels d’API d’accès aux données, en affichant dans un volet unique ce qu’il convient de faire pour prendre en charge le nouveau serveur principal de base de données. Pour en savoir plus, consultez le blog [Migrer notre application Java à partir d’Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 



### <a name="perform-tests"></a>Effectuer des tests

L’approche de test d’une migration de base de données comprend les activités suivantes :

1.  **Développer des tests de validation**. pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Vous devez créer les requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.

2.  **Configurer un environnement de test**. l’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.

3.  **Exécuter des tests de validation**. exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.

4.  **Exécuter des tests de performances**. exécutez un test de performances sur la source et sur la cible, puis analysez et comparez les résultats.


### <a name="optimize"></a>Optimiser

La phase postmigration est cruciale pour résoudre les problèmes de justesse et d’exhaustivité des données ainsi que pour gérer les problèmes de performances liés à la charge de travail.

> [!NOTE]
> Pour plus d’informations sur ces problèmes et les étapes spécifiques pour les atténuer, consultez le [Guide de validation et d’optimisation post-migration](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire concernant ce scénario de migration, consultez les ressources suivantes qui ont été développées dans le cadre d’un engagement de projet de migration réel.

| **Titre/lien**                                                                                                                                          | **Description**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Outil et modèle d’évaluation de charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil suggère les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une génération de rapports et des calculs simples en un clic, qui permettent d’accélérer considérablement les évaluations d’un vaste domaine en fournissant un processus de décision automatisé et uniforme concernant le choix de la plateforme cible.                                                          |
| [Artefacts de script d’inventaire Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Cette ressource inclut une requête PL/SQL qui interroge les tables système Oracle et fournit un nombre d’objets par type de schéma, type d’objet et état. Elle fournit également une estimation approximative des « données brutes », ainsi que du dimensionnement des tables dans chaque schéma, avec les résultats enregistrés dans un fichier de format CSV.                                                                                                               |
| [Automatiser la collecte et la consolidation d’évaluation Oracle pour l’outil SSMA](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Cet ensemble de ressources utilise un fichier .csv en entrée (sources.csv dans les dossiers de projet) pour produire les fichiers xml nécessaires à l’exécution d’une évaluation SSMA en mode console. Le fichier source.csv est fourni par le client sur la base d’un inventaire des instances Oracle existantes. Les fichiers de sortie sont AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml et VariableValueFile.xml.|
| [SSMA pour les erreurs courantes dans Oracle et comment les corriger](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle vous permet d’attribuer une condition non scalaire dans la clause WHERE. Toutefois, SQL Server ne prend pas en charge ce type de condition. Par conséquent, l’Assistant Migration SQL Server (SSMA) pour Oracle ne convertit pas les requêtes incluant une condition non scalaire dans la clause WHERE, et génère une erreur O2SS0001 à la place. Ce livre blanc fournit des informations supplémentaires sur le problème et les manières de le résoudre.          |
| [Guide de migration d’Oracle vers SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ce document se concentre sur les tâches associées à la migration d’un schéma Oracle vers la dernière version de SQL Serverbase. Si la migration nécessite des modifications de fonctions ou de fonctionnalités, il convient de prendre soigneusement en considération l’impact possible de chacune d’elles sur les applications qui utilisent la base de données.                                                     |

Ces ressources ont été développées dans le cadre du programme Data SQL Ninja, qui est sponsorisé par l’équipe d’ingénierie Groupe de données Azure. La charte fondamentale du programme Data SQL Ninja a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux opportunités de migration de plateforme de données vers la plateforme de données Azure de Microsoft. Si vous pensez que votre organisation aimerait participer au programme Data SQL Ninja, contactez votre équipe en charge des compte et demandez-lui de soumettre une candidature.

## <a name="next-steps"></a>Étapes suivantes

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez l’article [Services et outils disponibles pour la migration de données](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- Pour en savoir plus sur Azure SQL Database, consultez : 
  - [Vue d’ensemble d’Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [Outil de calcul du coût total de possession (TCO) Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Pour plus d’informations sur l’infrastructure et le cycle d’adoption pour les migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour le contenu vidéo, consultez : 
    - [Vue d’ensemble du parcours de migration et des outils/services recommandés pour effectuer l’évaluation et la migration](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)


