---
title: Mettre à niveau Azure Data Lake Storage de Gen1 à Gen2
description: Mettezà niveau Azure Data Lake Storage de Gen1 à Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 11/19/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 41074561b4805fef1889bd889b625e1a59d57d91
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327878"
---
# <a name="upgrade-azure-data-lake-storage-from-gen1-to-gen2"></a>Mettre à niveau Azure Data Lake Storage de Gen1 à Gen2

Si Azure Data Lake Storage Gen1 fait partie de vos solutions d’analytique de Big Data, ce guide vous permet de mettre à jour ces solutions pour utiliser Azure Data Lake Storage Gen2. Vous pouvez utiliser ce document pour évaluer les dépendances entre votre solution et Data Lake Storage Gen1. Ce guide vous explique également comment planifier et effectuer la mise à niveau.

Nous vous aiderons à effectuer les tâches suivantes :

:heavy_check_mark: Évaluer votre préparation à la mise à niveau

:heavy_check_mark: Planifier une mise à niveau

:heavy_check_mark: Effectuer la mise à niveau

## <a name="assess-your-upgrade-readiness"></a>Évaluer votre préparation à la mise à niveau

Notre objectif est que toutes les fonctionnalités présentes dans Data Lake Storage Gen1 soient disponibles dans Data Lake Storage Gen2. La présentation de ces fonctions, par exemple dans le SDK ou la CLI, peut varier entre Data Lake Storage Gen1 et Data Lake Storage Gen2. Les applications et services qui fonctionnent avec Data Lake Storage Gen1 doivent pouvoir fonctionner de la même manière avec Data Lake Storage Gen2. Enfin, certaines des fonctionnalités ne seront pas disponibles dans Data Lake Storage Gen2 immédiatement. Nous annoncerons leur disponibilité dans ce document.

Les sections suivantes vous permettront de déterminer la meilleure façon de vous mettre à niveau vers Data Lake Storage Gen2 et à quel moment cela serait le plus judicieux.

### <a name="data-lake-storage-gen1-solution-components"></a>Composants de la solution Data Lake Storage Gen1

Si vous utilisez Data Lake Storage Gen1 avec vos solutions ou pipelines d’analyse, vous devez employer de nombreuses autres technologies pour profiter de toutes les fonctionnalités de bout en bout. Cet [article](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) décrit les divers composants de ce flux de données : ingestion, traitement et consommation des données.

En outre, il existe des composants transversaux pour provisionner, gérer et surveiller ces composants. Chacun de ces composants fonctionne avec Data Lake Storage Gen1 via une interface adaptée. Lorsque vous prévoyez de mettre à jour votre solution vers Data Lake Storage Gen2, vous devez connaître les interfaces utilisées. Vous devrez mettre à niveau les interfaces de gestion ainsi que les interfaces de données dans la mesure où chaque interface a des exigences distinctes.

