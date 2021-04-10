---
title: Guide de migration d’Oracle vers SQL Server sur machine virtuelle Azure
description: Ce guide explique comment migrer des schémas Oracle vers SQL Server sur des machines virtuelles Azure à l’aide de l’Assistant Migration SQL Server pour Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 1767f1f990326e513393b8ce47e1ed8485f73849
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656389"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Guide de migration d’Oracle vers SQL Server sur machine virtuelle Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ce guide explique comment migrer des schémas Oracle vers SQL Server sur une machine virtuelle Azure à l’aide de l’Assistant Migration SQL Server pour Oracle. 

Pour d’autres scénarios, consultez le [guide de migration de base de données](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Prérequis 

Pour migrer votre schéma Oracle vers SQL Server sur une machine virtuelle Azure, vous avez besoin des ressources suivantes :

- un environnement source pris en charge ;
- l’[Assistant Migration SQL Server (SSMA) pour Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) ;
- une [machine virtuelle SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md) cible ;
- les [autorisations nécessaires pour SSMA pour Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) et un [fournisseur](/sql/ssma/oracle/connect-to-oracle-oracletosql).

## <a name="pre-migration"></a>Prémigration

Lorsque vous préparez une migration vers le cloud, vérifiez que votre environnement source est pris en charge et que les conditions préalables sont réunies. Cela contribue à une migration efficace et réussie.

Cette partie du processus inclut de dresser l’inventaire des bases de données que vous devez migrer, d’évaluer celles-ci en lien avec des problèmes de migration ou des blocages potentiels, et de résoudre les problèmes que vous pourriez avoir découverts. 

### <a name="discover"></a>Découvrez

Utilisez le [Kit de ressources MAP](https://go.microsoft.com/fwlink/?LinkID=316883) pour identifier les sources de données existantes, ainsi que divers détails concernant les fonctionnalités que votre entreprise utilise afin d’acquérir une meilleure compréhension de la migration et de planifier celle-ci. Ce processus implique une analyse du réseau pour identifier toutes les instances Oracle de votre organisation, ainsi que la version et les fonctionnalités utilisées.

Pour utiliser le Kit de ressources MAP afin d’effectuer une analyse d’inventaire, procédez comme suit : 

1. Ouvrez le [Kit de ressources MAP](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Sélectionnez **Create/Select database** (Créer/sélectionner une base de données).

   ![Sélectionner une base de données](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Sélectionnez **Create an inventory database** (Créer une base de données d’inventaire), entrez un nom pour la base de données à créer, fournissez une brève description, puis choisissez **OK**. 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Créer une base de données d’inventaire":::

1. Sélectionnez **Collect inventory data** (Collecter les données d’inventaire) pour ouvrir l’**Inventory and Assessment Wizard** (Assistant inventaire et évaluation). 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Recueillir les données d’inventaire":::

1. Dans l’**Assistant**, choisissez **Oracle**, puis sélectionnez **Next** (Suivant). 

   ![Choisir Oracle](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Choisissez l’option de recherche d’ordinateur correspondant le mieux à vos besoins et à votre environnement, puis sélectionnez **Next** (Suivant) : 

   ![Choisissez l’option de recherche d’ordinateur correspondant le mieux à vos besoins](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Entrez les informations d’identification ou créez-en pour les systèmes que vous souhaitez explorer, puis sélectionnez **Next** (Suivant).

    ![Entrer les informations d’identification](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)

1. Définissez l’ordre des informations d’identification, puis sélectionnez **Next** (Suivant). 

   ![Définir l’ordre des informations d'identification](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  

1. Spécifiez les informations d’identification pour chaque ordinateur à découvrir. Vous pouvez utiliser des informations d’identification uniques pour chaque ordinateur/machine, ou choisir d’utiliser la liste **All Computer Credentials** (Toutes les informations d’identification d’ordinateur).  


   ![Spécifiez les informations d’identification pour chaque ordinateur à découvrir.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Vérifiez le récapitulatif de votre sélection, puis sélectionnez **Finish** (Terminer).

   ![Résumé de l’examen](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)

1. Une fois l’analyse terminée, affichez le rapport de synthèse **Data Collection** (Collection de données). L’analyse prend quelques minutes et dépend du nombre de bases de données. Quand vous avez terminé, sélectionnez **Close** (Fermer). 

   ![Rapport résumé de la collection](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Sélectionnez **Options** pour générer un rapport sur l’évaluation Oracle et les détails de la base de données. Sélectionnez les deux options (une à une) pour générer le rapport.


### <a name="assess"></a>Évaluer

Après avoir identifié les sources de données, utilisez l’[Assistant Migration SQL Server (SSMA) pour Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) afin d’évaluer les instances Oracle qui migrent vers la machine virtuelle SQL Server de manière à comprendre les écarts entre les deux. L’Assistant Migration vous permet d’examiner les objets et données de base de données, d’évaluer les bases de données pour la migration, de migrer les objets de base de données vers SQL Server, puis de migrer les données vers SQL Server.

Pour créer une évaluation, effectuez les étapes suivantes : 

1. Ouvrez l’[Assistant Migration SQL Server (SSMA) pour Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Sélectionnez **Fichier**, puis choisissez **Nouveau projet**. 
1. Fournissez un nom de projet et un emplacement d'enregistrement de votre projet, puis sélectionnez une cible de migration SQL Server dans la liste déroulante. Sélectionnez **OK**. 

   ![Nouveau projet](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)

1. Sélectionnez **Se connecter à Oracle**. Dans la boîte de dialogue **Connect to Oracle** (Se connecter à Oracle), entrez des valeurs pour les détails de connexion à Oracle.

   ![Connexion à Oracle](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Sélectionnez le(s) schéma(s) Oracle que vous voulez migrer : 

   ![Sélectionner le schéma Oracle](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)

1. Dans l’**Oracle Metadata Explorer** (Explorateur de métadonnées Oracle), cliquez avec le bouton droit sur le schéma Oracle à migrer, puis choisissez **Create report** (Créer un rapport). Cette opération génère un rapport HTML. Vous pouvez également choisir **Create report** (Créer un rapport) dans la barre de navigation après avoir sélectionné la base de données.

   ![Créer un rapport](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Dans l’**Oracle Metadata Explorer** (Explorateur de métadonnées Oracle), sélectionnez le schéma Oracle, puis **Create report** (Créer un rapport) afin de générer un rapport HTML contenant les statistiques de conversion ainsi que les erreurs/avertissements éventuels.
1. Dans le rapport HTML, examinez les statistiques de conversion, ainsi que les erreurs et avertissements. Analysez-les pour comprendre les problèmes de conversion et les solutions.

   Ce rapport est également accessible à partir du dossier de projets SSMA sélectionné dans le premier écran. Dans l’exemple ci-dessus, localisez le fichier report.xml dans : 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

    Ouvrez-le dans Excel pour obtenir un inventaire des objets Oracle et une estimation de l’effort requis pour opérer les conversions de schémas.

   ![Rapport de conversion](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)



### <a name="validate-data-types"></a>Valider les types de données

Validez les mappages de types de données par défaut et changez-les en fonction des exigences, si nécessaire. Pour ce faire, procédez comme suit : 

1. Sélectionnez **Outils** dans le menu. 
1. Sélectionnez **Paramètres du projet**. 
1. Sélectionnez l’onglet **Mappage de types**. 

   ![Mappages de types](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Vous pouvez modifier le mappage de types pour chaque table en sélectionnant celle-ci dans l’**Oracle Metadata Explorer** (Explorateur de données Oracle). 



### <a name="convert-schema"></a>Convertir le schéma

Pour convertir le schéma, effectuez les étapes suivantes : 

1. (Facultatif) Pour convertir des requêtes dynamiques ou ad hoc, cliquez avec le bouton droit sur le nœud, puis choisissez **Ajouter une instruction**.
1. Choisissez **Connexion au serveur SQL Server** dans la barre de navigation supérieure, puis fournissez les détails de connexion pour votre serveur SQL Server sur machine virtuelle Azure. Vous pouvez vous connecter à une base de données existante ou fournir un nouveau nom afin de créer une base de données sur le serveur cible.

   ![Se connecter à SQL](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Dans **l’Explorateur de métadonnées Oracle**, cliquez avec le bouton droit sur le schéma Oracle, puis choisissez **Convertir le schéma**.

   ![Convertir le schéma](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)

1. Une fois la conversion du schéma terminée, comparez et examinez la structure du schéma afin d’identifier les problèmes potentiels.

   ![Passer en revue les recommandations](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Comparez le texte Transact-SQL converti aux procédures stockées d’origine et passez en revue les recommandations : 

   ![Passez en revue le code des recommandations](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Vous pouvez enregistrer le projet localement pour un exercice de correction de schéma hors connexion. Pour ce faire, dans le menu **Fichier**, sélectionnez **Enregistrer le projet**. Cela vous permet d’évaluer les schémas source et cible hors connexion, et d’apporter une correction avant de publier le schéma sur SQL Server.


## <a name="migrate"></a>Migrate

Une fois les conditions préalables réunies et les tâches associées à la phase **Prémigration** accomplies, vous êtes prêt à effectuer la migration du schéma et des données. La migration comprend deux étapes : la publication du schéma et la migration des données. 


Pour publier le schéma et migrer les données, procédez comme suit : 

1. Cliquez avec le bouton droit sur la base de données dans **SQL Server Metadata Explorer** (Explorateur de métadonnées SQL Server), puis choisissez **Synchroniser avec la base de données**. Cette action a pour effet de publier le schéma Oracle dans SQL Server sur la machine virtuelle Azure. 

   ![Synchroniser avec la base de données](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Vérifiez l’état de la synchronisation : 

   ![Vérifier l’état de synchronisation](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)


1. Dans l’**Oracle Metadata Explorer** (Explorateur de métadonnées Oracle), cliquez avec le bouton droit sur le schéma Oracle, puis choisissez **Migrer les données**. Vous pouvez également sélectionner Migrer des données dans la barre de navigation supérieure.

   ![Migration des données](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Dans la boîte de dialogue qui s’affiche sur la machine virtuelle Azure, entrez les détails de connexion pour Oracle et SQL Server .
1. Une fois la migration terminée, affichez le rapport de migration des données :

    ![Rapport de migration des données](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Connectez-vous à votre serveur SQL Server sur la machine virtuelle Azure à l’aide de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) afin d’examiner les données et le schéma sur votre instance SQL Server. 

   ![Valider dans SSMA](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)




En plus de l’outil SSMA, vous pouvez utiliser la fonctionnalité SQL Server Integration Services (SSIS) pour migrer les données. Pour plus d'informations, consultez les rubriques suivantes : 
- l’article [Prise en main de SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services) ;
- le livre blanc [SQL Server Integration Services : SSIS pour Azure et déplacement de données hybrides](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>Post-migration 

Une fois la phase de **migration** accomplie, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de manière aussi fluide et efficace que possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, l’accomplissement de cette tâche nécessitera d’apporter des changements aux applications.

Le kit de ressources [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) est une extension pour Visual Studio Code qui vous permet d’analyser votre code source Java, ainsi que de détecter les requêtes et appels d’API d’accès aux données, en affichant dans un volet unique ce qu’il convient de faire pour prendre en charge le nouveau serveur principal de base de données. Pour en savoir plus, consultez le blog [Migrer notre application Java à partir d’Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Effectuer des tests

L’approche de test d’une migration de base de données comprend les activités suivantes :

1. **Développer des tests de validation**. pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Vous devez créer les requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.

2. **Configurer un environnement de test**. l’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.

3. **Exécuter des tests de validation**. exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.

4. **Exécuter des tests de performances**. exécutez un test de performances sur la source et sur la cible, puis analysez et comparez les résultats.

### <a name="optimize"></a>Optimiser

La phase postmigration est cruciale pour résoudre les problèmes de justesse et d’exhaustivité des données ainsi que pour gérer les problèmes de performances liés à la charge de travail.

> [!Note]
> Pour plus d’informations sur ces problèmes et les étapes spécifiques pour les atténuer, consultez le [Guide de validation et d’optimisation post-migration](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire concernant ce scénario de migration, consultez les ressources suivantes qui ont été développées dans le cadre d’un engagement de projet de migration réel.

| **Titre/lien**                                                                                                                                          | **Description**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Outil et modèle d’évaluation de charge de travail de données](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Cet outil suggère les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une génération de rapports et des calculs simples en un clic, qui permettent d’accélérer considérablement les évaluations d’un vaste domaine en fournissant un processus de décision automatisé et uniforme concernant le choix de la plateforme cible.                                                          |
| [Artefacts de script d’inventaire Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Cette ressource inclut une requête PL/SQL qui interroge les tables système Oracle et fournit un nombre d’objets par type de schéma, type d’objet et état. Elle fournit également une estimation approximative des « données brutes », ainsi que du dimensionnement des tables dans chaque schéma, avec les résultats enregistrés dans un fichier de format CSV.                                                                                                               |
| [Automatiser la collecte et la consolidation d’évaluation Oracle pour l’outil SSMA](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Cet ensemble de ressources utilise un fichier .csv en entrée (sources.csv dans les dossiers de projet) pour produire les fichiers xml nécessaires à l’exécution d’une évaluation SSMA en mode console. Le fichier source.csv est fourni par le client sur la base d’un inventaire des instances Oracle existantes. Les fichiers de sortie sont AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml et VariableValueFile.xml.|
| [SSMA pour les erreurs courantes dans Oracle et comment les corriger](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle vous permet d’attribuer une condition non scalaire dans la clause WHERE. Toutefois, SQL Server ne prend pas en charge ce type de condition. Par conséquent, l’Assistant Migration SQL Server (SSMA) pour Oracle ne convertit pas les requêtes incluant une condition non scalaire dans la clause WHERE, et génère une erreur O2SS0001 à la place. Ce livre blanc fournit des informations supplémentaires sur le problème et les manières de le résoudre.          |
| [Guide de migration d’Oracle vers SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ce document se concentre sur les tâches associées à la migration d’un schéma Oracle vers la dernière version de SQL Server. Si la migration nécessite des modifications de fonctions ou de fonctionnalités, il convient de prendre soigneusement en considération l’impact possible de chacune d’elles sur les applications qui utilisent la base de données.                                                     |

Ces ressources ont été développées dans le cadre du programme Data SQL Ninja, qui est sponsorisé par l’équipe d’ingénierie Groupe de données Azure. La charte fondamentale du programme Data SQL Ninja a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux opportunités de migration de plateforme de données vers la plateforme de données Azure de Microsoft. Si vous pensez que votre organisation aimerait participer au programme Data SQL Ninja, contactez votre équipe en charge des compte et demandez-lui de soumettre une candidature.

## <a name="next-steps"></a>Étapes suivantes

- Pour vérifier la disponibilité des services applicables à SQL Server, accédez au [Centre de l’infrastructure mondiale Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez l’article [Services et outils disponibles pour les scénarios de migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL, consultez :
   - [Options de déploiement](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server sur machines virtuelles Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Outil de calcul du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Pour plus d’informations sur l’infrastructure et le cycle d’adoption pour les migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour plus d’informations sur la gestion des licences, consultez :
   - [BYOL (apportez votre propre licence) avec Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Bénéficier d’un support étendu gratuit pour SQL Server 2008 et SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour savoir comment exécuter un test A/B sur la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).

