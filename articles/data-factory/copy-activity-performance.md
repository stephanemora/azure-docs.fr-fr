---
title: Guide sur les performances et le réglage de l’activité de copie dans Azure Data Factory | Microsoft Docs
description: Découvrez les facteurs clés ayant des répercussions sur les performances du déplacement de données dans Azure Data Factory lorsque vous utilisez l’activité de copie.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: face3719f32ccb44e7479150e94417496141f90b
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509564"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guide sur les performances et le réglage de l’activité de copie
> [!div class="op_single_selector" title1="Sélectionnez la version Azure Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Version actuelle](copy-activity-performance.md)


L’activité de copie Azure Data Factory offre une solution de chargement de données de premier ordre, sécurisée, fiable et hautes performances. Vous pouvez l’utiliser pour copier des dizaines de téraoctets de données tous les jours entre une grande variété de banques de données cloud et locales. Les performances de chargement des données rapides constituent la clé vous permettant de vous concentrer sur le problème Big Data principal : créer des solutions d’analyse avancées et obtenir des informations approfondies à l’aide de toutes ces données.

Azure fournit un ensemble de solutions de stockage de données et d’entrepôt de données d’entreprise. L’activité de copie offre une expérience de chargement de données hautement optimisé simple à configurer et à paramétrer. Avec une seule activité de copie, vous pouvez charger des données dans :

* Azure SQL Data Warehouse à 1,2 Gbit/s.
* Stockage d’objets blob Azure à 1,0 Gbit/s.
* Azure Data Lake Store à 1,0 Gbit/s.

Cet article aborde les points suivants :

* [Chiffres de référence des performances](#performance-reference) pour les banques de données source et réceptrice prises en charge afin de vous aider à planifier votre projet.
* Fonctionnalités qui peuvent améliorer le débit de la copie dans différents scénarios, notamment les [unités d’intégration de données cloud](#data-integration-units), la [copie parallèle](#parallel-copy) et la [copie intermédiaire](#staged-copy).
* [Conseils de réglage des performances](#performance-tuning-steps) pour optimiser les performances et les facteurs clés qui peuvent affecter les performances de la copie.

> [!NOTE]
> Si vous n’êtes pas familiarisé avec l’activité de copie, consultez la [vue d’ensemble de l’activité de copie](copy-activity-overview.md) avant de lire cet article.
>

## <a name="performance-reference"></a>Performances de référence

À titre de référence, le tableau suivant décrit la valeur de débit de copie en Mbits/s pour les paires de source et de récepteur données durant une seule exécution de l’activité de copie à partir de tests internes. À des fins de comparaison, il montre également comment les différents paramètres des [unités d’intégration de données](#data-integration-units) ou d’[extensibilité du runtime d’intégration auto-hébergé](concepts-integration-runtime.md#self-hosted-integration-runtime) (plusieurs nœuds) peuvent améliorer les performances de la copie.

![Matrice des performances](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Lorsqu’une activité de copie s’exécute sur un runtime d’intégration Azure, le nombre minimal d’unités d’intégration de données (auparavant appelées Unités de déplacement de données) autorisé est de deux. S’il n’est pas spécifié, consultez les unités d’intégration de données par défaut utilisées dans [Unités d’intégration de données](#data-integration-units).

**Points à noter :**

* Le débit est calculé à l’aide de la formule suivante : [taille des données lues à partir de la source]/[durée d’exécution de l’activité de copie].
* Les chiffres de référence des performances dans le tableau ont été mesurés à l’aide du jeu de données [TPC-H](http://www.tpc.org/tpch/) durant une seule exécution de l’activité de copie. Les fichiers de test des banques basées sur fichier sont constitués de plusieurs fichiers d’une taille de 10 Go.
* Dans le cas de banques de données Azure, la source et le récepteur sont dans la même région Azure.
* Pour une copie hybride entre des banques de données en local et dans le cloud, chaque nœud de runtime d’intégration auto-hébergé était exécuté sur un ordinateur isolé de la banque de données et présentant les caractéristiques suivantes. Lorsqu’une seule activité était exécutée, l’opération de copie n’a utilisé qu’une petite partie du processeur ou de la mémoire de l’ordinateur de test, ou de la bande passante réseau.
    <table>
    <tr>
        <td>UC</td>
        <td>32 cœurs 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Mémoire</td>
        <td>128 Go</td>
    </tr>
    <tr>
        <td>Réseau</td>
        <td>Interface Internet : 10 Gbits/s ; interface intranet : 40 Gbits/s</td>
    </tr>
    </table>


> [!TIP]
> Vous pouvez augmenter le débit en utilisant plus d’unités d’intégration de données. Par exemple, avec 100 unités d’intégration de données, vous pouvez copier des données depuis le stockage d’objets blob Azure dans Azure Data Lake Store à un débit de 1,0 Gbit/s. Pour plus d’informations sur cette fonctionnalité et sur le scénario pris en charge, consultez la section [Unités d’intégration de données](#data-integration-units). 

## <a name="data-integration-units"></a>Unités d’intégration de données

Une unité d’intégration de données est une mesure qui représente la puissance (combinaison de l’allocation de ressources de processeur, de mémoire et de réseau) d’une seule unité dans Azure Data Factory. Une unité d’intégration de données s’applique seulement à [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), et non pas au [runtime d’intégration auto-hébergé](concepts-integration-runtime.md#self-hosted-integration-runtime).

Les unités d’intégration de données minimales pour dynamiser l’exécution d’une activité de copie est de deux. S’il n’est pas spécifié, le tableau suivant répertorie les unités d’intégration de données par défaut utilisées dans différents scénarios de copie :

| Scénario de copie | Unités d’intégration de données par défaut déterminées par service |
|:--- |:--- |
| Copie de données entre des magasins basés sur des fichiers | Entre 4 et 32, selon le nombre et la taille des fichiers |
| Copie de données dans Azure SQL Database ou Azure Cosmos DB |Entre 4 et 16, selon le niveau d’Azure SQL Database ou de Cosmos DB récepteur (nombre d’unités d’intégration de données/unités de requête) |
| Tous les autres scénarios de copie | 4 |

Pour remplacer cette valeur par défaut, spécifiez comme suit une valeur pour la propriété **dataIntegrationUnits**. Les *valeurs autorisées* pour la propriété **dataIntegrationUnits** vont jusqu’à 256. Le *nombre réel d’unités d’intégration de données* que l’opération de copie utilise au moment de l’exécution est égal ou inférieur à la valeur configurée, en fonction de votre modèle de données. Pour plus d’informations sur le niveau de gain de performances que vous pouvez obtenir lorsque vous configurez plusieurs unités pour une source et un récepteur de copie spécifiques, voir [Performances de référence](#performance-reference).

Vous pouvez voir les unités d’intégration de données utilisées pour chaque exécution de copie dans la sortie de l’activité de copie lors de l’analyse d’une exécution d’activité. Pour plus d’informations, consultez [Surveillance de l’activité de copie](copy-activity-overview.md#monitoring).

> [!NOTE]
> Pour l’instant, un nombre d’unités d’intégration de données supérieur à quatre s’applique uniquement lorsque vous copiez plusieurs fichiers à partir du Stockage Azure, d’Azure Data Lake Storage, d’Amazon S3, de Google Cloud Storage, d’un FTP cloud ou d’un SFTP cloud vers d’autres banques de données cloud.
>

**Exemple**

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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>Impact des unités d’intégration de données sur la facturation

Gardez à l’esprit que vous êtes facturé selon la durée totale de l’opération de copie. La durée totale de déplacement de données qui vous est facturée correspond à la somme de la durée de toutes les unités d’intégration de données. Si un travail de copie prenait auparavant une heure avec deux unités cloud et qu’il prend maintenant 15 minutes avec huit unités cloud, le montant total de la facture reste identique.

## <a name="parallel-copy"></a>Copie en parallèle

Vous pouvez utiliser la propriété **parallelCopies** pour indiquer le parallélisme que vous voulez que l’activité de copie utilise. Vous pouvez penser cette propriété comme le nombre maximum de threads dans une activité de copie qui peut lire dans votre source ou écrire dans vos banques de données réceptrices en parallèle.

Pour chaque exécution d’activité de copie, Azure Data Factory détermine le nombre de copies en parallèle à utiliser pour copier les données depuis la banque de données source et vers la banque de données de destination. Le nombre de copies en parallèle par défaut qu’il utilise dépend du type de source et de récepteur que vous utilisez.

| Scénario de copie | Nombre de copie en parallèle par défaut déterminé par le service |
| --- | --- |
| Copie de données entre des magasins basés sur des fichiers |Dépend de la taille des fichiers et du nombre d’unités d’intégration de données utilisées pour copier des données entre les deux banques de données cloud, ou de la configuration physique de l’ordinateur du runtime d’intégration auto-hébergé. |
| Copie de données de n’importe quelle banque source vers le stockage Table Azure |4 |
| Tous les autres scénarios de copie |1 |

> [!TIP]
> Lorsque vous copiez des données entre banques basées sur fichier, le comportement par défaut offre généralement le meilleur débit. Le comportement par défaut est déterminé automatiquement en fonction de votre modèle de fichier source.

Pour contrôler la charge sur les machines qui hébergent vos banques de données ou pour optimiser les performances de copie, vous pouvez remplacer la valeur par défaut et spécifier une valeur pour la propriété **parallelCopies**. La valeur doit être un nombre entier supérieur ou égal à 1. Au moment de l’exécution, pour des performances optimales, l’activité de copie utilise une valeur inférieure ou égale à la valeur que vous avez définie.

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

**Points à noter :**

* Lorsque vous copiez des données entre des banques basées sur fichier, **parallelCopies** détermine le parallélisme au niveau du fichier. La segmentation dans un seul fichier se produit en arrière-plan de manière automatique et transparente. Elle est conçue pour utiliser la meilleure taille de segment appropriée pour un type de banque de données source donnée pour charger des données de manière parallèle et orthogonale à la valeur **parallelCopies**. Le nombre réel de copies en parallèle que le service de déplacement de données utilise pour l’opération de copie au moment de l’exécution ne peut pas être supérieur au nombre de fichiers dont vous disposez. Si le comportement de copie est défini sur **mergeFile**, l’activité de copie ne peut pas tirer parti du parallélisme au niveau du fichier.
* Lorsque vous copiez des données à partir de banques qui ne sont pas basées sur fichier (à l’exception de la base de données Oracle comme source avec le partitionnement des données activé) vers des banques basées sur fichier, le service de déplacement de données ignore la propriété **parallelCopies**. Même si le parallélisme est spécifié, il n’est pas appliqué dans ce cas.
* La propriété **parallelCopies** est orthogonale par rapport à **dataIntegrationUnits**. La première valeur est calculée à partir de toutes les unités d’intégration de données.
* Lorsque vous spécifiez une valeur pour la propriété **parallelCopies**, songez à l’augmentation de la charge sur vos banques de données source et réceptrice. Envisagez également l’augmentation de la charge pour le runtime d’intégration auto-hébergé si l’activité de copie repose sur celui-ci, par exemple, pour une copie hybride. Cela se produit en particulier lorsque plusieurs activités ou exécutions simultanées des mêmes activités ont lieu en même temps dans la même banque de données. Si vous remarquez que la banque de données ou le runtime d’intégration auto-hébergé est submergé par la charge, diminuez la valeur **parallelCopies** pour alléger la charge.

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
| chemin d’accès |Spécifiez le chemin du stockage Blob où vous souhaitez placer les données intermédiaires. Si vous ne renseignez pas le chemin d’accès, le service crée un conteneur pour stocker les données temporaires. <br/><br/> Ne spécifiez un chemin d’accès que si vous utilisez le stockage avec une signature d’accès partagé, ou si vous avez besoin de données temporaires dans un emplacement spécifique. |N/A |Non |
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

## <a name="performance-tuning-steps"></a>Procédure de réglage des performances

Effectuez cette procédure pour régler les performances de votre service Azure Data Factory avec l’activité de copie.

1. **Établissez une ligne de base.** Pendant la phase de développement, testez votre pipeline en utilisant l’activité de copie sur un échantillon de données représentatif. Collectez les détails de l’exécution et les caractéristiques des performances suivant la [surveillance de l’activité de copie](copy-activity-overview.md#monitoring).

2. **Diagnostiquez et optimisez les performances**. Si les performances que vous observez ne répondent pas à vos attentes, identifiez les goulots d’étranglement. Ensuite, optimisez les performances pour supprimer ou réduire l’effet des goulots d’étranglement.

    Dans certains cas, lorsque vous exécutez une activité de copie dans Azure Data Factory, vous voyez un message « Conseils sur le réglage des performances » en haut de la [page de surveillance de l’activité de copie](copy-activity-overview.md#monitor-visually) comme indiqué dans l’exemple suivant. Le message vous indique le goulot d’étranglement qui a été identifié pour l’exécution de copie donnée. Il vous guide également sur les modifications à apporter pour optimiser le débit de copie. Les conseils sur le réglage des performances fournissent actuellement des suggestions telles que :

    - Utilisez PolyBase lorsque vous copiez des données dans Azure SQL Data Warehouse.
    - Augmentez les unités de requête Azure Cosmos DB ou les DTU (Database Throughput Units) Azure SQL Database lorsque la ressource sur le côté de la banque de données est le goulot d’étranglement.
    - Supprimez la copie intermédiaire inutile.

    Les règles d'optimisation des performances seront également enrichies de façon progressive.

    **Exemple : Copier dans Azure SQL Database avec des conseils sur le réglage des performances**

    Dans cet exemple, pendant une exécution de copie, Azure Data Factory constate qu’Azure SQL Database récepteur utilise intensivement une DTU, ce qui ralentit les opérations d’écriture. La suggestion consiste à augmenter le niveau d’Azure SQL Database avec plus de DTU. 

    ![Surveillance de la copie avec conseils d'optimisation des performances](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    En outre, vous trouverez ci-dessous quelques considérations courantes. Une description complète du diagnostic des performances dépasserait la portée de cet article.

   * Fonctionnalités de performances :
     * [Copie en parallèle](#parallel-copy)
     * [Unités d’intégration de données](#data-integration-units)
     * [Copie intermédiaire](#staged-copy)
     * [Extensibilité du runtime d’intégration auto-hébergé](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Runtime d’intégration auto-hébergé](#considerations-for-self-hosted-integration-runtime)
   * [Source](#considerations-for-the-source)
   * [Section sink](#considerations-for-the-sink)
   * [Sérialisation et désérialisation](#considerations-for-serialization-and-deserialization)
   * [Compression](#considerations-for-compression)
   * [Mappage de colonnes](#considerations-for-column-mapping)
   * [Autres points à considérer](#other-considerations)

3. **Étendez la configuration à l’ensemble de votre jeu de données.** Lorsque vous êtes satisfait des résultats et des performances de l’exécution, vous pouvez étendre la définition et le pipeline pour couvrir l’ensemble de votre jeu de données.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considérations relatives au runtime d’intégration auto-hébergé

Si votre activité de copie s’exécute sur un runtime d’intégration auto-hébergé, notez les points suivants :

**Configuration** : Nous vous recommandons d’utiliser un ordinateur dédié pour héberger le runtime d’intégration. Consultez [Considérations relatives à l’utilisation d’un runtime d’intégration auto-hébergé](concepts-integration-runtime.md).

**Mise à l’échelle** : Un seul runtime d’intégration auto-hébergé logique comportant un ou plusieurs nœuds peut traiter simultanément plusieurs exécutions de l’activité de copie. Si vous avez besoin de déplacer des données hybrides avec de nombreuses exécutions d’activité de copie simultanées ou avec un gros volume de données à copier, envisagez [d’augmenter la taille des instances du runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md#high-availability-and-scalability) afin de mieux utiliser vos ressources ou de configurer plus de ressources pour répondre à vos besoins de copie.

## <a name="considerations-for-the-source"></a>Considérations relatives à la source

### <a name="general"></a>Généralités

Vérifiez que la banque de données sous-jacente n’est pas submergée par d’autres charges de travail s’exécutant dessus ou s’y rapportant.

Pour les banques de données Microsoft, consultez les [rubriques relatives à la surveillance et au réglage](#performance-reference) qui sont spécifiques aux banques de données. Ces rubriques peuvent vous aider à comprendre les caractéristiques des performances des banques de données et à déterminer comment réduire les temps de réponse et optimiser le débit.

* Si vous copiez des données depuis le stockage d’objets blob vers SQL Data Warehouse, envisagez d’utiliser PolyBase pour améliorer les performances. Pour plus d’informations, consultez [Utiliser PolyBase pour charger des données dans Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Si vous copiez des données depuis HDFS vers le stockage d’objets blob Azure ou Azure Data Lake Store, envisagez d’utiliser DistCp pour améliorer les performances. Pour plus d’informations, consultez [Utiliser DistCp pour copier des données de HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Si vous copiez des données depuis Redshift vers Azure SQL Data Warehouse, le stockage d’objets blob Azure ou Azure Data Lake Store, envisagez d’utiliser UNLOAD pour améliorer les performances. Pour plus d’informations, consultez [Utiliser UNLOAD pour copier des données à partir d’Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Magasins de données basés sur un fichier

* **Taille et quantité moyennes de fichiers**: L’activité de copie transfère les données un fichier à la fois. Pour une même quantité de données à déplacer, le débit global est plus lent si les données se composent de beaucoup de petits fichiers plutôt que de quelques fichiers volumineux, en raison de la phase d’amorçage nécessaire pour chaque fichier. Vous devez autant que possible combiner plusieurs petits fichiers en fichiers plus volumineux pour augmenter le débit.
* **Format de fichier et compression** : pour d’autres méthodes permettant d’améliorer les performances, voir les sections [Considérations relatives à la sérialisation et à la désérialisation](#considerations-for-serialization-and-deserialization) et [Considérations relatives à la compression](#considerations-for-compression).

### <a name="relational-data-stores"></a>Bases de données relationnelles

* **Modèle de données** : Votre schéma de table a des répercussions sur le débit de copie. Une taille de ligne importante vous donne de meilleures performances qu’une petite taille de ligne pour copier la même quantité de données. Raison : la base de données peut récupérer plus efficacement moins de lots de données contenant moins de lignes.
* **Requête ou procédure stockée** : Optimisez la logique de la requête ou de la procédure stockée que vous spécifiez dans la source d’activité de copie pour extraire les données plus efficacement.

## <a name="considerations-for-the-sink"></a>Considérations relatives au récepteur

### <a name="general"></a>Généralités

Vérifiez que la banque de données sous-jacente n’est pas submergée par d’autres charges de travail s’exécutant dessus ou s’y rapportant.

Pour les banques de données Microsoft, consultez les [rubriques relatives à la surveillance et au réglage](#performance-reference) qui sont spécifiques aux banques de données. Ces rubriques peuvent vous aider à comprendre les caractéristiques des performances des banques de données et à déterminer comment réduire les temps de réponse et optimiser le débit.

* Si vous copiez des données depuis une banque de données vers Azure SQL Data Warehouse, envisagez d’utiliser PolyBase pour améliorer les performances. Pour plus d’informations, consultez [Utiliser PolyBase pour charger des données dans Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Si vous copiez des données depuis HDFS vers le stockage d’objets blob Azure ou Azure Data Lake Store, envisagez d’utiliser DistCp pour améliorer les performances. Pour plus d’informations, consultez [Utiliser DistCp pour copier des données de HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Si vous copiez des données depuis Redshift vers Azure SQL Data Warehouse, le stockage d’objets blob Azure ou Azure Data Lake Store, envisagez d’utiliser UNLOAD pour améliorer les performances. Pour plus d’informations, consultez [Utiliser UNLOAD pour copier des données à partir d’Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Magasins de données basés sur un fichier

* **Comportement de copie**: Si vous copiez des données d’une banque de données basée sur fichier différente, l’activité de copie propose trois options via la propriété **copyBehavior**. Elle conserve la hiérarchie, aplatit la hiérarchie ou fusionne les fichiers. La conservation ou l’aplanissement de la hiérarchie entraîne peu ou pas de surcharge de performances, mais la fusion de fichiers entraîne une augmentation de la surcharge des performances.
* **Format de fichier et compression** : pour d’autres méthodes permettant d’améliorer les performances, voir les sections [Considérations relatives à la sérialisation et à la désérialisation](#considerations-for-serialization-and-deserialization) et [Considérations relatives à la compression](#considerations-for-compression).

### <a name="relational-data-stores"></a>Bases de données relationnelles

* **Copier l’implication du comportement et des performances** : Différentes méthodes d’écriture de données dans un récepteur SQL sont possibles. Pour en savoir plus, consultez l’article [Meilleures pratiques de chargement de données dans Azure SQL Database](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Modèle de données et taille de lot**:
  * Votre schéma de table a des répercussions sur le débit de copie. Pour copier une même quantité de données, une taille de ligne importante produit de meilleures performances qu’une petite taille, car la base de données peut valider plus efficacement moins de lots de données.
  * L’activité de copie insère des données dans une série de lots. Vous pouvez définir le nombre de lignes dans un lot à l’aide de la propriété **writeBatchSize** . Si vos données comportent des lignes de petite taille, vous pouvez définir la propriété **writeBatchSize** sur une valeur plus élevée pour réduire la surcharge de traitement par lots et augmenter le débit. Si la taille de ligne de vos données est importante, soyez prudent lorsque vous augmentez la valeur **writeBatchSize**. Une valeur élevée peut entraîner un échec de la copie en raison de la surcharge de la base de données.

### <a name="nosql-stores"></a>Magasins NoSQL

* Pour le **Stockage Table**:
  * **Partition** : l’écriture de données en partitions entrelacées dégrade considérablement les performances. Classez vos données sources par clé de partition afin qu’elles soient insérées efficacement partition après partition. Vous pouvez également ajuster la logique pour écrire les données dans une seule partition.

## <a name="considerations-for-serialization-and-deserialization"></a>Considérations relatives à la sérialisation et à la désérialisation

Une sérialisation et une désérialisation peuvent survenir lorsque le jeu de données d’entrée ou de sortie est un fichier. Pour plus d’informations sur les formats de fichier pris en charge par activité de copie, consultez [Formats de fichier et de compression pris en charge](supported-file-formats-and-compression-codecs.md).

**Comportement de copie**:

* Copie de fichiers entre banques de données basées sur fichier :
  * Lorsque les jeux de données d’entrée et de sortie ont les mêmes paramètres de format de fichier ou n’en ont aucun, le service de déplacement de données exécute une *copie binaire* sans sérialisation ou désérialisation. Vous voyez un débit plus élevé par rapport au scénario, dans lequel les paramètres de format de fichier source et récepteur sont différents.
  * Lorsque les jeux de données d’entrée et de sortie sont au format texte et que seul le type d’encodage est différent, le service de déplacement de données n’effectue que la conversion de l’encodage. Il n’effectue aucune sérialisation ni désérialisation, ce qui entraîne une surcharge de performances par rapport à la copie binaire.
  * Lorsque les jeux de données d’entrée et de sortie diffèrent par leur format de fichier ou leur configuration, par exemple au niveau des séparateurs, le service de déplacement de données désérialise les données sources en flux, les transforme, puis les sérialise au format de sortie que vous avez indiqué. Cette opération donne lieu à une surcharge des performances beaucoup plus importante par rapport à d’autres scénarios.
* Lorsque vous copiez des fichiers vers ou à partir d’une banque de données qui n’est pas basée sur fichier (par exemple, d’une banque basée sur fichier vers une banque relationnelle), l’étape de sérialisation ou de désérialisation est requise. Cette étape génère une surcharge significative des performances.

**Format de fichier** : le format de fichier que vous choisissez peut affecter les performances de copie. Par exemple, Avro est un format binaire compact qui stocke des métadonnées avec des données. Il bénéficie d’une prise en charge étendue dans l’écosystème Hadoop pour le traitement et l’interrogation. Le format Avro étant plus coûteux en sérialisation et désérialisation, le débit de copie est inférieur par rapport au format texte. 

Choisissez votre format de fichier dans le flux de traitement de manière holistique. Commencez par :

- La forme dans laquelle les données sont stockées, les banques de données sources ou si elles doivent être extraites de systèmes externes.
- Le meilleur format pour le stockage, le traitement analytique et l’interrogation.
- Dans quel format les données doivent être exportées dans des mini-Data Warehouses pour les outils de génération de rapports et de visualisation.

Parfois, un format de fichier non optimal pour les performances en lecture et écriture peut constituer une option idéale pour le processus analytique général.

## <a name="considerations-for-compression"></a>Considérations relatives à la compression

Lorsque votre jeu de données d’entrée ou de sortie est un fichier, vous pouvez définir l’activité de copie pour qu’elle effectue une compression ou une décompression lors de l’écriture de données dans la destination. Lorsque vous choisissez la compression, vous trouvez un compromis entre les entrées/sorties (E/S) et le processeur. La compression des données a un coût supplémentaire en ressources de calcul. Toutefois, en retour, elle réduit les E/S réseau et le stockage. En fonction de vos données, vous pouvez constater une augmentation du débit global de copie.

**Codec** : Chaque codec de compression présente des avantages. Par exemple, bzip2 a le plus faible débit de copie, mais vous obtenez les meilleures performances de requêtes Hive avec bzip2, car vous pouvez le fractionner pour traitement. Gzip est l’option la plus équilibrée, et la plus souvent utilisée. Choisissez le codec le plus approprié pour votre scénario de bout en bout.

**Niveau** : vous avec le choix entre deux options pour chaque codec de compression, la compression la plus rapide et la compression optimale. L’option de compression la plus rapide compresse les données aussi rapidement que possible, même si le fichier résultant de l’opération n’est pas compressé de façon optimale. L’option de compression optimale nécessite plus de temps, et produit une quantité de données minimale. Vous pouvez tester les deux options pour voir celle qui offre les meilleures performances globales dans votre cas.

**Élément à prendre en compte** : pour copier une grande quantité de données entre un magasin local et le cloud, envisagez d’utiliser la [copie intermédiaire](#staged-copy) avec la compression. L’utilisation du stockage intermédiaire est utile lorsque la bande passante de votre réseau d’entreprise et de vos services Azure est le facteur limitant et que vous souhaitez que le jeu de données d’entrée et le jeu de données de sortie soient sous forme non compressée.

## <a name="considerations-for-column-mapping"></a>Considérations relatives au mappage de colonnes

Vous pouvez définir la propriété **columnMappings** dans une activité de copie pour mapper la totalité ou un sous-ensemble des colonnes d’entrée aux colonnes de sortie. Une fois que le service de déplacement de données a lu les données de la source, il doit effectuer le mappage de colonnes sur les données avant d’écrire celles-ci sur le récepteur. Ce traitement supplémentaire réduit le débit de copie.

Si votre banque de données source peut faire l’objet de requêtes, par exemple, s’il s’agit d’une banque de données relationnelle telle que SQL Database ou SQL Server ou s’il ne s’agit pas d’une banque NoSQL comme le Stockage Table ou Azure Cosmos DB, envisagez d’envoyer le filtrage de colonnes et la logique de réorganisation à la propriété **query** , au lieu d’utiliser le mappage de colonnes. Ainsi, la projection survient alors que le service de déplacement de données lit les données à partir de la banque de données source, là où elle est beaucoup plus efficace.

Pour en savoir plus, consultez [Mappage de schéma de l’activité de copie](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Autres points à considérer

Si la taille des données à copier est importante, vous pouvez ajuster votre logique métier pour partitionner davantage les données. Vous pouvez planifier l’activité de copie pour qu’elle s’exécute plus fréquemment pour réduire la taille des données pour chaque activité de copie exécutée.

Faites attention au nombre de jeux de données et d’activités de copie nécessitant qu’Azure Data Factory se connecte à la même banque de données en même temps. De nombreux travaux de copie simultanés peuvent limiter une banque de données, et entraîner une dégradation des performances, une multiplication des tentatives internes de travail de copie et, dans certains cas, des échecs d’exécution.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exemple de scénario : Copie depuis un SQL Server local vers le stockage d’objets blob

**Scénario** : Un pipeline est conçu pour copier des données d’un serveur SQL Server local vers un stockage d’objets blob au format CSV. Pour accélérer la copie des travaux, les fichiers CSV doivent être compressés au format bzip2.

**Test et analyse** : Le débit de l’activité de copie est inférieur à 2 Mbits/s, ce qui est beaucoup plus lent que le test d’évaluation des performances.

**Analyse des performances et réglage** : pour résoudre le problème de performances, nous allons tout d’abord examiner la manière dont les données sont traitées et déplacées.

- **Lecture des données** : Le runtime d’intégration ouvre la connexion à SQL Server et envoie la requête. SQL Server répond en envoyant le flux de données au runtime d’intégration via l’intranet.
- **Sérialiser et compresser les données** : Le runtime d’intégration sérialise le flux de données au format CSV, et compresse les données dans un flux bzip2.
- **Écriture des données** : Le runtime d’intégration charge le flux bzip2 vers le stockage d’objets blob via Internet.

Comme vous pouvez le voir, les données sont traitées et déplacées de manière séquentielle en continu : SQL Server > LAN > Passerelle > WAN > Stockage Blob. Les performances globales sont contrôlées par le débit minimum sur le pipeline.

![Flux de données](./media/copy-activity-performance/case-study-pic-1.png)

Un ou plusieurs des facteurs suivants peuvent entraîner un goulot d’étranglement des performances :

* **Source** : SQL Server offre lui-même un faible débit en raison des charges lourdes.
* **Runtime d’intégration auto-hébergé** :
  * **LAN** : le runtime d’intégration est éloigné de l’ordinateur SQL Server et dispose d’une connexion à faible bande passante.
  * **Runtime d’intégration** : le runtime d’intégration a atteint ses limites de charge pour effectuer les opérations suivantes :
    * **Sérialisation** : la sérialisation du flux de données au format CSV présente un débit lent.
    * **Compression** : Vous avez choisi un codec de compression lent, par exemple, bzip2, c’est-à-dire 2,8 Mbits/s avec Core i7.
  * **WAN** : La bande passante entre le réseau d’entreprise et vos services Azure est faible, par exemple, T1 = 1 544 Kbits/s ; T2 = 6 312 Kbits/s.
* **Récepteur** : le stockage Blob a un faible débit. Ce scénario est peu probable car son contrat de niveau de service (SLA) garantit un minimum de 60 Mbits/s.

Dans ce cas, la compression de données bzip2 pourrait ralentir l’ensemble du pipeline. Un basculement vers le codec de compression gzip peut résoudre ce goulot d’étranglement.

## <a name="references"></a>Références

Voici des références relatives à la surveillance et au réglage des performances pour quelques banques de données prises en charge :

* Stockage Azure, qui inclut le stockage d’objets blob et le stockage Table : [Objectifs d’évolutivité du Stockage Azure](../storage/common/storage-scalability-targets.md) et [Liste de contrôle des performances et de l’évolutivité du Stockage Azure](../storage/common/storage-performance-checklist.md).
* Azure SQL Database : Vous pouvez [surveiller les performances](../sql-database/sql-database-single-database-monitor.md) et vérifier le pourcentage de l’unité de transaction de base de données (DTU).
* Azure SQL Data Warehouse : Sa capacité est mesurée en DWU (Data Warehouse Units). Consultez [Gestion de la puissance de calcul dans Azure SQL Data Warehouse (Vue d’ensemble)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB : [Niveaux de performances dans Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Serveur SQL Server local : [Surveillance et réglage des performances](https://msdn.microsoft.com/library/ms189081.aspx).
* Serveur de fichiers local : [Réglage des performances des serveurs de fichiers](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Mappage de schéma de l’activité de copie](copy-activity-schema-and-type-mapping.md)
- [Tolérance de panne de l’activité de copie](copy-activity-fault-tolerance.md)
