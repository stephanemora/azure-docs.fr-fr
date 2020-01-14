---
title: Activité de copie dans Azure Data Factory
description: En savoir plus sur l’activité de copie dans Azure Data Factory. Vous pouvez l’utiliser pour copier des données à partir d’un magasin de données source pris en charge vers un magasin de données de récepteur pris en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 893ef88647824398ec106a964cbacf118bb14308
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440331"
---
# <a name="copy-activity-in-azure-data-factory"></a>Activité de copie dans Azure Data Factory

> [!div class="op_single_selector" title1="Sélectionnez la version de Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [Version actuelle](copy-activity-overview.md)

Dans Azure Data Factory, vous pouvez utiliser l’activité de copie pour copier des données entre des banques de données locales et dans cloud. Une fois que vous avez copié les données, vous pouvez utiliser d’autres activités pour les transformer et les analyser ultérieurement. Vous pouvez également utiliser l’activité de copie pour publier les résultats de transformation et d’analyse pour l’aide à la décision (BI) et l’utilisation d’application.

![Rôle de l’activité de copie](media/copy-activity-overview/copy-activity.png)

L’activité de copie est exécutée sur un [runtime d’intégration](concepts-integration-runtime.md). Vous pouvez utiliser différents types de runtimes d’intégration pour différents scénarios de copie de données :

* Lorsque vous copiez des données entre deux magasins de données accessibles publiquement via Internet à partir de n’importe quelle adresse IP, vous pouvez utiliser le runtime d’intégration Azure pour l’activité de copie. Ce runtime d’intégration est sécurisé, fiable, évolutif et [disponible dans le monde entier](concepts-integration-runtime.md#integration-runtime-location).
* Lorsque vous copiez des données vers et à partir de banques de données situées localement ou dans un réseau avec contrôle d’accès (par exemple, un réseau virtuel Azure), vous devez configurer un runtime d’intégration auto-hébergé.

Un runtime d’intégration doit être associé à chaque magasin de données source et récepteur. Pour plus d’informations sur la façon dont l’activité de copie détermine le runtime d’intégration à utiliser, consultez [Choix du runtime d’intégration](concepts-integration-runtime.md#determining-which-ir-to-use).

Pour copier des données d’une source vers un récepteur, le service qui exécute l’activité de copie effectue les étapes suivantes :

1. Lit les données d’une banque de données source.
2. Effectue les opérations de sérialisation/désérialisation, de compression/décompression, de mappage de colonnes, et ainsi de suite. Il effectue ces opérations en se basant sur les configurations du jeu de données d’entrée, du jeu de données de sortie et de l’activité de copie.
3. Écrit les données dans la banque de données réceptrice/de destination.

![Vue d’ensemble de l’activité de copie](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Banques de données et formats pris en charge

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formats de fichiers pris en charge

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Vous pouvez utiliser l’activité de copie pour copier des fichiers en l'état entre deux banques de données de fichiers, auquel cas les données sont copiées efficacement sans sérialisation ou désérialisation. En outre, vous avez la possibilité d'analyser ou de générer des fichiers d’un format donné. Vous pouvez notamment effectuer ce qui suit :

* Copier des données à partir d’une base de données SQL Server locale et écrire dans Azure Data Lake Storage Gen2 au format Parquet.
* Copier des fichiers au format texte (CSV) à partir d’un système de fichiers local et les écrire dans le stockage d’objets BLOB Azure au format Avro.
* Copier des fichiers compressés à partir d’un système de fichiers local, les décompresser à la volée et écrire les fichiers extraits dans Azure Data Lake Storage Gen2.
* Copier des données au format de texte compressé Gzip (CSV) à partir du stockage Blob Azure et les écrire dans Azure SQL Database.
* Beaucoup d’autres activités qui nécessitent la sérialisation/désérialisation ou la compression/décompression.

## <a name="supported-regions"></a>Régions prises en charge

Le service qui permet l’activité de copie est disponible mondialement, dans les régions et zones géographiques répertoriées dans [Emplacements du runtime d’intégration Azure](concepts-integration-runtime.md#integration-runtime-location). La topologie globalement disponible garantit le déplacement efficace des données en évitant généralement les sauts entre régions. Consultez [Produits par région](https://azure.microsoft.com/regions/#services) pour vérifier la disponibilité de Data Factory et le déplacement des données dans une région spécifique.

## <a name="configuration"></a>Configuration

Pour utiliser l’activité de copie dans Azure Data Factory, vous devez :

1. **Créer des services liés pour le magasin de données source et le magasin de données récepteur.** Reportez-vous à la section « Propriétés du service lié » de l’article relatif au connecteur pour obtenir des informations sur la configuration et les propriétés prises en charge. Vous trouverez la liste des connecteurs pris en charge dans la section [Magasins de données et formats pris en charge](#supported-data-stores-and-formats) dans cet article.
2. **Créer des jeux de données pour la source et le récepteur.** Pour plus d’informations sur la configuration et les propriétés prises en charge, reportez-vous aux sections « Propriétés du jeu données » des articles relatifs au connecteur source et récepteur.
3. **Créer un pipeline avec l’activité de copie.** La section suivante fournit un exemple.

### <a name="syntax"></a>Syntaxe

Le modèle suivant d’activité de copie contient une liste complète des propriétés prises en charge. Spécifiez celles qui correspondent à votre scénario.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Détails de la syntaxe

| Propriété | Description | Requis ? |
|:--- |:--- |:--- |
| type | Pour une activité de copie, définissez sur `Copy` | Oui |
| inputs | Spécifiez le jeu de données que vous avez créé qui pointe vers les données sources. L’activité de copie ne prend en charge qu’une seule entrée. | Oui |
| outputs | Spécifiez le jeu de données que vous avez créé qui pointe vers les données de récepteur. L’activité de copie ne prend en charge qu’une seule sortie. | Oui |
| typeProperties | Spécifiez les propriétés pour configurer l’activité de copie. | Oui |
| source | Spécifiez le type de source de copie et les propriétés correspondantes pour la récupération des données.<br/>Pour plus d’informations, consultez la section « Propriétés de l’activité de copie » de l’article sur le connecteur répertorié dans [Magasins de données et formats pris en charge](#supported-data-stores-and-formats). | Oui |
| sink | Spécifiez le type de récepteur de copie et les propriétés correspondantes pour l’écriture des données.<br/>Pour plus d’informations, consultez la section « Propriétés de l’activité de copie » de l’article sur le connecteur répertorié dans [Magasins de données et formats pris en charge](#supported-data-stores-and-formats). | Oui |
| translator | Spécifiez des mappages de colonnes explicites de la source au récepteur. Cette propriété s’applique lorsque le comportement de copie par défaut ne répond pas à vos besoins.<br/>Pour plus d’informations, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md). | Non |
| dataIntegrationUnits | Spécifiez une mesure qui représente la quantité d’énergie que le [runtime d’intégration Azure](concepts-integration-runtime.md) utilise pour la copie des données. Ces unités étaient auparavant appelées unités de déplacement de données Cloud. <br/>Pour plus d’informations, consultez [Unités d’intégration de données](copy-activity-performance.md#data-integration-units). | Non |
| parallelCopies | Spécifiez le parallélisme que l’activité de copie doit utiliser lors de la lecture des données de la source et l’écriture des données vers le récepteur.<br/>Pour plus d’informations, voir [Copie en parallèle](copy-activity-performance.md#parallel-copy). | Non |
| conserves | Spécifiez s’il faut conserver les métadonnées/ACL lors de la copie des données. <br/>Pour plus d’informations, consultez [Conserver les métadonnées](copy-activity-preserve-metadata.md). |Non |
| enableStaging<br/>stagingSettings | Spécifiez s’il faut effectuer une copie intermédiaire des données intermédiaires dans le stockage Blob au lieu de copier directement les données de la source vers le récepteur.<br/>Pour plus d’informations sur les scénarios utiles et les détails de la configuration, consultez [Copie intermédiaire](copy-activity-performance.md#staged-copy). | Non |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Choisissez comment gérer les lignes incompatibles lorsque vous copiez des données de la source vers le récepteur.<br/>Pour plus d’informations, consultez [Tolérance aux pannes](copy-activity-fault-tolerance.md). | Non |

## <a name="monitoring"></a>Surveillance

Vous pouvez surveiller l’exécution de l’activité de copie dans l’interface utilisateur **Créer et surveiller** d’Azure Data Factory ou par programmation.

### <a name="monitor-visually"></a>Surveiller visuellement

Pour surveiller visuellement l’exécution de l’activité de copie, accédez à votre fabrique de données, puis accédez à **Créer et surveiller**. Sous l’onglet **Surveiller**, vous pouvez voir une liste des exécutions de pipeline avec le bouton **Afficher l’exécution de l’activité** dans la colonne **Actions** :

![Surveiller des exécutions de pipelines](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Sélectionnez **Afficher les exécutions d’activités** pour afficher la liste des activités dans l’exécution du pipeline. Dans la colonne **Actions** figurent des liens vers l’entrée, la sortie, les erreurs (si l’exécution de l’activité de copie échoue) et les détails de l’activité de copie :

![Surveiller des exécutions d’activités](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Sélectionnez le bouton **Détails** dans la colonne **Actions** pour afficher les détails de l’exécution de l’activité de copie et les caractéristiques de performances. Vous pouvez voir des informations telles que le volume/le nombre de lignes/le nombre de fichiers de données copiés de la source vers le récepteur, le débit, les étapes de l’activité de copie avec les durées correspondantes et les configurations utilisées pour votre scénario de copie.

>[!TIP]
>Dans certains scénarios, vous verrez également des **Conseils sur l’optimisation des performances** en haut de la page d’analyse de copie. Ces conseils vous indiquent les goulots d’étranglement identifiés et fournissent des informations sur les modifications à apporter pour améliorer le débit de copie. Pour obtenir un exemple, consultez la section [Performances et paramétrage](#performance-and-tuning) de cet article.

**Exemple : Copier d’Amazon S3 vers Azure Data Lake Store**
![Surveiller les détails de l’exécution d’activité](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exemple : Copier d’Azure SQL Database vers Azure SQL Data Warehouse avec la copie intermédiaire**
![Surveiller les détails de l’exécution d’activité](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Surveiller par programmation

Les détails de l’exécution de l’activité de copie et les caractéristiques de performances sont également retournés dans la section **Résultat d’exécution de l’activité de copie** > section **Sortie**. Voici une liste complète des propriétés qui peuvent être retournées. Vous ne verrez que les propriétés qui s’appliquent à votre scénario de copie. Pour plus d’informations sur la surveillance des exécutions d’activités, consultez [Surveiller une exécution de pipeline](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nom de la propriété  | Description | Unité |
|:--- |:--- |:--- |
| dataRead | Quantité de données lues à partir de la source. | Valeur Int64, en octets |
| dataWritten | Quantité de données écrites dans le récepteur. | Valeur Int64, en octets |
| filesRead | Nombre de fichiers copiés lors de la copie à partir du stockage de fichiers. | Valeur Int64 (aucune unité) |
| filesWritten | Nombre de fichiers copiés lors de la copie à partir du stockage de fichiers. | Valeur Int64 (aucune unité) |
| sourcePeakConnections | Nombre maximal de connexions simultanées établies vers le magasin de données source lors de l’exécution de l’activité de copie. | Valeur Int64 (aucune unité) |
| sinkPeakConnections | Nombre maximal de connexions simultanées établies vers le magasin de données récepteur lors de l’exécution de l’activité de copie. | Valeur Int64 (aucune unité) |
| rowsRead | Nombre de lignes lues à partir de la source (non applicable pour une copie binaire). | Valeur Int64 (aucune unité) |
| rowsCopied | Nombre de lignes copiées vers le récepteur (non applicable pour une copie binaire). | Valeur Int64 (aucune unité) |
| rowsSkipped | Nombre de lignes incompatibles qui ont été ignorées. Vous pouvez faire en sorte que les lignes incompatibles soient ignorées en définissant `enableSkipIncompatibleRow` sur true. | Valeur Int64 (aucune unité) |
| copyDuration | Durée de l’exécution de la copie. | Valeur Int32, en secondes |
| throughput | Taux de transfert de données. | Nombre à virgule flottante, en Kbits/s |
| sourcePeakConnections | Nombre maximal de connexions simultanées établies vers le magasin de données source lors de l’exécution de l’activité de copie. | Valeur Int32 (aucune unité) |
| sinkPeakConnections| Nombre maximal de connexions simultanées établies vers le magasin de données récepteur lors de l’exécution de l’activité de copie.| Valeur Int32 (aucune unité) |
| sqlDwPolyBase | Indique si PolyBase est utilisé lorsque les données sont copiées dans SQL Data Warehouse. | Boolean |
| redshiftUnload | Indique si UNLOAD est utilisé lorsque les données sont copiées à partir de Redshift. | Boolean |
| hdfsDistcp | Indique si DistCp est utilisé lorsque les données sont copiées à partir de HDFS. | Boolean |
| effectiveIntegrationRuntime | Runtime d’intégration (IR) ou runtimes utilisés pour alimenter l’exécution de l’activité, au format `<IR name> (<region if it's Azure IR>)`. | Texte (chaîne) |
| usedDataIntegrationUnits | Unités d’intégration de données effectives pendant la copie. | Valeur Int32 |
| usedParallelCopies | Nombre effectif de parallelCopies pendant la copie. | Valeur Int32 |
| redirectRowPath | Chemin d’accès au journal des lignes incompatibles ignorées dans le stockage Blob que vous configurez dans la propriété `redirectIncompatibleRowSettings`. Consultez [Tolérance de panne](#fault-tolerance) plus loin dans cet article. | Texte (chaîne) |
| executionDetails | Détails supplémentaires sur les phases de l’activité de copie et les étapes, durées, configurations, etc. correspondantes. Nous vous déconseillons d’analyser cette section, car elle est susceptible de changer.<br/><br/>Data Factory signale également les durées détaillées (en secondes) consacrées aux différentes étapes sous `detailedDurations`. Les durées de ces étapes sont exclusives. Seules les durées s’appliquant à l’exécution d’activité de copie donnée s’affichent :<br/>**Durée de mise en file d’attente**  (`queuingDuration`) : Durée avant le démarrage effectif de l’activité de copie sur le runtime d’intégration. Si vous utilisez un runtime d’intégration auto-hébergé et que cette valeur est élevée, vérifiez la capacité et l’utilisation du runtime d'intégration, puis montez en puissance ou pontez en charge en fonction de votre charge de travail. <br/>**Durée du script de pré-copie** (`preCopyScriptDuration`) : Temps écoulé entre le moment où l’activité de copie démarre sur le runtime d’intégration et le moment où l’activité de copie a fini d’exécuter le script de pré-copie dans le magasin de données récepteur. S’applique lorsque vous configurez le script de pré-copie. <br/>**Temps jusqu’au premier octet** (`timeToFirstByte`) : Temps écoulé entre la fin de l’étape précédente et l’heure à laquelle le runtime d'intégration reçoit le premier octet du magasin de données source. S’applique aux sources non basées sur des fichiers. Si cette valeur est élevée, vérifiez et optimisez la requête ou le serveur.<br/>**Durée du transfert** (`transferDuration`) : Temps écoulé entre la fin de l’étape précédente et l’heure à laquelle le runtime d'intégration transfère toutes les données de la source au récepteur. | Array |
| perfRecommendation | Copier les conseils sur le réglage des performances Pour plus d’informations, consultez [Performances et réglage](#performance-and-tuning). | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="incremental-copy"></a>Copie incrémentielle

Data Factory vous permet de copier de façon incrémentielle des données delta d’un magasin de données source vers un magasin de données récepteur. Pour plus d’informations, consultez [Didacticiel : Copier les données de façon incrémentielle](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Performances et réglage

Le [Guide des performances et de l’évolutivité de l’activité de copie](copy-activity-performance.md) décrit les facteurs clés qui affectent les performances du déplacement des données par le biais de l’activité de copie dans Azure Data Factory. Il répertorie également les valeurs de performances observées pendant le test et explique comment optimiser les performances de l’activité de copie.

Dans certains scénarios, lorsque vous exécutez une activité de copie dans Data Factory, vous voyez des **Conseils sur le réglage des performances** en haut de la [Page de surveillance de l’activité de copie](#monitor-visually), comme indiqué dans l’exemple suivant. Les conseils vous indiquent le goulot d’étranglement identifié pour l’exécution de copie donnée. Ils fournissent également des informations sur les modifications à apporter pour améliorer le débit de copie. Actuellement, les conseils pour le réglage des performances fournissent des suggestions comme l’utilisation de PolyBase lors de la copie de données dans Azure SQL Data Warehouse, l’augmentation d’unités de requête Azure Cosmos DB ou d’unités de transaction de base de données Azure SQL Database lorsque la ressource côté magasin de données est le goulet d’étranglement, et la suppression des copies intermédiaires inutiles.

**Exemple : Copier dans Azure SQL Database, avec des conseils sur le réglage des performances**

Dans cet exemple, lors de l’exécution d’une copie, Data Factory effectue le suivi d’une utilisation DTU élevée dans le récepteur Azure SQL Database. Cette condition ralentit les opérations d’écriture. La suggestion consiste à augmenter les DTU sur le niveau d’Azure SQL Database :

![Surveillance de la copie avec conseils d'optimisation des performances](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="preserve-metadata-along-with-data"></a>Conserver les métadonnées avec les données

Lors de la copie des données de la source vers le récepteur, dans des scénarios tels que la migration d'un lac de données, vous pouvez également choisir de conserver les métadonnées et ACL avec les données à l’aide de l’activité de copie. Pour plus d’informations, consultez [Conserver les métadonnées](copy-activity-preserve-metadata.md).

## <a name="schema-and-data-type-mapping"></a>Mappage du schéma et du type de données

Pour plus d’informations sur la façon dont l’activité de copie met en correspondance vos données sources et votre récepteur, consultez [Mappage de type de données et de schéma](copy-activity-schema-and-type-mapping.md).

## <a name="fault-tolerance"></a>Tolérance de panne

Par défaut, l’activité de copie arrête la copie des données et retourne un échec lorsque les lignes de données sources sont incompatibles avec les lignes de données du récepteur. Pour que la copie aboutisse, vous pouvez configurer l’activité de copie afin d’ignorer et de journaliser les lignes incompatibles et de copier uniquement les données compatibles. Pour plus d’informations, consultez [Tolérance de panne de l’activité de copie](copy-activity-fault-tolerance.md).

## <a name="next-steps"></a>Étapes suivantes
Voir les procédures de démarrage rapide, didacticiels et exemples suivants :

- [Copier des données d’un emplacement vers un autre dans le même compte de stockage Blob Azure](quickstart-create-data-factory-dot-net.md)
- [Copier des données de stockage Blob Azure vers Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Copier des données depuis une base de données SQL Server locale vers Azure](tutorial-hybrid-copy-powershell.md)
