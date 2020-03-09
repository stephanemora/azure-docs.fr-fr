---
title: Superviser un runtime d'intégration dans Azure Data Factory
description: Découvrez comment surveiller plusieurs types de runtime d’intégration dans Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 2399849b87e44c5cb70d2db987ae18d8d2d9c552
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355840"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Surveiller un runtime d’intégration dans Azure Data Factory  
Le **runtime d’intégration** est l’infrastructure de calcul utilisée par Azure Data Factory pour fournir des capacités d’intégration de données entre différents environnements réseau. Azure Data Factory propose trois types de runtimes d’intégration :

- Runtime d’intégration Azure
- Runtime d’intégration auto-hébergé
- Runtime d’intégration Azure SSIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour obtenir l’état d’une instance de runtime d’intégration (IR), exécutez la commande PowerShell suivante : 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cette cmdlet renvoie des informations différentes en fonction du type de runtime d’intégration. Cet article explique les propriétés et les états de chacun des types de runtime d’intégration.  

## <a name="azure-integration-runtime"></a>Runtime d’intégration Azure
La ressource de calcul d’un runtime d’intégration Azure est entièrement gérée dans Azure, en toute flexibilité. Le tableau suivant fournit des descriptions des propriétés renvoyées par la commande **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Propriétés
Le tableau suivant fournit des descriptions des propriétés renvoyées par la cmdlet pour un runtime d’intégration Azure :

| Propriété | Description |
-------- | ------------- | 
| Nom | Nom du runtime d’intégration Azure. |  
| State | État du runtime d’intégration Azure. | 
| Emplacement | Emplacement du runtime d’intégration Azure. Pour plus d’informations sur l’emplacement d’un runtime d’intégration d’Azure, voir [Introduction au runtime d’intégration](concepts-integration-runtime.md). |
| DataFactoryName | Nom de la fabrique de données à laquelle le runtime d’intégration Azure appartient. | 
| ResourceGroupName | Nom du groupe de ressources auquel la fabrique de données appartient.  |
| Description | Description du runtime d’intégration Azure.  |

### <a name="status"></a>Statut
Le tableau suivant indique les états possibles d’un runtime d’intégration Azure :

| Statut | Commentaires/Scénarios | 
| ------ | ------------------ |
| En ligne | Le runtime d’intégration Azure est en ligne et prêt à être utilisé. | 
| Hors connexion | Le runtime d’intégration Azure est hors connexion en raison d’une erreur interne. |

## <a name="self-hosted-integration-runtime"></a>Runtime d’intégration auto-hébergé
Le tableau suivant fournit des descriptions des propriétés renvoyées par la cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Les propriétés renvoyées et l’état contiennent des informations sur le runtime d’intégration auto-hébergé dans son ensemble et sur chacun des nœuds du runtime.  

### <a name="properties"></a>Propriétés

Le tableau suivant fournit des descriptions des propriétés de surveillance pour **chaque nœud** :

