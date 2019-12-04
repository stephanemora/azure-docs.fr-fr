---
title: Infrastructure Integration Runtime dans Azure Data Factory
description: Découvrez l’infrastructure Integration Runtime dans Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 0b137edbfb5ca439d4ba15614225ec0973511763
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218812"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Infrastructure Integration Runtime dans Azure Data Factory
IR est l’infrastructure de calcul utilisée par Azure Data Factory pour fournir les fonctionnalités d’intégration de données suivantes entre différents environnements réseau :

- **Data Flow** : exécutez un [Data Flow](concepts-data-flow-overview.md) dans un environnement de calcul Azure managé.  
- **Déplacement des données** : copie des données entre les banques de données d’un réseau public et celles d’un réseau privé (sur un réseau privé local ou virtuel). Les connecteurs intégrés, la conversion de format, le mappage de colonnes, ainsi que les transferts de données performants et évolutifs sont pris en charge.
- **Répartition des activités** :  Répartit et analyse les activités de transformation exécutées sur un large éventail de services de calcul, tels qu’Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, etc.
- **Exécution des packages SSIS** : exécute en mode natif les packages SSIS (SQL Server Integration Services) dans un environnement Compute Azure managé.

Dans Data Factory, une activité désigne l’action à effectuer. Un service lié désigne un magasin de données cible ou un service de calcul. Un runtime d’intégration permet de créer une passerelle entre l’activité et les services liés.  Il est référencé par l’activité ou le service lié et fournit l’environnement de calcul dans lequel l’activité s’exécute ou depuis lequel elle est envoyée. L’activité peut être effectuée de la façon la plus efficace possible dans la région la plus proche du magasin de données cible ou du service de calcul, tout en respectant les exigences de conformité et de sécurité.

## <a name="integration-runtime-types"></a>Types de runtime d’intégration
Data Factory propose trois types de runtime d’intégration. Vous devez choisir le type qui répond le mieux à vos besoins en matière de fonctionnalités d’intégration de données et d’environnement réseau.  Ces trois types sont :

- Azure
- Auto-hébergé
- Azure-SSIS

Le tableau suivant décrit les fonctionnalités et l’environnement réseau pour chaque type de runtime d’intégration :

Type de runtime | Réseau public | Réseau privé
------- | -------------- | ---------------
Azure | Data Flow<br/>Déplacement des données<br/>Répartition des activités | &nbsp;
Auto-hébergé | Déplacement des données<br/>Répartition des activités | Déplacement des données<br/>Répartition des activités
Azure-SSIS | Exécution des packages SSIS | Exécution des packages SSIS

Le schéma suivant montre comment les différents runtimes d’intégration peuvent être utilisés conjointement pour offrir des fonctionnalités d’intégration de données et un environnement réseau riches :

