---
title: Fonctionnalités d’optimisation des performances de l’activité de copie
description: Découvrez les fonctionnalités clés qui vous aident à optimiser les performances de l’activité de copie dans Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/09/2020
ms.openlocfilehash: d37b4648c0a37f16fe5c9d8794bd78417c5780ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257884"
---
# <a name="copy-activity-performance-optimization-features"></a>Fonctionnalités d’optimisation des performances de l’activité de copie

Cet article décrit les fonctionnalités d’optimisation des performances de l’activité de copie dont vous pouvez tirer parti dans Azure Data Factory.

## <a name="data-integration-units"></a>Unités d’intégration de données

Une unité d’intégration de données est une mesure qui représente la puissance (combinaison de l’allocation de ressources de processeur, de mémoire et de réseau) d’une seule unité dans Azure Data Factory. Une unité d’intégration de données s’applique seulement à [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), et non pas au [runtime d’intégration auto-hébergé](concepts-integration-runtime.md#self-hosted-integration-runtime).

Le nombre d’unités d’intégration de données autorisées pour dynamiser l’exécution d’une activité de copie se situe **entre 2 et 256**. S’il n’est pas spécifié ou si vous choisissez « Auto » dans l’interface utilisateur, Data Factory applique dynamiquement le paramètre d’unités d’intégration de données optimal en fonction de la paire source-récepteur et du modèle de données. Le tableau suivant liste les plages d’unités d’intégration de données prises en charge et le comportement par défaut dans différents scénarios de copie :

| Scénario de copie | Plage d’unités d’intégration de données prise en charge | Unités d’intégration de données par défaut déterminées par service |
|:--- |:--- |---- |
| Entre des magasins de fichiers |- **Copie depuis ou vers un fichier unique** : 2-4 <br>- **Copie depuis et vers plusieurs fichiers** : 2-256, selon le nombre et la taille des fichiers <br><br>Par exemple, si vous copiez des données à partir d’un dossier contenant 4 fichiers volumineux et que vous choisissez de conserver la hiérarchie, le nombre effectif maximal d’unités d’intégration de données est de 16. Lorsque vous choisissez de fusionner un fichier, le nombre effectif maximal d’unités d’intégration de données est de 4. |Entre 4 et 32, selon le nombre et la taille des fichiers |
| D’un magasin de fichiers vers un magasin hors fichier |- **Copie depuis un fichier unique** : 2-4 <br/>- **Copie depuis plusieurs fichiers** : 2-256, selon le nombre et la taille des fichiers <br/><br/>Par exemple, si vous copiez des données à partir d’un dossier contenant 4 fichiers volumineux, le nombre effectif maximal d’unités d’intégration de données est de 16. |- **Copie dans Azure SQL Database ou Azure Cosmos DB** : entre 4 et 16 en fonction du niveau de récepteur (DTU/RU) et du modèle de fichier source<br>- **Copie dans Azure Synapse Analytics** à l’aide de l’instruction PolyBase ou COPY : 2<br>- Autre scénario : 4 |
| D’un magasin hors fichier vers un magasin de fichiers |- **Copie depuis des magasins de données avec l’option de partition activée** (y compris [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)) : 2-256 lors de l’écriture dans un dossier, et 2-4 lors de l’écriture dans un fichier unique. Remarquez que la partition de données par source peut utiliser jusqu’à 4 unités d’intégration de données.<br>- **Autres scénarios** : 2-4 |- **Copie depuis REST ou HTTP** : 1<br/>- **Copie depuis Amazon Redshift** à l’aide d’UNLOAD : 2<br>- **Autre scénario** : 4 |
| Entre des magasins hors fichiers |- **Copie depuis des magasins de données avec l’option de partition activée** (y compris [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)) : 2-256 lors de l’écriture dans un dossier, et 2-4 lors de l’écriture dans un fichier unique. Remarquez que la partition de données par source peut utiliser jusqu’à 4 unités d’intégration de données.<br/>- **Autres scénarios** : 2-4 |- **Copie depuis REST ou HTTP** : 1<br>- **Autre scénario** : 4 |

Vous pouvez voir les unités d’intégration de données utilisées pour chaque exécution de copie dans l’affichage de surveillance de l’activité de copie ou dans la sortie de l’activité. Pour plus d’informations, consultez [Surveillance de l’activité de copie](copy-activity-monitoring.md). Pour remplacer cette valeur par défaut, spécifiez une valeur pour la propriété `dataIntegrationUnits`, comme suit. Le *nombre réel d’unités d’intégration de données* que l’opération de copie utilise au moment de l’exécution est égal ou inférieur à la valeur configurée, en fonction de votre modèle de données.

Vous serez facturé **nombre d’unités d’intégration de données utilisées \* durée de copie \* prix unitaire/DIU-heure**. Consultez les tarifs actuels [ici](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Une monnaie locale et une remise distincte peuvent s’appliquer par type d’abonnement.

**Exemple :**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Extensibilité du runtime d’intégration auto-hébergé

Si vous souhaitez obtenir un débit plus élevé, vous pouvez appliquer un scale-up ou un scale-out au runtime d’intégration auto-hébergé :

- Si le processeur et la mémoire disponible sur le nœud du runtime d’intégration auto-hébergé ne sont pas entièrement utilisés, mais que l’exécution de travaux simultanés atteint la limite, vous devez procéder à un scale-up en augmentant le nombre de travaux simultanés pouvant s’exécuter sur un nœud.  Pour obtenir des instructions, consultez [ces informations](create-self-hosted-integration-runtime.md#scale-up).
- Si, en revanche, le processeur est élevé sur le nœud du runtime d’intégration auto-hébergé ou que la mémoire disponible est faible, vous pouvez ajouter un nouveau nœud pour faciliter le scale-out de la charge sur les divers nœuds.  Pour obtenir des instructions, consultez [ces informations](create-self-hosted-integration-runtime.md#high-availability-and-scalability).

Remarquez que dans les scénarios suivants, l’exécution d’une activité de copie unique peut tirer parti de plusieurs nœuds de runtime d’intégration auto-hébergé :

- Copiez des données depuis des magasins basés sur des fichiers, en fonction du nombre et de la taille des fichiers.
- Copiez des données depuis un magasin de données avec l’option de partition activée (y compris [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source) et [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), en fonction du nombre de partitions de données.

## <a name="parallel-copy"></a>Copie en parallèle

Vous pouvez définir la copie parallèle (propriété `parallelCopies`) sur l’activité de copie pour indiquer le parallélisme que vous voulez que l’activité de copie utilise. Vous pouvez voir cette propriété comme le nombre maximum de threads dans l’activité de copie qui lisent dans votre source ou écrivent dans vos magasins de données récepteurs en parallèle.

La copie parallèle est orthogonale aux [unités d’intégration de données](#data-integration-units) ou aux [nœuds de runtime d’intégration auto-hébergé](#self-hosted-integration-runtime-scalability). Elle est comptée sur toutes les unités d’intégration de données ou tous les nœuds de runtime d’intégration auto-hébergé.

Pour chaque exécution d’activité de copie, par défaut Azure Data Factory applique dynamiquement le paramètre de copie en parallèle optimal en fonction de la paire source-récepteur et du modèle de données. 

> [!TIP]
> Le comportement par défaut de la copie parallèle vous donne généralement le meilleur débit, déterminé automatiquement par ADF en fonction de votre paire source-récepteur, du modèle de données et du nombre d’unités d’intégration de données ou du nombre de processeurs/mémoire/nœuds du runtime d’intégration auto-hébergé. Reportez-vous à [Résoudre les problèmes de performances de l’activité de copie](copy-activity-performance-troubleshooting.md) pour savoir quand ajuster la copie en parallèle.

Le tableau suivant liste le comportement de copie en parallèle :

| Scénario de copie | Comportement de copie en parallèle |
| --- | --- |
| Entre des magasins de fichiers | `parallelCopies` détermine le parallélisme **au niveau du fichier**. La segmentation au sein de chaque fichier se produit en arrière-plan de manière automatique et transparente. Elle est conçue pour utiliser la taille de segment la plus appropriée pour un type de magasin de données particulier pour charger les données en parallèle. <br/><br/>Le nombre réel de copies en parallèle que l’activité de copie utilise au moment de l’exécution ne dépasse pas le nombre de fichiers dont vous disposez. Si le comportement de copie est défini sur **mergeFile** dans le récepteur de fichier, l’activité de copie ne peut pas tirer parti du parallélisme au niveau du fichier. |
| D’un magasin de fichiers vers un magasin hors fichier | - Lors de la copie de données dans Azure SQL Database ou Azure Cosmos DB, la copie en parallèle par défaut dépend également du niveau de récepteur (nombre de DTU/RU).<br>- Lors de la copie de données dans une table Azure, la copie en parallèle par défaut est 4. |
| D’un magasin hors fichier vers un magasin de fichiers | - Lors de la copie de données depuis un magasin de données avec l’option de partition activée (y compris [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source) et [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), la copie en parallèle par défaut est 4. Le nombre réel de copies en parallèle que l’activité de copie utilise au moment de l’exécution ne dépasse pas le nombre de partitions de données dont vous disposez. Lorsque vous utilisez le runtime d’intégration auto-hébergé et que vous copiez dans Azure Blob/ADLS Gen2, notez que le nombre effectif maximal de copies en parallèle est de 4 ou 5 par nœud de runtime d’intégration.<br>- Pour d’autres scénarios, la copie en parallèle ne prend pas effet. Même si le parallélisme est spécifié, il n’est pas appliqué. |
| Entre des magasins hors fichiers | - Lors de la copie de données dans Azure SQL Database ou Azure Cosmos DB, la copie en parallèle par défaut dépend également du niveau de récepteur (nombre de DTU/RU).<br/>- Lors de la copie de données depuis un magasin de données avec l’option de partition activée (y compris [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source) et [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), la copie en parallèle par défaut est 4.<br>- Lors de la copie de données dans une table Azure, la copie en parallèle par défaut est 4. |

Pour contrôler la charge sur les machines qui hébergent vos magasins de données ou pour ajuster les performances de copie, vous pouvez remplacer la valeur par défaut et spécifier une valeur pour la propriété `parallelCopies`. La valeur doit être un nombre entier supérieur ou égal à 1. Au moment de l’exécution, pour des performances optimales, l’activité de copie utilise une valeur inférieure ou égale à la valeur que vous avez définie.

Lorsque vous spécifiez une valeur pour la propriété `parallelCopies`, prenez en compte l’augmentation de la charge sur vos magasins de données source et récepteur. Considérez également l’augmentation de la charge pour le runtime d’intégration auto-hébergé si l’activité de copie repose sur celui-ci. Cela se produit en particulier lorsque plusieurs activités ou exécutions simultanées des mêmes activités ont lieu en même temps dans la même banque de données. Si vous remarquez que le magasin de données ou le runtime d’intégration auto-hébergé est submergé par la charge, diminuez la valeur `parallelCopies` pour alléger la charge.

**Exemple :**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>copie intermédiaire

Lorsque vous copiez des données entre une banque de données source et une banque de données réceptrice, vous pouvez choisir d’utiliser le stockage Blob comme banque intermédiaire. La fonctionnalité intermédiaire est particulièrement utile dans les cas suivants :

- **Vous voulez recevoir des données à partir de diverses banques de données dans SQL Data Warehouse via PolyBase**. SQL Data Warehouse utilise PolyBase comme un mécanisme de haut débit pour charger des données volumineuses dans SQL Data Warehouse. La source de données doit se trouver dans le stockage d’objets blob ou Azure Data Lake Store, et se conformer à des critères supplémentaires. Lorsque vous chargez des données à partir d’un magasin de données autre que le stockage Blob ou Azure Data Lake Store, vous pouvez activer la copie de données via un stockage Blob intermédiaire. Dans ce cas, Azure Data Factory effectue les transformations de données requises pour garantir la conformité vis-à-vis des exigences de PolyBase. Ensuite, il utilise PolyBase pour charger efficacement des données dans SQL Data Warehouse. Pour plus d’informations, consultez [Utiliser PolyBase pour charger des données dans Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Il peut être assez long parfois d’effectuer des déplacements de données hybrides (c’est-à-dire, de copier à partir d’une banque de données locale vers une banque de données cloud) sur une connexion réseau lente**. Pour améliorer les performances, vous pouvez utiliser une copie intermédiaire pour compresser les données locales afin de réduire le temps nécessaire pour déplacer des données vers la banque de données intermédiaire dans le cloud. Ensuite, vous pouvez décompresser les données dans la banque intermédiaire avant de charger dans la banque de données de destination.
- **Vous ne souhaitez pas ouvrir des ports autres que le port 80 et le port 443 dans votre pare-feu, en raison des stratégies informatiques d’entreprise**. Par exemple, lorsque vous copiez des données d’une banque de données locale vers un récepteur Azure SQL Database ou un récepteur Azure SQL Data Warehouse, vous devez activer les communications TCP sortantes sur le port 1433 pour le pare-feu Windows et votre pare-feu d’entreprise. Dans ce scénario, une copie intermédiaire peut tirer parti de la passerelle du runtime d’intégration auto-hébergé pour commencer par copier les données dans une instance de stockage blob intermédiaire via HTTP ou HTTPS sur le port 443. Ensuite, elle peut charger les données dans SQL Database ou SQL Data Warehouse à partir du stockage d’objet blob intermédiaire. Dans ce flux, vous n’avez pas besoin d’activer le port 1433.

### <a name="how-staged-copy-works"></a>Fonctionnement de la copie intermédiaire

Lorsque vous activez la fonctionnalité intermédiaire, les données sont d’abord copiées à partir du magasin de données source vers le stockage Blob intermédiaire (indiquez le vôtre). Ensuite, les données sont copiées à partir de la banque de données intermédiaire dans la banque de données de réceptrice. Azure Data Factory gère automatiquement le flux à deux étapes pour vous. Azure Data Factory nettoie également les données temporaires du stockage intermédiaire une fois le déplacement de données terminé.

![copie intermédiaire](media/copy-activity-performance/staged-copy.png)

Lorsque vous activez le déplacement de données à l’aide d’une banque de données intermédiaire, vous pouvez indiquer si vous souhaitez compresser les données avant de les déplacer de la banque de données source vers une banque de données intermédiaire, et les décompresser avant leur transfert d’une banque de données intermédiaire vers une banque de données réceptrice.

Actuellement, vous ne pouvez pas copier des données entre deux banques de données qui sont connectées via différents runtimes d’intégration auto-hébergés, ni avec ni sans copie intermédiaire. Dans ce scénario, vous pouvez configurer deux activités de copie explicitement chaînées pour copier à partir de la source dans la banque intermédiaire, puis de la banque intermédiaire dans la banque réceptrice.

### <a name="configuration"></a>Configuration

Configurez le paramètre **enableStaging** sur l’activité de copie pour spécifier si vous souhaitez que les données soient placées dans un stockage d’objets blob intermédiaire avant d’être chargées dans une banque de données de destination. Lorsque vous définissez **enableStaging** sur `TRUE`, spécifiez les propriétés supplémentaires répertoriées dans le tableau suivant. Vous devez créer un Stockage Azure ou un service lié à la signature d’accès partagé du stockage pour le stockage intermédiaire si vous n’en avez pas.

| Propriété | Description | Valeur par défaut | Obligatoire |
| --- | --- | --- | --- |
| enableStaging |Indiquez si vous souhaitez copier les données via un magasin de données intermédiaire. |False |Non |
| linkedServiceName |Spécifiez le nom d’un service lié [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) faisant référence à l’instance de stockage que vous utilisez comme magasin de données intermédiaire. <br/><br/> Vous ne pouvez pas utiliser le stockage avec une signature d’accès partagé pour charger les données dans SQL Data Warehouse via PolyBase. Vous pouvez l’utiliser dans tous les autres scénarios. |N/A |Oui, quand **enableStaging** est défini sur TRUE |
| path |Spécifiez le chemin du stockage Blob où vous souhaitez placer les données intermédiaires. Si vous ne renseignez pas le chemin d’accès, le service crée un conteneur pour stocker les données temporaires. <br/><br/> Ne spécifiez un chemin d’accès que si vous utilisez le stockage avec une signature d’accès partagé, ou si vous avez besoin de données temporaires dans un emplacement spécifique. |N/A |Non |
| enableCompression |Spécifie si les données doivent être compressées avant d’être copiées vers la destination. Ce paramètre réduit le volume de données transférées. |False |Non |

>[!NOTE]
> Si vous utilisez une copie intermédiaire avec compression activée, l’authentification du principal du service ou MSI pour le service lié d’objets blob intermédiaire n’est pas prise en charge.

Voici un exemple de définition de l’activité de copie avec les propriétés qui sont décrites dans le tableau précédent :

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Impact sur la facturation de la copie intermédiaire

Vous êtes facturé en fonction de deux étapes : la durée de la copie et le type de copie.

* Lorsque vous utilisez la copie intermédiaire lors d’une copie dans le cloud (qui copie des données à partir d’une banque de données cloud vers une autre banque de données cloud, les deux étapes utilisant le runtime d’intégration Azure), vous êtes facturé au prix de [somme de la durée de copie pour les étapes 1 et 2] x [prix unitaire de la copie dans le cloud].
* Lorsque vous utilisez la copie intermédiaire lors d’une copie hybride (qui copie des données à partir d’une banque de données locale vers une banque de données cloud, une étape utilisant le runtime d’intégration auto-hébergé), vous êtes facturé au prix de [durée de la copie hybride] x [prix unitaire de la copie hybride] + [durée de la copie cloud] x [prix unitaire de la copie cloud].

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Guide sur les performances et la scalabilité de l’activité de copie](copy-activity-performance.md)
- [Résoudre les problèmes de performances de l’activité de copie](copy-activity-performance-troubleshooting.md)
- [Utiliser Azure Data Factory pour migrer des données de Data Lake ou Data Warehouse vers Azure](data-migration-guidance-overview.md)
- [Migrer des données d’Amazon S3 vers le stockage Azure](data-migration-guidance-s3-azure-storage.md)