| Propriété | Description | 
| -------- | ----------- | 
| Nom | Nom du runtime d’intégration auto-hébergé et des nœuds qui lui sont associés. Le nœud est un ordinateur Windows local sur lequel le runtime d’intégration auto-hébergé est installé. |  
| Statut | État du runtime d’intégration auto-hébergé dans son ensemble et de chacun des nœuds. Exemple : En ligne/Hors connexion/Limité/etc. Pour plus d’informations sur ces états, voir la section suivante. | 
| Version | Version du runtime d’intégration auto-hébergé et de chacun des nœuds. La version du runtime d’intégration auto-hébergé est déterminée d’après la version de la majorité des nœuds dans le groupe. S’il existe des nœuds de différentes versions dans la configuration du runtime d’intégration auto-hébergé, seuls les nœuds dont le numéro de version est identique à celui du runtime d’intégration auto-hébergé logique fonctionnent correctement. Les autres sont en mode limité et ont besoin d’une mise à jour manuelle (uniquement si la mise à jour automatique échoue). | 
| Mémoire disponible | Mémoire disponible dans un nœud de runtime d’intégration auto-hébergé. Cette valeur est un instantané en quasi temps réel. | 
| Utilisation du processeur | Utilisation du processeur dans un nœud de runtime d’intégration auto-hébergé. Cette valeur est un instantané en quasi temps réel. |
| Réseau (entrée/sortie) | Utilisation du réseau dans un nœud de runtime d’intégration auto-hébergé. Cette valeur est un instantané en quasi temps réel. | 
| Tâches simultanées (en cours d’exécution/limite) | **Exécution en cours**. Nombre de travaux ou tâches qui s’exécutent sur chaque nœud. Cette valeur est un instantané en quasi temps réel. <br/><br/>**Limite**. La limite correspond au nombre maximal de travaux simultanés pour chaque nœud. Cette valeur est définie selon la taille de l’ordinateur. Vous pouvez augmenter la limite pour monter en puissance l’exécution de tâches simultanées dans les scénarios avancés, quand des activités expirent alors même que le processeur, la mémoire ou le réseau est sous-utilisé. Cette fonctionnalité est également disponible avec un runtime d’intégration d’auto-hébergé à nœud unique. |
| Role | Il existe deux types de rôles dans un runtime d’intégration auto-hébergé à nœuds multiples : répartiteur et rôle de travail. Tous les nœuds sont des rôles de travail, ce qui signifie qu’ils peuvent tous être utilisés pour exécuter des tâches. Il n’existe qu’un seul nœud répartiteur. Il est utilisé pour extraire des tâches ou des travaux auprès de services cloud et pour les répartir entre différents nœuds rôles de travail. Le nœud répartiteur est également un nœud rôle de travail. |

Certains paramètres de ces propriétés semblent plus logiques dans une configuration d’au moins deux nœuds dans le runtime d’intégration auto-hébergé (autrement dit, dans un scénario d’augmentation du nombre d’instances).

#### <a name="concurrent-jobs-limit"></a>Limite de tâches simultanées

La valeur par défaut de la limite de tâches simultanées est définie selon la taille de la machine. Les facteurs utilisés pour calculer cette valeur dépendent de la quantité de RAM et du nombre de cœurs de processeur de la machine. Ainsi, plus il y a de cœurs et de mémoire, plus la limite par défaut de tâches simultanées est élevée.

Vous montez en charge en augmentant le nombre de nœuds. Lorsque vous augmentez le nombre de nœuds, la limite de tâches simultanées est constituée par la somme des valeurs de limite de tâches simultanées de tous les nœuds disponibles.  Par exemple, si un seul nœud vous permet d’exécuter un maximum de douze tâches simultanées, l’ajout de trois nœuds similaires supplémentaires vous permet d’exécuter un maximum de 48 tâches simultanées (c’est-à-dire 4 x 12). Nous vous recommandons d’augmenter la limite de tâches simultanées uniquement lorsque vous constatez une faible utilisation des ressources avec des valeurs par défaut sur chaque nœud.

Vous pouvez remplacer la valeur par défaut calculée dans le portail Azure. Sélectionnez Auteur > Connexions > Runtimes d’intégration > Modifier > Nœuds > Modifier la valeur de tâches simultanées par nœud. Vous pouvez également utiliser la commande PowerShell [update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples).
  
### <a name="status-per-node"></a>État (par nœud)
Le tableau suivant indique les états possibles d’un nœud de runtime d’intégration Azure auto-hébergé :

| Statut | Description |
| ------ | ------------------ | 
| En ligne | Le nœud est connecté au service Data Factory. |
| Hors connexion | Le nœud est hors connexion. |
| Mise à niveau | Le nœud est en cours de mise à jour automatique. |
| Limité | En raison d’un problème de connectivité. Éventuellement dû à un problème de port HTTP 8050, à un problème de connectivité du bus de service ou à un problème de synchronisation des informations d’identification. |
| Inactif | La configuration du nœud est différente de celle de la majorité des autres nœuds. |