![Différents types de runtime d’intégration](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Runtime d’intégration Azure
Un runtime d’intégration Azure peut :

- Exécution de flux de données dans Azure 
- Exécuter des activités de copie entre les magasins de données cloud
- Répartir les activités de transformation suivantes dans un réseau public : Notebook Databricks / Jar /activité Python, activité Hive HDInsight, activité Pig HDInsight, activité MapReduce HDInsight, activité Spark HDInsight, activité de diffusion en continu HDInsight, activité d’exécution par lot Machine Learning, activités des ressources de mise à jour de Machine Learning, activité de procédure stockée, activité U-SQL Data Lake Analytics, activité personnalisée .NET, activité web, activité de recherche et activité d’obtention des métadonnées.

### <a name="azure-ir-network-environment"></a>Environnement réseau du runtime d'intégration Azure
L’infrastructure Azure Integration Runtime prend en charge les connexions aux magasins de données et aux services de calcul comprenant des points de terminaison accessibles publiquement. Utilisez un runtime d’intégration auto-hébergé pour l’environnement du réseau virtuel Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Ressources de calcul et mise à l’échelle du runtime d'intégration Azure
Le runtime d’intégration Azure fournit une expérience de calcul entièrement gérée, sans serveur dans Azure.  Vous n’avez plus à vous soucier de l’approvisionnement de l’infrastructure, de l’installation du logiciel, des mises à jour correctives ou de la mise à l’échelle des besoins.  Par ailleurs, vous payez uniquement pour ce que vous utilisez.

Le runtime d’intégration Azure fournit le calcul natif pour déplacer des données entre les magasins de données cloud de manière sécurisée, fiable et efficace.  Vous pouvez définir le nombre d’unités d’intégration des données à utiliser sur l’activité de copie. Ainsi, la taille de calcul du runtime d’intégration Azure est mise à l’échelle sans que vous deviez ajuster la taille du runtime d’intégration Azure. 

La répartition des activités est une opération légère pour acheminer l’activité vers le service de calcul cible. Par conséquent, vous n’avez pas besoin de mettre la taille de calcul à l’échelle pour ce scénario.

Pour en savoir plus sur la création et la configuration d’un runtime d’intégration Azure, consultez la rubrique « Comment créer et configurer Azure IR » sous Procédures. 

> [!NOTE] 
> Le runtime d’intégration Azure possède des propriétés liées au runtime Data Flow, définissant l’infrastructure de calcul sous-jacente qui sera utilisée pour exécuter les flux de données. 

## <a name="self-hosted-integration-runtime"></a>Runtime d’intégration auto-hébergé
Un runtime d’intégration auto-hébergé peut :

- Exécuter une activité de copie entre des magasins de données cloud et un magasin de données situé sur un réseau privé.
- Répartir les activités de transformation suivantes selon les ressources de calcul dans le réseau local ou Azure : activité Hive HDInsight (BYOC - Bring Your Own Cluster), activité Pig HDInsight (BYOC), activité MapReduce HDInsight (BYOC), activité Spark HDInsight (BYOC), activité de diffusion en continu HDInsight (BYOC), activité d’exécution par lot Machine Learning, activités des ressources de mise à jour de Machine Learning, activité de procédure stockée, activité U-SQL Data Lake Analytics, activité personnalisée .NET (s’exécute sur Azure Batch), activité de recherche et activité d’obtention des métadonnées.

> [!NOTE] 
> Utilisez le runtime d’intégration auto-hébergé pour prendre en charge les magasins de données nécessitant l’utilisation de votre propre pilote tels que SAP, Hana, MySQL, etc.  Pour en savoir plus, consultez les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) est une dépendance de l’IR auto-hébergé. Vérifiez que vous avez installé JRE sur le même hôte.

### <a name="self-hosted-ir-network-environment"></a>Environnement réseau du runtime d'intégration auto-hébergé
Si vous souhaitez intégrer vos données en toute sécurité dans un environnement réseau privé, qui n’a pas de ligne de vue directe depuis l’environnement cloud public, vous pouvez installer un runtime d’intégration auto-hébergé dans l’environnement local derrière votre pare-feu d’entreprise, ou à l’intérieur d’un réseau privé virtuel.  Le runtime d’intégration auto-hébergé établit uniquement des connexions HTTP sortantes pour l’accès à Internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Ressources de calcul et mise à l’échelle du runtime d'intégration auto-hébergé
Le runtime d’intégration auto-hébergé doit être installé sur un ordinateur local ou une machine virtuelle à l’intérieur d’un réseau privé. Actuellement, nous prenons uniquement en charge l’exécution du runtime d’intégration auto-hébergé sur un système d’exploitation Windows.  

Pour obtenir un runtime d’intégration hautement disponible et évolutif, vous pouvez augmenter la taille des instances du runtime d’intégration auto-hébergé en associant l’instance logique avec plusieurs ordinateurs locaux en mode actif/actif.  Pour en savoir plus, consultez l’article [Comment créer et configurer le runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md) sous Procédures.

## <a name="azure-ssis-integration-runtime"></a>Runtime d’intégration Azure SSIS
Pour effectuer une opération lift-and-shift sur la charge de travail SSIS existante, vous pouvez créer un runtime d’intégration Azure SSIS pour exécuter les packages SSIS en mode natif.

### <a name="azure-ssis-ir-network-environment"></a>Environnement réseau du runtime d'intégration Azure SSIS
Le runtime d’intégration Azure SSIS peut être configuré dans un réseau public ou un réseau privé.  L’accès aux données sur site est pris en charge en associant le runtime d’intégration Azure SSIS à un réseau virtuel connecté à votre réseau local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Ressources de calcul et mise à l’échelle du runtime d’intégration Azure SSIS
Le runtime d’intégration Azure SSIS est un cluster entièrement géré de machines virtuelles Azure qui est chargé d’exécuter vos packages SSIS. Vous pouvez demander à votre propre serveur Azure SQL Database ou Managed Instance d’héberger le catalogue de projets/packages SSIS (SSISDB) qui y sera attaché. Vous pouvez monter en puissance le calcul en spécifiant la taille du nœud et augmenter la taille des instances en spécifiant le nombre de nœuds du cluster. Vous pouvez maîtriser le coût d’exécution de votre runtime d’intégration Azure SSIS en l’arrêtant et en le démarrant comme bon vous semble.

