---
title: Guide de migration d’Oracle vers SQL Server sur les machines virtuelles Azure
description: Ce guide explique comment migrer des schémas Oracle vers SQL Server sur les machines virtuelles Azure à l’aide de l’Assistant Migration SQL Server pour Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 11/06/2020
ms.openlocfilehash: d7c315694b5b25597abdacce74813b3578682f89
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111972055"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Guide de migration d’Oracle vers SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ce guide explique comment migrer des schémas Oracle vers SQL Server sur les machines virtuelles Azure à l’aide de l’Assistant Migration SQL Server pour Oracle. 

Pour obtenir d’autres guides de migration, consultez [Migration de base de données](/data-migration). 

## <a name="prerequisites"></a>Prérequis 

Pour migrer votre schéma Oracle vers SQL Server sur les machines virtuelles Azure, vous avez besoin des ressources suivantes :

- un environnement source pris en charge ;
- l’[Assistant Migration SQL Server (SSMA) pour Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) ;
- une [machine virtuelle SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md) cible ;
- les [autorisations nécessaires pour SSMA pour Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) et le [fournisseur](/sql/ssma/oracle/connect-to-oracle-oracletosql) ;
- une connectivité et des autorisations suffisantes pour accéder à source et à la cible. 


## <a name="pre-migration"></a>Prémigration

Quand vous préparez votre migration vers le cloud, vérifiez que votre environnement source est pris en charge et que les conditions préalables sont réunies. Cela contribuera à une migration efficace et réussie.

Cette partie du processus implique les opérations suivantes : 
- Réalisation d’un inventaire des bases de données à migrer.
- Évaluation de ces bases de données pour détecter les problèmes ou les obstacles potentiels à la migration. 
- Résolution des problèmes que vous dévoilez. 

### <a name="discover"></a>Découvrez

Utilisez [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) pour identifier les sources de données existantes et les détails sur les fonctionnalités que votre entreprise utilise. Cela vous permettra de mieux comprendre la migration et vous aidera à la planifier. Ce processus implique une analyse du réseau pour identifier les instances Oracle de votre organisation, ainsi que les versions et les fonctionnalités que vous utilisez.

Pour utiliser MAP Toolkit afin d’effectuer une analyse d’inventaire, procédez comme suit : 