![Composants de la solution Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

La **Figure 1** ci-dessus montre les fonctionnalités de la plupart des solutions d’analytique.

La **Figure 2** montre un exemple du mode d’implémentation de ces composants à l’aide de technologies spécifiques.

La fonctionnalité de stockage de la **Figure1** est fournie par Data Lake Storage Gen1 (**Figure 2**). Notez comment les différents composants du flux de données interagissent avec Data Lake Storage Gen1 via l’API REST ou le SDK Java. Notez également la façon dont les composants de fonctionnalités transversales interagissent avec Data Lake Storage Gen1. Le composant d’approvisionnement utilise des modèles de ressources Azure, tandis que le composant de surveillance s’appuyant sur les journaux Azure Monitor qui utilisent les données opérationnelles provenant de Data Lake Storage Gen1.

Pour mettre à niveau une solution pour qu’elle passe de Data Lake Storage Gen1 à Data Lake Storage Gen2, vous devrez copier les données et les métadonnées, rattacher les flux de données, puis tous les composants devront être en mesure de fonctionner avec Data Lake Storage Gen2.

Les sections ci-dessous fournissent des informations qui vous aideront à prendre de meilleures décisions :

:heavy_check_mark: Fonctionnalités de la plateforme

:heavy_check_mark: Interfaces de programmation

:heavy_check_mark: Écosystème Azure

:heavy_check_mark: Écosystème de partenaires

:heavy_check_mark: Informations opérationnelles

Dans chaque section, vous pourrez déterminer les « éléments indispensables » à votre mise à niveau. Une fois que vous vous êtes assuré que les fonctionnalités sont disponibles, ou qu’il existe des solutions de contournement raisonnables en place, passez à la section [Planification d’une mise à niveau](#planning-for-an-upgrade) de ce guide.

### <a name="platform-capabilities"></a>Fonctionnalités de la plateforme

Cette section décrit les fonctionnalités de la plateforme Data Lake Storage Gen1 actuellement disponibles dans Data Lake Storage Gen2.

| | Data Lake Storage Gen 1 | Data Lake Storage Gen2 - objectif | Data Lake Storage Gen2 - état de disponibilité  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Organisation des données| Prend en charge les données stockées en tant que fichiers et dossiers | Prend en charge les données stockées en tant qu’objets/objets blob, ainsi que les dossiers et fichiers - [Lien](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Prend en charge les données stockées en tant que fichiers et dossiers – *Disponible maintenant* <br><br> Prend en charge les données stockées en tant qu’objets/objets blobs - *Pas encore disponible* |
| Espace de noms| Hiérarchique | Hiérarchique |  *Déjà disponible*  |
| API  | API REST sur HTTPS | API REST sur HTTP/HTTPS| *Déjà disponible* |
| API côté serveur| [API REST compatible WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | API REST du service Blob Azure [API REST de Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | API REST Azure Data Lake Storage Gen2 – *Disponible maintenant* <br><br> API REST du service blob Azure : *Déjà disponible*       |
| Client de système de fichiers Hadoop | Oui ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Oui ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Déjà disponible*  |  
| Opérations de données - Autorisation  | Listes de contrôle d’accès (ACL) POSIX aux niveaux fichier et dossier basées sur les identités Azure Active Directory  | Listes de contrôle d’accès (ACL) POSIX aux niveaux fichier et dossier basées sur les identités Azure Active Directory [Clé de partage](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) pour le contrôle d’accès en fonction du rôle ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) avec autorisation au niveau du compte pour accéder aux conteneurs | *Déjà disponible* |
| Opérations de données - Journaux d’activité  | OUI | OUI | *Déjà disponible* (préversion). Consultez [problèmes connus](data-lake-storage-known-issues.md).<br><br> Intégration Azure Monitoring – *Pas encore disponible* |
| Chiffrement des données au repos | Transparent, côté serveur, avec clés gérées par le service et clés gérées par le client dans Azure KeyVault | Transparent, côté serveur, avec clés gérées par le service et clés gérées par le client dans Azure KeyVault | Clés gérées par le service – *Disponible maintenant*<br><br> Clés gérées par le client – *Disponible maintenant*  |
| Opérations de gestion (par exemple, créer un compte) | [Le contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) fournit par Azure pour la gestion des comptes ; | [Le contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) fournit par Azure pour la gestion des comptes ; | *Déjà disponible*|
| Kits de développement logiciel pour développeur | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| Kit de développement logiciel (SDK) Blob : *Déjà disponible*. Kit de développement logiciel (SDK) Azure Data Lake Storage Gen2 : *Pas encore disponible*  |
| |Optimisation des performances pour les charges de travail d’analyses parallèles. Débit et nombre d’E/S par seconde élevés. | Optimisation des performances pour les charges de travail d’analyses parallèles. Débit et nombre d’E/S par seconde élevés. | *Déjà disponible* |
| Prise en charge du réseau virtuel  | [Utilisation de l’intégration du Réseau virtuel](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Utilisation d’un point de terminaison de service pour Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Déjà disponible* |
| Limites de taille | Aucune limite de taille pour les comptes, les fichiers ou le nombre de fichiers | Aucune limite sur la taille des comptes ou le nombre de fichiers. Taille de fichier limitée à 5 To. | *Déjà disponible*|
| Géo-redondance| Localement redondant (LRS) | Localement redondant (LRS), redondant dans une zone (ZRS), géo-redondant (GRS), géo-redobdabt avec accès en lecture (RA-GRS) Pour plus d’informations, voir [ici](https://docs.microsoft.com/azure/storage/common/storage-redundancy)| *Déjà disponible* |
| Disponibilité régionale | Voir [ici](https://azure.microsoft.com/regions/) | Toutes les [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Déjà disponible*                                                                                                                           |
| Prix                                       | Consultez la page [Tarification](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Consultez la page [Tarification](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Contrat SLA de disponibilité                            | [Voir contrat SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Voir contrat SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Déjà disponible*                                                                                                                           |
| Gestion des données                             | Expiration des fichiers                                                                                                                                        | Stratégies de cycle de vie                                                                                                                                                                                                                                                                                                                                                                                                          | Stratégies de cycle de vie *Déjà disponible* (préversion). Consultez [problèmes connus](data-lake-storage-known-issues.md).                                                                                                                     |

### <a name="programming-interfaces"></a>Interfaces de programmation

Ce tableau décrit les ensembles d’API disponibles pour vos applications personnalisées. La prise en charge du SDK pour les opérations ACL et au niveau des répertoires n’est pas encore prise en charge.

|  Ensemble d’API                           |  Data Lake Storage Gen 1                                                                                                                                                                                                                                                                                                   | Disponibilité pour Data Lake Storage Gen2 - avec authentification par clé partagée | Disponibilité pour Data Lake Storage Gen2 - avec authentification OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kit de développement logiciel (SDK) .NET                  | [Lien](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet) | *Déjà disponible* | *Déjà disponible* |
| Kit de développement logiciel (SDK) Java                | [Lien](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Déjà disponible*                                                      | *Déjà disponible*                                     |
| Node.js                | [Lien](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Déjà disponible*                                                     | *Déjà disponible*                                                                                           |
| Kit de développement logiciel (SDK) Python                   | [Lien](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Déjà disponible*                                                      | *Déjà disponible*                                        |
| API REST                 | [Lien](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Déjà disponible*                                                      | *Déjà disponible*                                                                                                                                               |
| PowerShell | [Lien](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | *Déjà disponible* |
| Interface de ligne de commande                 | [Lien](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Déjà disponible*                                                      | *Déjà disponible*                                                               |
| Modèles Azure Resource Manager - gestion             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Déjà disponible*                                                      | *Déjà disponible*                                          |

### <a name="azure-ecosystem"></a>Écosystème Azure

Lorsque vous utilisez Data Lake Storage Gen1, vous pouvez utiliser une variété de produits et services Microsoft dans vos pipelines de bout en bout. Ces produits et services fonctionnent avec Data Lake Storage Gen1 directement ou indirectement. Ce tableau affiche une liste des services que nous avons modifiés pour qu’ils fonctionnent avec Data Lake Storage Gen1, et indique ceux qui sont actuellement compatibles avec Data Lake Storage Gen2.

| **Zone**             | **Disponibilité pour Data Lake Storage Gen1**                                                                                                                                    | **Disponibilité pour Data Lake Storage Gen2 - avec authentification par clé partagée**                                                                                                           | **Disponibilité pour Data Lake Storage Gen2 - avec authentification OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Infrastructure d’analyse  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Déjà disponible*                                                                                                                                                              | *Déjà disponible*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - *Déjà disponible* HDInsight 4.0 - *Pas encore disponible*      | ESP HDInsight 3.6 – *Disponible maintenant* <br><br>  ESP HDInsight 4.0 - *Pas encore disponible*                                                                 |
|                      | Databricks Runtime 3.1 et versions ultérieures                                                                                                                                               | [Databricks Runtime 5.1 et versions ultérieures](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2)  *– Déjà disponible* | [Databricks Runtime 5.1 et versions ultérieures](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *Déjà disponible*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Non pris en charge*                                                                                                                                                              | *Déjà disponible* [Lien](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Intégration des données     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *Déjà disponible* Version 1 – *Non pris en charge*                               | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *Disponible maintenant* <br><br> Version 1 – *Non pris en charge*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Non pris en charge*                                                                                                                                                              | *Non pris en charge*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Déjà disponible*                                                                                                                                                          | *Déjà disponible*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Pas encore disponible*                                                                                                                                                          | *Pas encore disponible*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Déjà disponible*                                                                                                                                                          | *Déjà disponible*                                                                                                                             |
| IoT                  | [Event Hubs – Capture](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Déjà disponible*                                                                                                                                                          | *Déjà disponible*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Déjà disponible*                                                                                                                                                          | *Déjà disponible*                                                                                                                             |
| Consommation          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Déjà disponible*                                                                                                                                                          | *Déjà disponible*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Pas encore disponible*                                                                                                                                                          | *Pas encore disponible*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Pas encore disponible*                                                                                                                                                          | *Pas encore disponible*                                                                                                                             |
| Productivité         | [Portail Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Non pris en charge*                                                                                                                                                              | Gestion du compte *– Disponible maintenant* <br><br>Opérations sur les données *–*  *Pas encore disponible*                                                                   |
|                      | [Data Lake Tools pour Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Pas encore disponible*                                                                                                                                                          | *Pas encore disponible*                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Déjà disponible*                                                                                                                                                              | *Déjà disponible*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Pas encore disponible*                                                                                                                                                          | *Pas encore disponible*                                                                                                                             |

### <a name="partner-ecosystem"></a>Écosystème de partenaires

Ce tableau affiche une liste des produits et services tiers qui ont été modifiés pour fonctionner avec Data Lake Storage Gen1. Il montre également ceux qui sont actuellement compatibles avec Data Lake Storage Gen2.

| Domaine            | Partenaire  | Produit/Service  | Disponibilité pour Data Lake Storage Gen1                                                                                                                                               | Disponibilité pour Data Lake Storage Gen2 - avec authentification par clé partagée                                                   | Disponibilité pour Data Lake Storage Gen2 - avec OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Infrastructure d’analyse | Cloudera     | CDH                  | [Lien](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Pas encore disponible*                                                                                                  | [Lien](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Lien](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Non pris en charge*                                                                                                                  | *Pas encore disponible*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Lien](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Pas encore disponible*                                                                                                  | *Pas encore disponible*                                                                                                  |
|                     | Qubole       |                      | [Lien](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Pas encore disponible*                                                                                                  | *Pas encore disponible*                                                                                                  |
| ETL                 | StreamSets   |                      | [Lien](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Pas encore disponible*                                                                                                  | *Pas encore disponible*                                                                                                  |
|                     | Informatica  |                      | [Lien](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Pas encore disponible*                                                                                                  | *Pas encore disponible*                                                                                                  |
|                     | Attunity     |                      | [Lien](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Pas encore disponible*                                                                                                  | *Pas encore disponible*                                                                                                  |
|                     | Alteryx      |                      | [Lien](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Pas encore disponible*                                                                                                  | *Pas encore disponible*                                                                                                  |
|                     | ImanisData   |                      | [Lien](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Pas encore disponible*                                                                                                  | *Pas encore disponible*                                                                                                  |
|                     | WANdisco     |                      | [Lien](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Lien](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Lien](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informations opérationnelles

Data Lake Storage Gen1 transmet des informations et les données spécifiques à d’autres services, ce qui vous aide à faire fonctionner vos pipelines. Ce tableau montre la disponibilité des fonctions correspondantes prises en charge dans Data Lake Storage Gen2.

| Type de données                                                                                       | Disponibilité pour Data Lake Storage Gen1                                                                 | Disponibilité pour Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Données de facturation - compteurs envoyés à l’équipe commerciale pour la facturation et mis à disposition des clients  | *Déjà disponible*                                                                                             | *Déjà disponible*                                                                                                                           |
| Journaux d’activité                                                                                          | [Lien](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Requêtes uniques de journaux d’activité pour une durée spécifique avec Intégration Azure Monitoring – *Pas encore disponible* des tickets de support - *Déjà disponible* |
| Journaux de diagnostic                                                                                        | [Lien](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | *Déjà disponible* (préversion). Consultez [problèmes connus](data-lake-storage-known-issues.md). <br>Intégration Azure Monitoring : *Pas encore disponible* |
| Mesures                                                                                                | *Non pris en charge*                                                                                               | *Déjà disponible -* [Lien](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planifier une mise à niveau

Cette section part du principe que vous avez consulté la section [Évaluer votre préparation à la mise à niveau](#assess-your-upgrade-readiness) de ce guide, et que toutes vos dépendances sont satisfaites. Si certaines fonctionnalités ne sont toujours pas disponibles dans Data Lake Storage Gen2, continuez uniquement si vous connaissez les solutions de contournement correspondantes. Les sections suivantes fournissent des conseils sur la façon dont vous pouvez préparer la mise à niveau de vos pipelines. La mise à niveau elle-même sera décrite dans la section [Mise à niveau](#performing-the-upgrade) de ce guide.

### <a name="upgrade-strategy"></a>Stratégie de mise à niveau

La partie la plus importante de la mise à niveau consiste à en définir la stratégie. Cette décision détermine les choix qui s’offrent à vous.

Ce tableau répertorie certaines stratégies bien connues déjà utilisées pour migrer des bases de données, des clusters Hadoop, etc. Nous allons utiliser des stratégies similaires dans nos conseils et les adapter à notre contexte.

| Stratégie                   | Avantages                                                                                  | Inconvénients                                                           | Quand l’utiliser ?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lift-and-shift                 | La plus simple.                                                                                 | Nécessite un temps d’arrêt pour la copie des données, le déplacement des tâches et celui des entrées et sorties                                             | Pour les solutions les plus simples, qui n’impliquent pas l’accès de plusieurs solutions au même compte Gen1, et qui peuvent donc être déplacées rapidement d’un bloc.                                                  |
| Copie ponctuelle et copie incrémentielle | Réduisez les temps d’arrêt en effectuant la copie en arrière-plan pendant que le système source est actif. | Nécessite un temps d’arrêt pour le déplacement des entrées et sorties.                   | La quantité de données à copier est importante et le temps d’arrêt associé avec le Lift-and-shift n’est pas acceptable. Des tests sur des données de production significatives peuvent être requis sur le système cible avant la transition. |
| Adoption parallèle              | Temps d’arrêt minimal. Donne le temps aux applications de migrer à leur convenance.           | Stratégie la plus élaborée, car une synchronisation à double sens est nécessaire entre les deux systèmes. | Pour des scénarios complexes où les applications basées sur Data Lake Storage Gen1 ne peuvent pas être basculées simultanément et doivent être déplacées de manière incrémentielle.                                                      |

Voici plus de détails sur les étapes de chacune de ces stratégies. Les étapes décrivent les actions à exécuter avec les composants impliqués dans la mise à niveau. Cela inclut l’ensemble du système source, l’ensemble du système cible, les sources d’entrée pour le système source, les destinations de sortie pour le système source et les tâches s’exécutant sur le système source.

Ces étapes n’ont pas vocation à être normatives. Elles sont destinées à poser un cadre quant à la conception de chaque stratégie. Nous vous fournirons des études de cas pour chaque stratégie implémentée.

#### <a name="lift-and-shift"></a>Lift-and-shift

1. Suspendez le système source, les sources d’entrée, les tâches, les destinations de sortie.
2. Copiez toutes les données du système source vers le système cible.
3. Pointez toutes les sources d’entrée vers le système cible. Pointez vers la destination de sortie à partir du système cible.
4. Déplacez, modifiez, exécutez toutes les tâches sur le système cible.
5. Arrêtez le système source.

#### <a name="copy-once-and-copy-incremental"></a>Copie ponctuelle et copie incrémentielle

1. Copiez les données du système source vers le système cible.
2. Copiez les données incrémentielles du système source vers le système cible à intervalles réguliers.
3. Pointez vers la destination de sortie à partir du système cible.
4. Déplacez, modifiez, exécutez toutes les tâches sur le système cible.
5. Pointez les sources d’entrée de manière incrémentielle vers le système cible à votre convenance.
6. Une fois que toutes les sources d’entrée pointent vers le système cible.
    1. Arrêtez la copie incrémentielle.
    2. Arrêtez le système source.

#### <a name="parallel-adoption"></a>Adoption parallèle

1. Configurez le système cible.
2. Configurez une réplication bidirectionnelle entre le système source et le système cible.
3. Pointez les sources d’entrée de manière incrémentielle vers le système cible.
4. Déplacez, modifiez, exécutez les tâches de façon incrémentielle sur le système cible.
5. Pointez vers les destinations de sortie de manière incrémentielle depuis le système cible.
6. Une fois que toutes les sources d’entrée d’origine, tâches et destinations de sortie fonctionnent avec le système cible, arrêtez le système source.

### <a name="data-upgrade"></a>Mise à niveau des données

La stratégie globale que vous utilisez pour effectuer votre mise à niveau (décrite dans la section [Stratégie de mise à niveau](#upgrade-strategy) de ce guide) détermine les outils que vous pouvez utiliser pour la mise à niveau de vos données. Les outils répertoriés ci-dessous sont basés sur les informations actuelles et ne constituent que des suggestions. 

#### <a name="tools-guidance"></a>Conseils sur les outils

| Stratégie                       | Outils                                                                                                             | Avantages                                                                                                                             | Considérations                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lift-and-shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Service cloud managé                                                                                                                | Les données et les ACL peuvent être copiées.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Autorisations d’outil connues fournies par Hadoop : les ACL peuvent être copiées avec cet outil                                                   | Nécessite un cluster pouvant se connecter à Data Lake Storage Gen1 et Gen2 en même temps.                                                                                                                                                                                   |
| **Copie ponctuelle et copie incrémentielle** | Azure Data Factory                                                                                                    | Service cloud managé                                                                                                                | Les données et les ACL peuvent être copiées. Pour la prise en charge de la copie incrémentielle dans ADF, les données doivent être organisées en séries chronologiques. Le plus court intervalle entre les copies incrémentielles est de [15 minutes](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Adoption parallèle**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Prise en charge la réplication cohérente. En cas d’utilisation d’un environnement 100 % Hadoop connecté à Azure Data Lake Storage, prise en charge de la réplication bidirectionnelle | Si vous n’utilisez pas d’environnement 100 % Hadoop, il peut y avoir un délai dans la réplication.                                                                                                                                                                                                                                                  |

Notez que certains produits tiers peuvent gérer la mise à niveau de Data Lake Storage Gen1 vers Data Lake Storage Gen2 sans passer par les outils de copie de données/métadonnées présentés ci-dessus (par exemple : [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Ils offrent une expérience centralisée qui permet la migration des données ainsi que la migration des charges de travail. Vous devrez peut-être effectuer une mise à niveau hors-bande pour les outils en dehors de leur écosystème.

#### <a name="considerations"></a>Considérations

* Vous devez créer manuellement le compte Data Lake Storage Gen2 avant de commencer la mise à niveau, dans la mesure où les instructions actuelles n’incluent pas de processus de création automatique de compte Gen2 basé sur les informations de votre compte Gen1. Assurez-vous que vous comparez les processus de création de comptes [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) et [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 prend uniquement en charge les fichiers de 5 To maximum. Pour procéder à une mise à niveau vers Data Lake Storage Gen2, vous devrez peut-être redimensionner les fichiers dans Data Lake Storage Gen1 de façon à ce que leur taille soit inférieure à 5 To.

* Si vous utilisez un outil qui ne copie pas les ACL, ou si vous ne souhaitez pas les copier, vous devrez définir les ACL sur la destination manuellement au niveau supérieur approprié. Pour cela, utilisez l’Explorateur Stockage. Assurez-vous que ces listes de contrôle d’accès sont les ACL par défaut afin que les fichiers et dossiers copiés en héritent.

* Dans Data Lake Storage Gen1, le niveau le plus élevé auquel vous pouvez définir les ACL se trouve à la racine du compte. Dans Data Lake Storage Gen2, toutefois, le niveau le plus élevé auquel vous pouvez définir des ACL est le dossier racine d’un conteneur, pas le compte global. Par conséquent, si vous souhaitez définir des ACL par défaut au niveau du compte, vous devez dupliquer les ACL de tous les systèmes de fichiers dans votre compte Data Lake Storage Gen2.

* Les restrictions concernant l’attribution de noms de fichier diffèrent entre les deux systèmes de stockage. Ces différences concernent plus particulièrement la copie depuis Data Lake Storage Gen2 vers Data Lake Storage Gen1, car ce dernier compte davantage de restrictions.

### <a name="application-upgrade"></a>Mise à niveau de l’application

Lorsque vous avez besoin de créer des applications sur Data Lake Storage Gen1 ou Data Lake Storage Gen2, vous devez tout d’abord choisir une interface de programmation appropriée. Lors de l’appel d’une API sur cette interface, vous devez fournir l’URI et les informations d’identification appropriés. La représentation de ces trois éléments, l’API, l’URI et la fourniture des informations d’identification, est différente dans Data Lake Storage Gen1 et dans Data Lake Storage Gen2. De ce fait, dans le cadre de la mise à jour des applications, vous devrez mapper ces trois constructions en conséquence.

#### <a name="uri-changes"></a>Modifications de l’URI

La tâche principale ici consiste à traduire les URI ayant le préfixe `adl://` en URI ayant le préfixe `abfss://`.

Le schéma d’URI pour Data Lake Storage Gen1 est expliqué [ici](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) en détail, mais de manière générale, il suit le format *adl://mydatalakestore.azuredatalakestore.net/\<chemin_fichier\>.*

Le schéma d’URI permettant d’accéder aux fichiers Data Lake Storage Gen2 est expliqué [ici](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) en détail, mais de manière générale, il suit le format `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Vous devez passer par vos applications existantes et vérifier que vous avez correctement modifié les URI pour qu’ils pointent vers les URI Data Lake Storage Gen2. Vous devez également ajouter les informations d’identification appropriées. Enfin, la mise hors service des applications et leur remplacement par les nouvelles applications devront s’aligner étroitement avec votre stratégie de mise à niveau globale.

#### <a name="custom-applications"></a>Applications personnalisées

En fonction de l’interface que votre application utilise avec Data Lake Storage Gen1, vous devrez la modifier pour l’adapter à Data Lake Storage Gen2.

##### <a name="rest-apis"></a>API REST

Si votre application utilise les API REST de Data Lake Storage, vous devrez modifier votre application pour qu’elle utilise les API REST de Data Lake Storage Gen2. Des liens sont fournis dans la section *Interfaces de programmation*.

##### <a name="sdks"></a>Kits SDK

Comme indiqué dans la section *Évaluer votre préparation à la mise à niveau*, les kits de développement logiciel (SDK) ne sont pas disponibles actuellement. Si vous souhaitez effectuer le portage de vos applications vers Data Lake Storage Gen2, nous vous recommandons d’attendre que des SDK pris en charge soient disponibles.

##### <a name="powershell"></a>PowerShell

Comme indiqué dans la section Évaluer votre préparation à la mise à niveau, la prise en charge de PowerShell n’est pas disponible actuellement pour le plan de données.

Vous pouvez remplacer les applets de commande du plan de gestion par les applets appropriées dans Data Lake Storage Gen2. Des liens sont fournis dans la section *Interfaces de programmation*.

##### <a name="cli"></a>Interface de ligne de commande

Comme indiqué dans la section *Évaluer votre préparation à la mise à niveau*, la prise en charge de la CLI n’est pas disponible actuellement pour le plan de données.

Vous pouvez remplacer les commandes du plan de gestion par les commandes appropriées dans Data Lake Storage Gen2. Des liens sont fournis dans la section *Interfaces de programmation*.

### <a name="analytics-frameworks-upgrade"></a>Mise à niveau des frameworks analytiques

Si votre application crée des métadonnées sur les informations dans le magasin, comme des chemins de fichiers et de dossiers explicites, vous devrez effectuer des actions supplémentaires après la mise à niveau des données/métadonnées du magasin. Cela est particulièrement vrai pour les frameworks analytiques telles qu’Azure HDInsight, Databricks, etc., qui créent généralement des données de catalogue sur les données du magasin.

Les frameworks analytiques fonctionnent avec les données et les métadonnées stockées dans les magasins distants tels que Data Lake Storage Gen1 et Gen2. Par conséquent, en théorie, les moteurs peuvent être éphémères et être réactivés uniquement lorsque des tâches doivent s’exécuter sur les données stockées.

Toutefois, pour optimiser les performances, les frameworks analytiques peuvent créer des références explicites aux fichiers et aux dossiers stockés dans le magasin distant, puis créer un cache pour les conserver. Si l’URI des données distantes change, par exemple, un cluster qui stockait les données dans Data Lake Storage Gen1 précédemment souhaite maintenant les stocker dans Data Lake Storage Gen2, l’URI du même contenu copié sera différent. Par conséquent, après la mise à niveau de ces données et métadonnées, les caches de ces moteurs doivent également être mis à jour ou réinitialisés

Dans le cadre du processus de planification, vous devrez identifier votre application et déterminer comment les informations des métadonnées peuvent être réinitialisées pour pointer vers les données qui sont désormais stockées dans Data Lake Storage Gen2. Voici des conseils qui vous aideront à mettre à niveau les frameworks analytiques.

#### <a name="azure-databricks"></a>Azure Databricks

En fonction de la stratégie de mise à niveau choisie, les étapes sont différentes. La présente section suppose que vous avez choisi la stratégie de «Lift-and-shift ». En outre, l’espace de travail Databricks existant qui accédait aux données d’un compte Data Lake Storage Gen1 est censé fonctionner avec les données copiées dans le compte de Data Lake Storage Gen2.

Tout d’abord, assurez-vous que vous avez créé le compte Gen2 puis copié les données et métadonnées de Gen1 vers Gen2 à l’aide d’un outil approprié. Ces outils sont indiqués dans la section [Mise à niveau des données](#data-upgrade) de ce guide.

Ensuite, [mettez à niveau](https://docs.azuredatabricks.net/user-guide/clusters/index.html) votre cluster Databricks existant pour commencer à utiliser le runtime de Databricks 5.1 ou version ultérieure, qui doit prendre en charge Data Lake Storage Gen2.

Les étapes suivantes dépendent de la façon dont l’espace de travail Databricks existant accède aux données du compte Data Lake Storage Gen1. L’accès peut s’effectuer en appelant [directement](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) les URI adl:// depuis les notebooks, ou via des [points de montage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Si vous accédez directement aux données à partir de notebooks en fournissant les URI adl:// complètes, vous devrez parcourir chaque notebook et modifier la configuration pour accéder à l’URI correspondant dans Data Lake Storage Gen2.

Par la suite, vous devrez le reconfigurer pour qu’il pointe vers le compte Data Lake Storage Gen2. Aucune modification supplémentaire n’est nécessaire, et les notebooks devraient fonctionner comme avant.

Si vous utilisez l’une des autres stratégies de mise à niveau, vous pouvez créer une variante de la procédure ci-dessus pour répondre à vos besoins.

### <a name="azure-ecosystem-upgrade"></a>Mise à niveau de l’écosystème Azure

Chacun des outils et services présentés dans la section [Écosystème Azure](#azure-ecosystem) de ce guide devra être configuré pour fonctionner avec Data Lake Storage Gen2.

Tout d’abord, assurez-vous que l’intégration est disponible avec Data Lake Storage Gen2.

Ensuite, les éléments indiqués ci-dessus (par exemple : les URI et les informations d’identification), devront être modifiés. Vous pouvez modifier l’instance existante qui fonctionne avec Data Lake Storage Gen1, ou vous pouvez créer une instance qui fonctionnerait avec Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Mise à niveau de l’écosystème de partenaires

Collaborez avec le partenaire fournissant les composants et les outils pour vous assurer qu’ils sont compatibles avec Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Mise à niveau

### <a name="pre-upgrade"></a>Avant la mise à niveau

Dans le cadre de la mise à niveau, vous devriez avoir effectué les étapes des sections *Évaluer votre préparation à la mise à niveau* et [Planifier une mise à niveau](#planning-for-an-upgrade) de ce guide, avoir reçu toutes les informations nécessaires, et créé un plan répondant à vos besoins. Vous devrez probablement réaliser des tests pendant cette phase.

### <a name="in-upgrade"></a>Pendant la mise à niveau

Selon la stratégie choisie et la complexité de votre solution, cette phase peut être brève ou plus étendue s’il existe plusieurs charges de travail en attente d’un déplacement incrémentiel vers Data Lake Storage Gen2. Il s’agit de la partie la plus importante de votre mise à niveau.

### <a name="post-upgrade"></a>Après la mise à niveau

Une fois la transition terminée, les étapes finales impliquent une vérification approfondie de la mise à niveau. Cela implique notamment de vérifier que les données ont été copiées de manière fiable, que les listes de contrôle d’accès ont été configurées correctement, que les pipelines de bout en bout fonctionnent, etc. Une fois les vérifications terminées, vous pouvez arrêter vos anciens pipelines, supprimer vos comptes source Data Lake Storage Gen1 et adopter pleinement vos solutions basées sur Data Lake Storage Gen2.

## <a name="conclusion"></a>Conclusion

Les conseils fournis dans ce document doivent vous avoir aidé à mettre à niveau votre solution pour qu’elle utilise Data Lake Storage Gen2. 

En cas de question supplémentaire, ou si vous souhaitez faire des commentaires, utilisez la zone ci-dessous ou rendez-vous dans le [Forum de commentaires Azure](https://feedback.azure.com/forums/327234-data-lake).