Un nœud peut être inactif quand il ne parvient pas à se connecter à d’autres nœuds.

### <a name="status-overall-self-hosted-integration-runtime"></a>État (runtime d’intégration auto-hébergé dans son ensemble)
Le tableau suivant indique les états possibles d’un runtime d’intégration Azure auto-hébergé. Cet état dépend des états de tous les nœuds appartenant au runtime. 

| Statut | Description |
| ------ | ----------- | 
| Inscription requise | Aucun nœud n’est encore inscrit dans ce runtime d’intégration auto-hébergé. |
| En ligne | Tous les nœuds sont en ligne. |
| Hors connexion | Aucun nœud n’est en ligne. |
| Limité | L’état de tous les nœuds de ce runtime d’intégration auto-hébergé n’est pas sain. Cet état sert d’avertissement. Il peut indiquer que certains nœuds sont en panne. Cela peut être dû à un problème de synchronisation des informations d’identification sur le nœud répartiteur ou le nœud rôle de travail. |

Utilisez la cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetric**. Il s’agit d’extraire la charge utile JSON qui contient les propriétés détaillées du runtime d’intégration auto-hébergé et les valeurs des instantanés pendant toute la durée d’exécution de la cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Exemple de sortie (suppose que deux nœuds sont associés à ce runtime d’intégration auto-hébergé) :

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Runtime d’intégration Azure SSIS
Le runtime d’intégration Azure-SSIS est un cluster entièrement géré de machines virtuelles (ou nœuds) Azure. Il est dédié à l’exécution de vos packages SSIS. Il n’exécute aucune autre activité d’Azure Data Factory. Une fois configuré, vous pouvez interroger ses propriétés et surveiller ses états (dans son ensemble ou selon les nœuds).

### <a name="properties"></a>Propriétés

| Propriété/état | Description |
| --------------- | ----------- |
| CreateTime | Heure UTC de création du runtime d’intégration Azure-SSIS. |
| Nœuds | Nœuds disponibles ou alloués de votre runtime d’intégration Azure-SSIS avec états spécifiques aux nœuds (démarrage/disponibilité/recyclage/indisponibilité) et erreurs exploitables. |
| OtherErrors | Erreurs exploitables non spécifiques aux nœuds de votre runtime d’intégration Azure-SSIS. |
| LastOperation | Résultat de la dernière opération de démarrage ou d’arrêt de votre runtime d’intégration Azure-SSIS avec erreurs exploitables en cas d’échec. |
| State | État d’ensemble (initial/démarrage/arrêt/arrêté) de votre runtime d’intégration Azure-SSIS. |
| Emplacement | Emplacement de votre runtime d’intégration Azure-SSIS. |
| NodeSize | Taille de chacun des nœuds de votre runtime d’intégration Azure-SSIS. |
| NodeCount | Nombre de nœuds de votre runtime d’intégration Azure-SSIS. |
| MaxParallelExecutionsPerNode | Nombre d’exécutions en parallèle par nœud dans votre runtime d’intégration Azure-SSIS. |
| CatalogServerEndpoint | Point de terminaison du serveur Azure SQL Database/Managed Instance existant pour héberger la base de données SSISDB. |
| CatalogAdminUserName | Nom d’utilisateur administrateur du serveur Azure SQL Database/ Managed Instance existant. Le service Data Factory utilise ces informations pour préparer et gérer pour vous la base de données SSISDB. |
| CatalogAdminPassword | Mot de passe administrateur du serveur Azure SQL Database/ Managed Instance existant. |
| CatalogPricingTier | Niveau tarifaire de la base de données SSISDB hébergée par le serveur Azure SQL Database existant.  Ne s’applique pas à Azure SQL Database Managed Instance hébergeant la base de données SSISDB. |
| VNetId | ID de ressource de réseau virtuel que votre runtime d’intégration Azure-SSIS doit rejoindre. |
| Subnet | Nom du sous-réseau que pour votre runtime d’intégration Azure-SSIS doit rejoindre. |
| id | ID de ressource de votre runtime d’intégration Azure-SSIS. |
| Type | Type (managé/auto-hébergé) de votre runtime d’intégration Azure-SSIS. |
| ResourceGroupName | Nom du groupe de ressources Azure dans lequel votre fabrique de données et le runtime d’intégration Azure-SSIS ont été créés. |
| DataFactoryName | Nom de votre service Azure Data Factory. |
| Nom | Nom de votre runtime d’intégration Azure-SSIS. |
| Description | Description de votre runtime d’intégration Azure-SSIS. |

  
### <a name="status-per-node"></a>État (par nœud)