1. Ouvrez [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Sélectionnez **Créer/sélectionner une base de données** :

   ![Capture d’écran montrant l’option Créer/sélectionner une base de données.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Sélectionnez **Créer une base de données d’inventaire**. Entrez un nom pour la base de données d’inventaire que vous créez, fournissez une brève description, puis sélectionnez **OK** : 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Capture d’écran montrant l’interface permettant de créer une base de données d’inventaire.":::

1. Sélectionnez **Collecter les données d’inventaire** pour ouvrir **l’Assistant Inventaire et évaluation** :

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Capture d’écran montrant le lien Collecter les données d’inventaire.":::


1. Dans **l’Assistant Inventaire et évaluation**, sélectionnez **Oracle**, puis **Suivant** :

   ![Capture d’écran montrant la page Scénarios d’inventaire de l’Assistant Inventaire et évaluation.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Sélectionnez l’option de recherche d’ordinateur correspondant le mieux à vos besoins et à votre environnement, puis sélectionnez **Suivant** : 

   ![Capture d’écran montrant la page Méthodes de découverte de l’Assistant Inventaire et évaluation.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Entrez les informations d’identification ou créez-en pour les systèmes que vous souhaitez explorer, puis sélectionnez **Suivant** :

    ![Capture d’écran montrant la page Informations d’identification de tous les ordinateurs de l’Assistant Inventaire et évaluation.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Définissez l’ordre des informations d’identification, puis sélectionnez **Suivant** : 

   ![Capture d’écran montrant la page Ordre des informations d’identification de l’Assistant Inventaire et évaluation.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Entrez les informations d’identification pour chaque ordinateur à découvrir. Vous pouvez utiliser des informations d’identification uniques pour chaque ordinateur/machine, ou vous pouvez utiliser la liste d’informations d’identification Tous les ordinateurs.  

   ![Capture d’écran montrant la page Spécifier les ordinateurs et les informations d’identification de l’Assistant Inventaire et évaluation.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Vérifiez vos sélections, puis sélectionnez **Terminer** :

   ![Capture d’écran montrant la page Résumé de l’Assistant Inventaire et évaluation.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. Une fois l’analyse terminée, affichez le résumé **Collecte de données**. L’analyse peut prendre quelques minutes en fonction du nombre de bases de données. Sélectionnez **Fermer** lorsque vous avez terminé : 

   ![Capture d’écran montrant le résumé Collecte de données.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Sélectionnez **Options** pour générer un rapport sur l’évaluation Oracle et les détails de la base de données. Sélectionnez les deux options, une à la fois, pour générer le rapport.


### <a name="assess"></a>Évaluer

Après avoir identifié les sources de données, utilisez [Assistant Migration SQL Server pour Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) pour évaluer les instances Oracle qui migrent vers la machine virtuelle SQL Server. L’assistant vous aidera à comprendre les écarts entre les bases de données source et celles de destination. Vous pouvez examiner les objets et les données de base de données, évaluer les bases de données pour la migration, migrer les objets de base de données vers SQL Server, puis migrer les données vers SQL Server.

Pour créer une évaluation, effectuez les étapes suivantes : 


1. Ouvrez [Assistant Migration SQL Server pour Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Dans le menu **Fichier**, sélectionnez **Nouveau projet**. 
1. Fournissez un nom de projet et un emplacement pour votre projet, puis sélectionnez une cible de migration SQL Server dans la liste. Sélectionnez **OK**. 

   ![Capture d’écran montrant la boîte de dialogue Nouveau projet.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Sélectionnez **Se connecter à Oracle**. Dans la boîte de dialogue **Se connecter à Oracle**, entrez des valeurs pour la connexion à Oracle :

   ![Capture d’écran montrant la boîte de dialogue Se connecter à Oracle.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Sélectionnez les schémas Oracle que vous voulez migrer : 

   ![Capture d’écran montrant la liste des schémas Oracle qui peuvent être migrés.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. Dans **Oracle Metadata Explorer** (Explorateur de métadonnées Oracle), cliquez avec le bouton droit sur le schéma Oracle à migrer, puis sélectionnez **Create report** (Créer un rapport). Cette opération génère un rapport HTML. Vous pouvez également sélectionner la base de données, puis **Create report** dans le menu supérieur.

   ![Capture d’écran montrant comment créer un rapport.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Examinez le rapport HTML pour y trouver les statistiques de conversion, les erreurs et les avertissements. Analysez-le pour comprendre les problèmes de conversion et les solutions.

    Vous pouvez également ouvrir le rapport dans Excel pour obtenir un inventaire des objets Oracle et l’effort nécessaire pour effectuer des conversions de schémas. Le dossier de rapport situé dans SSMAProjects est l’emplacement par défaut du rapport. 

   Par exemple : `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Capture d’écran montrant un rapport de conversion.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et modifiez-les en fonction des besoins, le cas échéant. Pour ce faire, procédez comme suit : 


1. Dans le menu **Tools** (Outils), sélectionnez **Project Settings** (Paramètres du projet). 
1. Sélectionnez l’onglet **Type Mappings** (Mappage de types). 

   ![Capture d’écran montrant l’onglet Type Mappings.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Vous pouvez modifier le mappage des types pour chaque table en sélectionnant celle-ci dans **Oracle Metadata Explorer** (Explorateur de métadonnées Oracle). 

### <a name="convert-the-schema"></a>Convertir le schéma

Pour convertir le schéma, effectuez les étapes suivantes : 

1. (Facultatif) Pour convertir des requêtes dynamiques ou ad hoc, cliquez avec le bouton droit sur le nœud, puis sélectionnez **Add statement** (Ajouter une instruction).

1. Sélectionnez **Connect to SQL Server** (Se connecter à SQL Server) dans le menu supérieur. 
     1. Entrez les informations de connexion pour votre instance de SQL Server sur les machines virtuelles Azure. 
     1. Sélectionnez votre base de données cible dans la liste ou indiquez un nouveau nom. Si vous fournissez un nouveau nom, une base de données sera créée sur le serveur cible. 
     1. Fournissez des informations d’authentification. 
     1. Sélectionnez **Connecter**. 


   ![Capture d’écran montrant comment se connecter à SQL Server.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Dans **Oracle Metadata Explorer** (Explorateur de métadonnées Oracle), cliquez avec le bouton droit sur le schéma Oracle, puis sélectionnez **Convert Schema** (Convertir le schéma). Vous pouvez également sélectionner **Convert Schema** (Convertir le schéma) dans le menu supérieur :

   ![Capture d’écran montrant comment convertir le schéma.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. Une fois la conversion de schéma terminée, examinez les objets convertis et comparez-les aux objets d’origine pour identifier les problèmes potentiels. Utilisez les recommandations pour résoudre les problèmes :

   ![Capture d’écran montrant la comparaison de deux schémas.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Comparez le texte Transact-SQL converti aux procédures stockées d’origine et passez en revue les recommandations : 

   ![Capture d’écran montrant Transact-SQL, des procédures stockées et un avertissement.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Vous pouvez enregistrer le projet localement pour un exercice de correction de schéma hors connexion. Pour ce faire, sélectionnez **Save Project** (Enregistrer le projet) dans le menu **File** (Fichier). L’enregistrement du projet en local vous permet d’évaluer les schémas source et cible hors connexion et d’apporter une correction avant de publier le schéma sur SQL Server.

1. Sélectionnez **Review results** (Examiner les résultats) dans le volet **Output** (Sortie), puis examinez les erreurs dans le volet **Error list** (Liste d’erreurs). 
1. Enregistrez le projet localement pour un exercice de correction de schéma hors connexion. Dans le menu **File** (Fichier), sélectionnez **Save Project** (Enregistrer le projet). Cela vous permet d’évaluer les schémas source et cible hors connexion et d’apporter une correction avant de publier le schéma sur SQL Server sur les machines virtuelles Azure.


## <a name="migrate"></a>Migrate

Une fois que les prérequis sont réunis et que vous avez effectué les tâches associées à l’étape de prémigration, vous êtes prêt à commencer la migration du schéma et des données. La migration comprend deux étapes : la publication du schéma et la migration des données. 


Pour publier votre schéma et migrer vos données, effectuez les étapes suivante : 

1. Publiez le schéma : cliquez avec le bouton droit sur la base de données dans **SQL Server Metadata Explorer** (Explorateur de métadonnées SQL Server), puis sélectionnez **Synchronize with Database** (Synchroniser avec la base de données). Cela permet de publier le schéma Oracle sur SQL Server sur les machines virtuelles Azure. 

   ![Capture d’écran montrant la commande de synchronisation avec la base de données.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Passez en revue le mappage entre votre projet source et votre cible :

   ![Capture d’écran montrant l’état de synchronisation.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Migrez les données : cliquez avec le bouton droit sur la base de données ou l’objet que vous souhaitez migrer dans **Oracle Metadata Explorer** (Explorateur de métadonnées Oracle), puis sélectionnez **Migrate Data** (Migrer les données). Vous pouvez également sélectionner **Migrate Data** (Migrer les données) dans le menu supérieur.

   Pour migrer des données pour une base de données entière, cochez la case à côté du nom de la base de données. Pour migrer des données à partir de tables individuelles, développez la base de données, développez **Tables**, puis cochez la case à côté de la table. Pour omettre certaines données des tables individuelles, décochez les cases appropriées.

   ![Capture d’écran montrant la commande de migration des données.](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Entrez les informations de connexion pour Oracle et SQL Server sur les machines virtuelles Azure dans la boîte de dialogue.
1. Une fois la migration terminée, affichez le **rapport de migration des données** :

    ![Capture d’écran montrant le rapport de migration des données.](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Connectez-vous à votre instance de SQL Server sur les machines virtuelles Azure à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Validez la migration en examinant les données et le schéma :


   ![Capture d’écran montrant une instance SQL dans SSMA.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

Au lieu d’utiliser SSMA, vous pouvez utiliser la fonctionnalité SQL Server Integration Services (SSIS) pour migrer les données. Pour plus d'informations, consultez les rubriques suivantes : 
- L’article [SQL Server Integration Services](//sql/integration-services/sql-server-integration-services).
- Le livre blanc [SSIS pour le déplacement des données hybrides et Azure](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>Post-migration 

Une fois la phase de migration terminée, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de la manière la plus fluide et efficace possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient auparavant la source doivent commencer à consommer la cible. Pour effectuer ces changements, il peut être nécessaire de modifier les applications.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) est une extension de Visual Studio Code. Elle vous permet d’analyser votre code source Java et de détecter les appels et les requêtes d’API d’accès aux données. Elle fournit une vue à volet unique des éléments à corriger pour prendre en charge le nouveau back end de la base de données. Pour en savoir plus, consultez [Migrer votre application Java à partir d’Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Effectuer des tests

Pour tester la migration de votre base de données, effectuez ces activités :

1. **Développer des tests de validation**. pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Créez des requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.

2. **Configurer un environnement de test**. l’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.

3. **Exécuter des tests de validation**. exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.

4. **Exécuter des tests de performances**. exécutez un test de performances sur la source et sur la cible, puis analysez et comparez les résultats.

### <a name="optimize"></a>Optimiser

La phase de post-migration est cruciale pour résoudre les problèmes d’exactitude des données et vérifier leur exhaustivité. Elle est également essentielle pour résoudre les problèmes de performance de la charge de travail.

> [!Note]
> Pour plus d’informations sur ces problèmes et les étapes spécifiques permettant de les atténuer, consultez le [guide de validation et d’optimisation post-migration](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-resources"></a>Ressources de migration 

Pour plus d’aide dans la réalisation de ce scénario de migration, consultez les ressources suivantes, qui ont été développées dans le cadre d’un projet de migration réel.

| **Titre/lien**                                                                                                                                          | **Description**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Outil et modèle d’évaluation de charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil fournit des suggestions pour les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données qui sont les mieux adaptés pour une charge de travail donnée. Il propose des fonctionnalités de génération de rapports et de calculs simples en un clic qui permettent d’accélérer l’évaluation des grands patrimoines en fournissant un processus automatisé et uniforme de prise de décision concernant la plateforme cible.                                                          |
| [Artefacts de script d’inventaire Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Cette ressource inclut une requête PL/SQL qui interroge les tables du système Oracle et fournit un nombre d’objets par type de schéma, type d’objet et état. Elle fournit également une estimation approximative des données brutes, ainsi que du dimensionnement des tables dans chaque schéma, avec les résultats enregistrés dans un fichier au format CSV.                                                                                                               |
| [Automatiser la collecte et la consolidation d’évaluation Oracle pour l’outil SSMA](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Cet ensemble de ressources utilise un fichier .csv comme entrée (sources.csv dans les dossiers de projet) pour produire les fichiers XML dont vous avez besoin pour exécuter une évaluation SSMA en mode console. Vous fournissez le fichier source.csv en faisant l’inventaire des instances Oracle existantes. Les fichiers de sortie sont AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml et VariableValueFile.xml.|
| [Problèmes avec SSMA et solutions possibles lors de la migration de bases de données Oracle](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle vous permet d’attribuer une condition non scalaire dans une clause WHERE. SQL Server ne prend pas en charge ce type de condition. Par conséquent, SSMA pour Oracle ne convertit pas les requêtes qui ont une condition non scalaire dans la clause WHERE. Au lieu de cela, il génère une erreur : O2SS0001. Ce livre blanc fournit des informations sur le problème et les manières de le résoudre.          |
| [Guide de migration d’Oracle vers SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ce document se concentre sur les tâches associées à la migration d’un schéma Oracle vers la dernière version de SQL Server. Si la migration nécessite des modifications de caractéristiques ou de fonctionnalités, vous devez examiner attentivement l’effet possible de chacune d’elles sur les applications qui utilisent la base de données.                                                     |
|[Oracle vers SQL Server - Utilitaire Comparaison de bases de données](https://www.microsoft.com/download/details.aspx?id=103016)|SSMA pour Oracle Tester est l’outil recommandé pour valider automatiquement la conversion des objets de base de données et la migration des données, et il s’agit d’un sur-ensemble de fonctionnalités de l’utilitaire Comparaison de bases de données.<br /><br />Si vous recherchez une autre option de validation des données, vous pouvez utiliser l’utilitaire Comparaison de bases de données pour comparer des données au niveau des lignes ou des colonnes dans l’ensemble des tables, des lignes et des colonnes sélectionnées.|


L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme de données Microsoft Azure.


## <a name="next-steps"></a>Étapes suivantes

- Pour vérifier la disponibilité des services applicables à SQL Server, accédez au [Centre de l’infrastructure mondiale Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez [Services et outils disponibles pour la migration des données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL, consultez :
   - [Options de déploiement](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server sur les machines virtuelles Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Outil de calcul du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/)


- Pour en savoir plus sur l’infrastructure et le cycle d’adoption des migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques en termes de coût et de taille des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour plus d’informations sur la gestion des licences, consultez :
   - [BYOL (apportez votre propre licence) avec Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Bénéficier d’un support étendu gratuit pour SQL Server 2008 et SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour plus d’informations sur l’exécution des tests A/B de la couche d’accès aux données, consultez [Vue d’ensemble d’Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).