Pour en savoir plus, consultez l’article « Comment créer et configurer le runtime d’intégration Azure SSIS » sous Procédures.  Une fois votre runtime d’intégration créé, vous pouvez déployer et gérer vos packages SSIS existants, sans changement ou presque, à l’aide des outils SQL Server Data Tools (SSDT) et SQL Server Management Studio (SSMS), comme si vous utilisez SSIS en local.

Pour plus d’informations sur le runtime Azure-SSIS, voir les articles suivants : 

- [Didacticiel : deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Guide pratique : Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le tutoriel et fournit des instructions sur la façon d’utiliser Azure SQL Database Managed Instance et de joindre le runtime d’intégration à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment le faire évoluer en lui ajoutant des nœuds supplémentaires. 
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). Cet article fournit des informations conceptuelles sur la façon d’attacher un runtime d’intégration Azure-SSIS à un réseau virtuel Azure. Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 

## <a name="integration-runtime-location"></a>Emplacement du runtime d’intégration
L’emplacement de Data Factory contient les métadonnées de la fabrique de données. C’est également à cet endroit que le pipeline est déclenché. Une fabrique de données peut toutefois accéder à des magasins de données et à des services de calcul situés dans d’autres régions Azure pour déplacer des données entre des magasins de données ou pour traiter des données à l’aide des services de calcul. Ce comportement se réalise grâce au [runtime d’intégration globalement disponible](https://azure.microsoft.com/global-infrastructure/services/) pour garantir la conformité des données et l’efficacité, et réduire les frais de sortie de réseau.

L’emplacement du runtime d’intégration définit l’emplacement de son calcul principal, mais aussi l’emplacement où le déplacement des données, la répartition des activités et l’exécution des packages SSIS sont effectués. L’emplacement du runtime d’intégration peut être différent de l’emplacement de la fabrique de données à laquelle il appartient. 

### <a name="azure-ir-location"></a>Emplacement du runtime d'intégration Azure
Vous pouvez définir l’emplacement spécifique d’un runtime d'intégration Azure, auquel cas le déplacement des données ou la distribution d’activité se fera dans cette région spécifique. 

Si vous choisissez d’utiliser la **résolution automatique du runtime d’intégration Azure**, définie par défaut, 

- Pour une activité de copie, ADF fera au mieux pour détecter automatiquement votre récepteur et banque de données source afin de choisir le meilleur emplacement, que ce soit dans la même région si disponible ou dans région la plus proche dans la même zone géographique. S’ils ne sont pas détectables, il utilisera la région de la fabrique de données comme alternative.

- Pour l’exécution de l’activité Lookup/GetMetadata/Delete (également connue sous le nom d’activités Pipeline), la répartition de l’activité de transformation (également connue sous le nom d’activités externes) et les opérations de création (tester la connexion, dresser la liste des dossiers et des tables, prévisualiser les données), ADF utilise le runtime d’intégration dans la région Data factory.

- Pour les flux de données, ADF utilise le runtime d’intégration dans la région de la fabrique de données. 

  > [!TIP] 
  > Une bonne pratique serait de s’assurer que le flux de données s’exécute dans la même région que vos banques de données correspondantes (si possible). Vous pouvez y parvenir soit en résolvant automatiquement Azure IR (si l’emplacement de la banque de données est celui de Data Factory), soit en créant une nouvelle instance Azure IR dans la même région que vos banques de données, puis en y exécutant le flux de données. 

Vous pouvez surveiller quel emplacement du runtime d'intégration prend effet lors de l’exécution de l’activité dans la vue de surveillance de l’activité du pipeline sur l’interface utilisateur, ou dans la charge utile de la surveillance de l’activité.

>[!TIP]
>Si vos exigences en termes de conformité des données sont strictes et que vous avez besoin de vous assurer que les données restent dans une certaine zone géographique, vous pouvez explicitement créer un runtime d'intégration Azure dans une région donnée et diriger le service lié vers ce runtime d'intégration via la propriété ConnectVia. Par exemple, si vous voulez copier des données depuis Blob dans la région Sud du Royaume-Uni vers SQL DW dans la région Sud du Royaume-Uni et souhaitez vous assurer que les données ne quittent pas le Royaume-Uni, créez un runtime d'intégration dans la région Sud du Royaume-Uni et liez les deux services liés à ce runtime.

### <a name="self-hosted-ir-location"></a>Emplacement du runtime d’intégration auto-hébergé
Le runtime d’intégration auto-hébergé est logiquement enregistré auprès de la fabrique de données. Quant à vous, il vous revient de fournir le calcul utilisé pour prendre en charge ses fonctionnalités. Par conséquent, il n’existe aucune propriété d’emplacement explicite pour le runtime d’intégration auto-hébergé. 

Lorsqu’il est utilisé pour procéder au déplacement des données, le runtime d’intégration auto-hébergé extrait des données de la source et les écrit dans la destination.

### <a name="azure-ssis-ir-location"></a>Emplacement du runtime d’intégration Azure SSIS
Le choix de l’emplacement pour votre runtime d’intégration Azure SSIS est essentiel pour parvenir à un niveau de performance élevé dans vos flux de travail ETL (extraction, transformation et chargement).

- L’emplacement de votre runtime d’intégration Azure-SSIS ne doit pas nécessairement être identique à l’emplacement de votre fabrique de données, mais il doit être le même que l’emplacement de votre serveur Azure SQL Database/Managed Instance où SSISDB doit être hébergé. De cette manière, le runtime d’intégration Azure SSIS peut facilement accéder au SSISDB sans être entravé par le trafic entre les différents emplacements.
- Si vous n’avez pas de serveur Azure SQL Database/Managed Instance pour héberger SSISDB, mais que vous avez des sources/destinations de données locales, vous devez créer un serveur Azure SQL Database/Managed Instance là où un réseau virtuel est connecté à votre réseau local.  Ainsi, vous pouvez créer votre runtime d’intégration Azure-SSIS en utilisant le nouveau serveur Azure SQL Database/Managed Instance et en associant ce réseau virtuel, au même endroit, afin de réduire efficacement les déplacements de données entre les différents emplacements.
- Si l’emplacement de votre serveur Azure SQL Database/Managed Instance où SSISDB est hébergé n’est pas le même que l’emplacement où un réseau virtuel est connecté à votre réseau local, créez d’abord votre runtime d’intégration Azure-SSIS en utilisant Azure SQ Database/Managed Instance et en associant un autre réseau virtuel situé au même emplacement. Ensuite, configurez une connexion entre deux réseaux virtuels situés à différents emplacements.

Le schéma suivant représente les paramètres d’emplacement de Data Factory et de ses runtimes d’intégration :

![Emplacement du runtime d’intégration](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Choix du runtime d’intégration

### <a name="copy-activity"></a>Activité de copie

Dans le cas d’une activité de copie, les services liés source et récepteur doivent être indiqués pour définir la direction du flux de données. La logique suivante est utilisée pour déterminer l’instance de runtime d’intégration qui effectue la copie : 

- **Copie entre deux sources de données cloud** : lorsque les services liés source et du récepteur utilisent le runtime d'intégration Azure, ADF utilisera le runtime d'intégration Azure régional (si spécifié) ou déterminera automatiquement l’emplacement du runtime d'intégration Azure si vous avez choisi la résolution automatique du runtime d'intégration (par défaut) décrite dans la section [Emplacement du runtime d'intégration](#integration-runtime-location).
- **Copie entre une source de données cloud et une source de données d’un réseau privé** : si le service lié source ou récepteur pointe vers un runtime d’intégration auto-hébergé, l’activité de copie est exécutée sur ce runtime d’intégration.
- **Copie entre deux sources de données d’un réseau privé** : les services liés source et récepteur doivent tous deux pointer vers la même instance du runtime d’intégration. Ce runtime d’intégration est utilisé pour exécuter l’activité de copie.

### <a name="lookup-and-getmetadata-activity"></a>Activité Lookup/GetMetadata

L’activité Lookup/GetMetadata est exécutée sur le runtime d'intégration associé au service lié de la banque de données.

### <a name="transformation-activity"></a>Activités de transformation

Chaque activité de transformation a un service de calcul cible lié qui pointe vers un runtime d’intégration. Cette instance du runtime d’intégration se trouve au point d’envoi de l’activité de transformation.

### <a name="data-flow-activity"></a>Activité Data Flow

L’activité Data Flow est exécutée sur le runtime d’intégration qui lui est associé. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [Créer un runtime d’intégration Azure](create-azure-integration-runtime.md)
- [Créer un runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md)
- [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le tutoriel et fournit des instructions sur la façon d’utiliser Azure SQL Database Managed Instance et de joindre le runtime d’intégration à un réseau virtuel. 
