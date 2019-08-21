---
title: Activité de copie dans Azure Data Factory | Microsoft Docs
description: Découvrez l’activité de copie dans Azure Data Factory que vous pouvez utiliser pour copier des données d’une banque de données source prise en charge vers une banque de données réceptrice prise en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a8265496c475566ec7a87a19eab6d975838e9da4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966385"
---
# <a name="copy-activity-in-azure-data-factory"></a>Activité de copie dans Azure Data Factory

## <a name="overview"></a>Vue d'ensemble

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [Version actuelle](copy-activity-overview.md)

Dans Azure Data Factory, vous pouvez utiliser l’activité de copie pour copier des données entre des banques de données locales et dans cloud. Une fois les données copiées, elles peuvent être transformées et analysées à l’aide d’autres activités. Vous pouvez également utiliser l’activité de copie pour publier les résultats de transformation et d’analyse pour l’aide à la décision (BI) et l’utilisation d’application.

![Rôle d’activité de copie](media/copy-activity-overview/copy-activity.png)

L’activité de copie est exécutée sur un [runtime d’intégration](concepts-integration-runtime.md). Pour différents scénarios de copie de données, différentes versions du runtime d’intégration peuvent être exploitées :

* Lors de la copie de données entre banques de données accessibles publiquement via Internet à partir de n’importe quelle adresse IP, l’activité de copie peut être dynamisée par un **runtime d’intégration Azure** qui est sécurisé, fiable et évolutif et [disponible globalement](concepts-integration-runtime.md#integration-runtime-location).
* Lors de la copie de données entre banques de données locales ou en réseau avec contrôle d’accès (par exemple, Réseau virtuel Microsoft Azure), vous devez configurer un **runtime intégré auto-hébergé** pour dynamiser la copie des données.

Un runtime d’intégration doit être associé à chaque banque de données source et réceptrice. Découvrez plus de détails sur la manière dont l’activité de copie [détermine le runtime intégré à utiliser](concepts-integration-runtime.md#determining-which-ir-to-use).

Pour copier des données d’une source vers un récepteur, l’activité de copie suit les étapes suivantes. Le service qui alimente l’activité de copie :

1. Lit les données d’une banque de données source.
2. Effectue les opérations de sérialisation/désérialisation, de compression/décompression, de mappage de colonnes, etc. Il effectue ces opérations en se basant sur les configurations du jeu de données d’entrée, du jeu de données de sortie et de l’activité de copie.
3. Écrit les données dans la banque de données réceptrice/de destination.

![Présentation de l’activité de copie](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Banques de données et formats pris en charge

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formats de fichiers pris en charge

Vous pouvez utiliser l’activité de copie pour **copier des fichiers en l'état** entre deux banques de données de fichiers, auquel cas les données sont copiées efficacement sans aucune sérialisation/désérialisation.

L’activité de copie prend également en charge la lecture et l’écriture de fichiers dans des formats spécifiés : **Texte, JSON, Avro, ORC et Parquet** et la compression et la décompression des fichiers avec les codecs suivants : **GZip, Deflate, BZip2 et ZipDeflate**. Pour plus d’informations, consultez [Formats de fichier et de compression pris en charge](supported-file-formats-and-compression-codecs.md).

Par exemple, vous pouvez effectuer les activités de copie suivantes :

* Copier les données dans le SQL Server local et les écrire dans Azure Data Lake Storage Gen2 au format Parquet.
* Copier des fichiers au format texte (CSV) provenant d’un système de fichiers local et les écrire dans des objets blob Azure au format Avro.
* Copier les fichiers compressés depuis le système de fichiers local, les décompresser, puis accéder à Azure Data Lake Storage Gen2.
* Copier des données au format texte compressé GZip (CSV) provenant d’objets blob Azure et les écrire dans une base de données Azure SQL.
* Et d’autres nombreux cas impliquant la sérialisation/désérialisation ou la compression/décompression.

## <a name="supported-regions"></a>Régions prises en charge

Le service qui propose l’activité de copie est disponible mondialement, dans les régions et zones géographiques répertoriées dans [Emplacement du runtime d’intégration](concepts-integration-runtime.md#integration-runtime-location). La topologie globalement disponible garantit le déplacement efficace des données en évitant généralement les sauts entre régions. Consultez la section [Services par région](https://azure.microsoft.com/regions/#services) pour connaître la disponibilité de Data Factory et du déplacement des données dans une région.

## <a name="configuration"></a>Configuration

Pour utiliser l’activité de copie dans Azure Data Factory, vous devez :

1. **Créer des services liés pour les banques de données source et réceptrice.** Pour connaître la configuration et les propriétés prises en charge, voir la section « Propriétés du service lié » de l’article relatif au connecteur. La liste des connecteurs pris en charge figure dans la section [Banques de données et formats pris en charge](#supported-data-stores-and-formats).
2. **Créer des jeux de données pour les banques de données source et réceptrice.** Reportez-vous à la section « Propriétés du jeu de données » des articles relatifs au connecteur source et récepteur pour connaître la configuration et les propriétés prises en charge.
3. **Créer un pipeline avec une activité de copie.** La section suivante fournit un exemple.

### <a name="syntax"></a>Syntaxe

Le modèle suivant d’activité de copie contient une liste exhaustive des propriétés prises en charge. Spécifiez celles qui correspondent à votre scénario.

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

### <a name="syntax-details"></a>Détails de la syntaxe

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type d’une activité de copie doit être définie sur : **Copy** | OUI |
| inputs | Spécifiez le jeu de données que vous avez créé qui pointe vers les données sources. L’activité de copie ne prend en charge qu’une seule entrée. | OUI |
| outputs | Spécifiez le jeu de données que vous avez créé qui pointe vers les données du récepteur. L’activité de copie ne prend en charge qu’une seule sortie. | OUI |
| typeProperties | Groupe de propriétés pour configurer l’activité de copie. | OUI |
| source | Spécifiez le type de source de la copie et les propriétés correspondantes concernant la façon d’extraire les données.<br/><br/>Découvrez plus de détails dans la section « Propriétés de l’activité de copie » de l’article sur le connecteur répertorié dans [Banques de données et formats pris en charge](#supported-data-stores-and-formats). | OUI |
| sink | Spécifiez le type de récepteur de copie et les propriétés correspondantes concernant la manière d’écrire les données.<br/><br/>Découvrez plus de détails dans la section « Propriétés de l’activité de copie » de l’article sur le connecteur répertorié dans [Banques de données et formats pris en charge](#supported-data-stores-and-formats). | OUI |
| translator | Spécifiez des mappages de colonnes explicites de la source au récepteur. S’applique lorsque le comportement de copie par défaut ne peut pas répondre à vos besoins.<br/><br/>Découvrez plus de détails sur le [Mappage de schéma et de type de données](copy-activity-schema-and-type-mapping.md). | Non |
| dataIntegrationUnits | Spécifiez la puissance du [runtime d’intégration Azure](concepts-integration-runtime.md) pour dynamiser la copie des données. Anciennement appelé Unités de déplacement de données cloud. <br/><br/>Plus d’informations, consultez [unités d’intégration de données](copy-activity-performance.md#data-integration-units). | Non |
| parallelCopies | Spécifiez le parallélisme que l’activité de copie doit utiliser lors de la lecture des données de la source et l’écriture des données sur le récepteur.<br/><br/>Découvrez plus de détails sur la [Copie parallèle](copy-activity-performance.md#parallel-copy). | Non |
| enableStaging<br/>stagingSettings | Choisissez cette option pour placer les données dans un stockage blob intermédiaire au lieu de les copier des données directement de la source au récepteur.<br/><br/>Découvrez les scénarios et des détails de configuration utiles d’une [Copie intermédiaire](copy-activity-performance.md#staged-copy). | Non |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Choisissez comment gérer les lignes incompatibles lors de la copie de données de la source vers le récepteur.<br/><br/>Découvrez plus de détails sur la [Tolérance de panne](copy-activity-fault-tolerance.md). | Non |

## <a name="monitoring"></a>Surveillance

Vous pouvez surveiller l’exécution de l’activité de copie dans l’interface utilisateur « Créer et surveiller » d’Azure Data Factory ou par programmation. Vous pouvez ensuite comparer les performances et la configuration de votre scénario aux [performances de référence](copy-activity-performance.md#performance-reference) de l’activité de copie testée en interne.

### <a name="monitor-visually"></a>Surveiller visuellement

Pour surveiller visuellement l’exécution de l’activité de copie, accédez à votre fabrique de données -> **Créer et surveiller** -> **onglet Surveiller**. Une liste d’exécutions de pipeline s’affiche avec un lien « Afficher les exécutions d’activité » dans la colonne  **Actions**.

![Surveiller des exécutions de pipelines](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Cliquez pour afficher la liste des activités dans cette exécution de pipeline. Dans la colonne **Actions** figurent des liens vers l’entrée, la sortie, les erreurs (si l’exécution de l’activité de copie échoue) et les détails de l’activité de copie.

![Surveiller des exécutions d’activités](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Cliquez sur le lien « **Détails** » sous **Actions** pour afficher les détails et les caractéristiques de performances de l’exécution de l’activité de copie. Parmi les informations répertoriées figurent le volume/les lignes/les fichiers de données copiés de la source vers le récepteur, le débit, les étapes effectuées avec la durée correspondante, et les configurations utilisées pour votre scénario de copie.

>[!TIP]
>Dans certains scénarios, vous pouvez également voir « **Conseils pour le réglage des performances** » en haut de la page de surveillance de la copie, qui vous indique le goulot d’étranglement identifié et vous guide pour savoir quelles modifications apporter pour accélérer le débit de copie ; consultez l’exemple détaillé [ici](#performance-and-tuning).

**Exemple : copier d’Amazon S3 vers Azure Data Lake Store**
![Surveiller les détails de l’exécution d’activité](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exemple : copier d’Azure SQL Database vers Azure SQL Data Warehouse à l’aide de la copie intermédiaire**
![Surveiller les détails de l’exécution d’activité](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Surveiller par programmation

Les détails de l’exécution de l’activité de copie et les caractéristiques de performances sont également retournés dans la section résultat d’exécution de l’activité copie -> section Sortie. Voici une liste exhaustive ; seuls les détails applicables à votre scénario de copie seront affichés. Découvrez comment surveiller l’exécution de l’activité dans la [section relative à la surveillance du démarrage rapide](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nom de la propriété  | Description | Unité |
|:--- |:--- |:--- |
| dataRead | Taille des données lues à partir de la source | Valeur Int64 en **octets** |
| dataWritten | Taille des données écrites dans le récepteur | Valeur Int64 en **octets** |
| filesRead | Nombre de fichiers copiés lors de la copie de données à partir du stockage de fichier. | Valeur Int64 (aucune unité) |
| filesWritten | Nombre de fichiers copiés lors de la copie de données vers le stockage de fichier. | Valeur Int64 (aucune unité) |
| sourcePeakConnections | Nombre maximal de connexions simultanées établies au magasin de données source lors de l’exécution de l’activité de copie. | Valeur Int64 (aucune unité) |
| sinkPeakConnections | Nombre maximal de connexions simultanées établies pour recevoir le magasin de données lors de l’exécution de l’activité de copie. | Valeur Int64 (aucune unité) |
| rowsRead | Nombre de lignes lues à partir de la source (non applicable pour une copie binaire). | Valeur Int64 (aucune unité) |
| rowsCopied | Nombre de lignes copiées dans le récepteur (non applicable pour une copie binaire). | Valeur Int64 (aucune unité) |
| rowsSkipped | Nombre de lignes incompatibles ignorées. Vous pouvez activer la fonctionnalité en définissant « enableSkipIncompatibleRow » sur true. | Valeur Int64 (aucune unité) |
| copyDuration | Durée de la copie. | Valeur Int32 en secondes |
| throughput | Taux de transfert des données. | Nombre à virgule flottante exprimé en **Ko/s** |
| sourcePeakConnections | Nombre maximal de connexions simultanées établies au magasin de données source pendant la copie. | Valeur Int32 |
| sinkPeakConnections| Nombre maximal de connexions simultanées établies au magasin de données récepteur pendant la copie.| Valeur Int32 |
| sqlDwPolyBase | Si PolyBase est utilisé lors de la copie de données dans SQL Data Warehouse. | Boolean |
| redshiftUnload | Si UNLOAD est utilisé lors de la copie de données à partir de Redshift. | Boolean |
| hdfsDistcp | Si DistCp est utilisé lors de la copie de données à partir de HDFS. | Boolean |
| effectiveIntegrationRuntime | Affichez la ou les infrastructures Integration Runtime permettant de dynamiser l’exécution d’activité au format « `<IR name> (<region if it's Azure IR>)` ». | Texte (chaîne) |
| usedDataIntegrationUnits | Unités d’intégration de données effectives pendant la copie. | Valeur Int32 |
| usedParallelCopies | Nombre effectif de parallelCopies pendant la copie. | Valeur Int32 |
| redirectRowPath | Chemin d’accès du journal des lignes incompatibles ignorées dans le stockage blob que vous configurez sous « redirectIncompatibleRowSettings ». Voir exemple ci-dessous. | Texte (chaîne) |
| executionDetails | Détails supplémentaires sur les étapes effectuées lors de l’activité de copie, ainsi que les étapes correspondantes, la durée, les configurations utilisées, et ainsi de suite. Il n’est pas recommandé d’analyser cette section, car elle peut changer.<br/><br/>ADF indique également les durées détaillées (en secondes) passées sur les étapes respectives dans `detailedDurations`. Les durées de ces étapes sont exclusives et seules celles qui s’appliquent à l’exécution de l’activité de copie donnée s’affichent :<br/>- **Durée de mise en file d’attente** (`queuingDuration`): Temps écoulé jusqu’à ce que l’activité de copie commence sur le runtime d’intégration. Si vous utilisez le runtime d’intégration auto-hébergé et si cette valeur est importante, suggérez de vérifier la capacité et l’utilisation du runtime d’intégration, puis adaptez en fonction de votre charge de travail. <br/>- **Durée du script de pré-copie** (`preCopyScriptDuration`) : Temps écoulé entre le début de l’activité de copie sur le runtime d’intégration et la fin de l’exécution du script de pré-copie de l’activité de copie dans la banque de données réceptrice. S’applique lorsque vous configurez le script de pré-copie. <br/>- **Durée jusqu’au premier octet** (`timeToFirstByte`): Temps écoulé entre la fin de l’étape précédente et la réception par le runtime du premier octet de la banque de données source. S’applique à une source non basée sur un fichier. Si cette valeur est élevée, suggérez de vérifier et d’optimiser la requête ou le serveur.<br/>- **Durée du transfert** (`transferDuration`) : Temps écoulé entre la fin de l’étape précédente et le transfert par le runtime de toutes les données de la source vers le récepteur. | Array |
| perfRecommendation | Copier les conseils sur le réglage des performances Pour plus d’informations, consultez la section [Performances et réglage](#performance-and-tuning). | Array |

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

## <a name="schema-and-data-type-mapping"></a>Mappage du schéma et du type de données

Voir la section [Mappage du schéma et du type de données](copy-activity-schema-and-type-mapping.md) qui décrit la manière dont l’activité de copie mappe vos données source au récepteur.

## <a name="fault-tolerance"></a>Tolérance de panne

Par défaut, l’activité de copie arrête la copie de données et retourne une erreur quand elle rencontre des données incompatibles entre la source et le récepteur. Vous pouvez définir une configuration explicite pour ignorer et journaliser les lignes incompatibles et ne copier que les données compatibles pour assurer la réussite de la copie. Pour plus de détails, voir [Tolérance de panne de l’activité de copie](copy-activity-fault-tolerance.md).

## <a name="performance-and-tuning"></a>Performances et réglage

Consultez [Guide des performances et de l’optimisation de l’activité de copie](copy-activity-performance.md), qui décrit les facteurs clés affectant les performances du déplacement de données dans Azure Data Factory (activité de copie). Il répertorie également les performances observées lors des tests internes, et présente les différentes manières d’optimiser les performances de l’activité de copie.

Dans certains cas, lorsque vous exécutez une activité de copie dans ADF, vous voyez directement « **Conseils pour le réglage des performances** » en haut de la [page de surveillance de l’activité de copie](#monitor-visually) comme indiqué dans l’exemple suivant. En plus de vous indiquer le goulot d’étranglement identifié pour l’exécution de la copie, il vous guide sur les modifications à apporter pour accélérer le débit de copie. Actuellement, les conseils pour le réglage des performances offrent des suggestions : par exemple, utiliser PolyBase lors de la copie de données dans Azure SQL Data Warehouse, augmenter l’unité de requête Azure Cosmos DB ou l’unité de transaction de base de données Azure SQL Database quand la ressource côté magasin de données est le goulot d’étranglement, supprimer la copie intermédiaire non nécessaire, etc. Les règles d'optimisation des performances seront également enrichies de façon progressive.

**Exemple : copier dans Azure SQL Database avec des conseils pour le réglage des performances**

Dans cet exemple, lors de l’exécution de la copie, ADF constate que le récepteur Azure SQL DB atteint une utilisation d’unité de transaction de base de données élevée, ce qui ralentit les opérations d’écriture. La suggestion est donc d’augmenter le niveau de base de code SQL Azure avec plus d’unités de transaction de bases de données.

![Surveillance de la copie avec conseils d'optimisation des performances](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Copie incrémentielle
Data Factory prend en charge les scénarios de copie incrémentielle de données delta d’un magasin de données source vers un magasin de données récepteur. Consultez [Didacticiel : Copier de façon incrémentielle des données](tutorial-incremental-copy-overview.md).

## <a name="read-and-write-partitioned-data"></a>Lire et écrire des données partitionnées
Dans la version 1, Azure Data Factory prenait en charge la lecture et l’écriture de données partitionnées à l’aide des variables système SliceStart/SliceEnd/WindowStart/WindowEnd. Dans la version actuelle, ce comportement est obtenu à l’aide d’un paramètre de pipeline ayant comme valeur une heure de début ou une heure planifiée de déclencheur. Pour plus d’informations, consultez la page [Guide pratique pour lire ou écrire des données partitionnées](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Étapes suivantes
Voir les procédures de démarrage rapide, didacticiels et exemples suivants :

- [Copier des données d’un emplacement vers un autre dans le même Stockage Blob Azure](quickstart-create-data-factory-dot-net.md)
- [Copier des données de Stockage Blob Azure vers Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Copier des données d’une base de données SQL Server locale vers Azure](tutorial-hybrid-copy-powershell.md)