| Statut | Description |
| ------ | ----------- | 
| Démarrage en cours | Ce nœud est en cours de préparation. |
| Disponible | Ce nœud est prêt pour vous permettre de déployer ou d’exécuter des packages SSIS. |
| Recyclage | Ce nœud est en cours de réparation ou de redémarrage. |
| Non disponible | Ce nœud n’est pas prêt pour vous permettre de déployer ou d’exécuter des packages SSIS. Il présente des erreurs ou problèmes sur lesquels vous pouvez intervenir et que vous pouvez résoudre. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>État (runtime d’intégration Azure SSIS dans son ensemble)

| État global | Description | 
| -------------- | ----------- | 
| Initial | Les nœuds de votre runtime d’intégration Azure-SSIS ont été alloués ou préparés. | 
| Démarrage en cours | Les nœuds de votre runtime d’intégration Azure-SSIS sont en cours d’affectation ou de préparation et la facturation a commencé. |
| Démarré | Les nœuds de votre runtime d’intégration Azure-SSIS ont été alloués ou préparés et ils sont prêts pour vous permettre de déployer et d’exécuter des packages SSIS. |
| En cours d’arrêt  | Les nœuds de votre runtime d’intégration Azure-SSIS sont libérés. |
| Arrêté | Les nœuds de votre runtime d’intégration Azure-SSIS ont été libérés et la facturation a pris fin. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Surveiller le runtime d’intégration Azure-SSIS dans de portail Azure

Les captures d’écran suivantes montrent comment sélectionner le runtime d’intégration (IR) Azure-SSIS pour la surveillance, et fournissent un exemple des informations qui s’affichent.

![Sélectionner le runtime d’intégration Azure-SSIS pour la surveillance](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Voir des informations sur le runtime d’intégration Azure-SSIS](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Surveiller le runtime d’intégration Azure-SSIS avec PowerShell

Utilisez un script tel que l’exemple suivant pour vérifier l’état du runtime d’intégration Azure-SSIS.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Plus d’informations sur le runtime d’intégration Azure-SSIS

Pour plus d’informations sur le runtime d’intégration Azure-SSIS, voir les articles suivants :

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, y compris sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Procédure : Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le tutoriel et fournit des instructions sur la façon d’utiliser Azure SQL Database Managed Instance et de joindre le runtime d’intégration à un réseau virtuel. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment le faire évoluer en lui ajoutant des nœuds supplémentaires. 
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). Cet article fournit des informations conceptuelles sur la façon d’attacher un runtime d’intégration Azure-SSIS à un réseau virtuel Azure. Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants pour surveiller les pipelines de différentes façons : 

- [Démarrages rapides : create a data factory](quickstart-create-data-factory-dot-net.md) (Créer une fabrique de données)
- [Use Azure Monitor to monitor Data Factory pipelines](monitor-using-azure-monitor.md) (Utiliser Azure Monitor pour surveiller les pipelines Data Factory)
