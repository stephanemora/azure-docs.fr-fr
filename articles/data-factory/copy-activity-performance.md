---
title: Guide sur les performances et la scalabilité de l’activité de copie
description: Découvrez les facteurs clés ayant des répercussions sur les performances du déplacement de données dans Azure Data Factory lorsque vous utilisez l’activité de copie.
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
ms.date: 10/24/2019
ms.openlocfilehash: 1b1b02e310c98a78006d258333c0ec10e89e3b31
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927462"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guide sur les performances et la scalabilité de l’activité de copie

> [!div class="op_single_selector" title1="Sélectionnez la version Azure Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Version actuelle](copy-activity-performance.md)

Que vous souhaitiez effectuer une migration de données à grande échelle à partir d’un lac de données ou d’un entrepôt de données d’entreprise vers Azure ou ingérer des données à grande échelle à partir de différentes sources dans Azure à des fins d’analytique Big Data, il est essentiel d’obtenir des performances et une scalabilité optimales.  Azure Data Factory fournit un mécanisme performant, résilient et rentable pour l’ingestion de données à grande échelle, ce qui en fait une solution idéale pour les ingénieurs de données qui cherchent à créer des pipelines d’ingestion de données hautement performants et scalables.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

- Quel niveau de performance et de scalabilité puis-je obtenir à l’aide de l’activité de copie ADF pour les scénarios de migration de données et d’ingestion de données ?

- Quelles sont les étapes à suivre pour régler les performances de l’activité de copie ADF ?
- Quels sont les leviers d’optimisation des performances ADF que je peux utiliser pour optimiser les performances d’une exécution d’activité de copie unique ?
- Quels sont les autres facteurs hors d’ADF à prendre en compte lors de l’optimisation des performances de copie ?

> [!NOTE]
> Si vous n’êtes pas familiarisé avec l’activité de copie, consultez la [vue d’ensemble de l’activité de copie](copy-activity-overview.md) avant de lire cet article.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Scalabilité et performances de l’activité de copie réalisables avec ADF

ADF offre une architecture serverless qui autorise le parallélisme à différents niveaux, ce qui permet aux développeurs de créer des pipelines pour utiliser pleinement la bande passante de votre réseau, ainsi que les IOPS et la bande passante de stockage pour maximiser le débit de déplacement de données pour votre environnement.  Cela signifie que vous pouvez estimer le débit atteignable en mesurant le débit minimal offert par le magasin de données source, le magasin de données de destination et la bande passante réseau entre la source et la destination.  Le tableau ci-dessous calcule la durée de la copie en fonction de la taille des données et de la limite de bande passante pour votre environnement. 

| Taille des données / <br/> bandwidth | 50 Mbits/s    | 100 Mbits/s  | 500 Mbits/s  | 1 Gbit/s   | 5 Gbit/s   | 10 Gbits/s  | 50 Gbit/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 Go**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 Go**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 Go**                  | 4,6 heures    | 2,3 heures   | 0,5 heure   | 0,2 heures  | 0,05 heure | 0,02 heure | 0,0 heure   |
| **1 To**                    | 46,6 heures   | 23,3 heures  | 4,7 heures   | 2,3 heures  | 0,5 heure  | 0,2 heures  | 0,05 heure  |
| **10 To**                   | 19,4 jours  | 9,7 jours  | 1,9 jours  | 0,9 jour | 0,2 jour | 0,1 jour | 0,02 jour |
| **100 To**                  | 194,2 jours | 97,1 jours | 19,4 jours | 9,7 jours | 1,9 jours | 1 jour   | 0,2 jour  |
| **1 Po**                    | 64,7 mois    | 32,4 mois   | 6,5 mois    | 3,2 mois   | 0,6 mois   | 0,3 mois   | 0,06 mois   |
| **10 Po**                   | 647,3 mois   | 323,6 mois  | 64,7 mois   | 31.6 mois  | 6,5 mois   | 3,2 mois   | 0,6 mois    |

La copie ADF est scalable à différents niveaux :

![Mise à l’échelle de la copie ADF](media/copy-activity-performance/adf-copy-scalability.png)

- Le flux de contrôle ADF peut démarrer plusieurs activités de copie en parallèle, par exemple [Pour chaque boucle](control-flow-for-each-activity.md).
- Une seule activité de copie peut tirer parti des ressources de calcul évolutives : lorsque vous utilisez Azure Integration Runtime, vous pouvez spécifier [jusqu'à 256 DIUs](#data-integration-units) pour chaque activité de copie serverless ; lorsque vous utilisez des Integration Runtime auto-hébergés, vous pouvez mettre à l’échelle manuellement la machine ou la monter en charge sur plusieurs machines ([jusqu’à 4 nœuds](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), et une seule activité de copie partitionnera son jeu de fichiers sur tous les nœuds.
- Une activité de copie unique lit et écrit dans le magasin de données à l’aide de plusieurs threads [en parallèle](#parallel-copy).

## <a name="performance-tuning-steps"></a>Procédure de réglage des performances

Effectuez cette procédure pour régler les performances de votre service Azure Data Factory avec l’activité de copie.

1. **Choisir un jeu de données de test et établir une base de référence.** Pendant la phase de développement, testez votre pipeline en utilisant l’activité de copie sur un échantillon de données représentatif. Le jeu de données que vous choisissez doit représenter vos modèles de données classiques (structure de dossiers, modèle de fichier, schéma de données, etc.) et être suffisamment grand pour évaluer les performances de copie, par exemple il faut 10 minutes ou plus pour que l’activité de copie se termine. Collectez les détails de l’exécution et les caractéristiques des performances suivant la [surveillance de l’activité de copie](copy-activity-overview.md#monitoring).

2. **Comment optimiser les performances d’une seule activité de copie** :

   Pour commencer, nous vous recommandons d’optimiser les performances à l’aide d’une seule activité de copie.

   **Si l’activité de copie est exécutée sur un runtime d’intégration Azure :**

   Commencez avec les valeurs par défaut des paramètres [DIU (unités d’intégration de données)](#data-integration-units) et de [copie en parallèle](#parallel-copy).  Effectuez une série de tests de performances et prenez note des performances atteintes ainsi que des valeurs réelles utilisées pour les DIU et les copies parallèles.  Reportez-vous à [Supervision de l’activité de copie](copy-activity-overview.md#monitoring) pour savoir comment collecter les résultats des tests et les paramètres de performances utilisés.

   Exécutez maintenant des séries de tests de performances supplémentaires, en doublant à chaque fois la valeur du paramètre DIU.  Si vous pensez que les performances obtenues à l’aide du paramètre par défaut sont bien inférieures à vos attentes, vous pouvez augmenter considérablement le paramètre DIU dans la série de tests suivante.

   L’activité de copie doit être mise à l’échelle de façon quasi linéaire au fur et à mesure que vous augmentez le paramètre DIU.  Si, en doublant le paramètre DIU, vous ne voyez pas le double de débit, deux événements sont peut-être en train de se produire :

   - Le modèle de copie spécifique que vous exécutez ne tire pas parti de l’ajout de DIU.  Même si vous avez spécifié une valeur DIU plus grande, la valeur DIU réelle utilisée est restée la même et vous obtenez donc le même débit qu’auparavant.  Si tel est le cas, optimisez le débit agrégé en exécutant plusieurs copies simultanément comme à l’étape 3.
   - En ajoutant des DIU (plus de puissance) et en augmentant ainsi le taux d’extraction, de transfert et de chargement des données, le magasin de données source, le réseau intermédiaire ou le magasin de données de destination a atteint son goulot d’étranglement et est peut-être limité.  Si c’est le cas, essayez de contacter votre administrateur de magasin de données ou votre administrateur réseau pour augmenter la limite supérieure ou réduire le paramètre DIU jusqu’à ce que la limitation ne se produise plus.

   **Si l’activité de copie est exécutée sur un runtime d’intégration auto-hébergé :**

   Nous vous recommandons d’utiliser une machine dédiée distincte du serveur hébergeant le magasin de données pour héberger le runtime d’intégration.

   Commencez avec les valeurs par défaut du paramètre de [copie parallèle](#parallel-copy) en utilisant un seul nœud pour l’IR auto-hébergé.  Effectuez une série de tests de performances et prenez note des performances atteintes.

   Si vous souhaitez obtenir un débit plus élevé, vous pouvez appliquer un scale-up ou scale-out à l’IR auto-hébergé :

   - Si l’UC et la mémoire disponible sur le nœud de l’IR auto-hébergé ne sont pas entièrement utilisées, mais que l’exécution de travaux simultanés atteint la limite, vous devez procéder à une opération de scale-up en augmentant le nombre de travaux simultanés pouvant s’exécuter sur un nœud.  Pour obtenir des instructions, consultez [ces informations](create-self-hosted-integration-runtime.md#scale-up).
   - Si, en revanche, le processeur est élevé sur le nœud de l’IR auto-hébergé ou que la mémoire disponible est faible, vous pouvez ajouter un nouveau nœud pour faciliter l’application d’un scale-out à la charge sur plusieurs nœuds.  Pour obtenir des instructions, consultez [ces informations](create-self-hosted-integration-runtime.md#high-availability-and-scalability).

   Quand vous appliquez un scale-up ou scale-out à la capacité du runtime d’intégration auto-hébergé, répétez la série de tests de performances pour voir si vous obtenez de plus en plus de débit.  Si le débit ne s’améliore plus, il est probable que le magasin de données source, le réseau intermédiaire ou le magasin de données de destination ait atteint son goulot d’étranglement et qu’il commence à être limité. Si c’est le cas, essayez de contacter votre administrateur de magasin de données ou votre administrateur réseau pour augmenter la limite supérieure ou revenez au paramètre de mise à l’échelle précédent pour l’IR auto-hébergé. 

3. **Comment optimiser le débit agrégé en exécutant plusieurs copies simultanément :**

   Maintenant que vous avez optimisé les performances d’une seule activité de copie, si vous n’avez pas encore atteint les limites supérieures de débit de votre environnement (réseau, magasin de données source et magasin de données de destination), vous pouvez exécuter plusieurs activités de copie en parallèle à l’aide de constructions de flux de contrôle ADF telles qu’une [boucle For Each](control-flow-for-each-activity.md).

4. **Conseils sur le réglage des performances et fonctionnalités d’optimisation.** Dans certains cas, quand vous exécutez une activité de copie dans Azure Data Factory, vous voyez un message « Conseils sur le réglage des performances » en haut des [données de supervision de l’activité de copie](copy-activity-overview.md#monitor-visually) comme indiqué dans l’exemple suivant. Le message vous indique le goulot d’étranglement qui a été identifié pour l’exécution de copie donnée. Il vous guide également sur les modifications à apporter pour optimiser le débit de copie. Les conseils sur le réglage des performances fournissent actuellement des suggestions telles que :

   - Utilisez PolyBase lorsque vous copiez des données dans Azure SQL Data Warehouse.
   - Augmentez les unités de requête Azure Cosmos DB ou les DTU (Database Throughput Units) Azure SQL Database lorsque la ressource sur le côté de la banque de données est le goulot d’étranglement.
   - Supprimez la copie intermédiaire inutile.

   Les règles d'optimisation des performances seront également enrichies de façon progressive.

   **Exemple : Copier dans Azure SQL Database avec des conseils sur le réglage des performances**

   Dans cet exemple, pendant une exécution de copie, Azure Data Factory constate qu’Azure SQL Database récepteur utilise intensivement une DTU, ce qui ralentit les opérations d’écriture. La suggestion consiste à augmenter le niveau d’Azure SQL Database avec plus de DTU. 

   ![Surveillance de la copie avec conseils d'optimisation des performances](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   De plus, voici quelques-unes des fonctionnalités d’optimisation des performances que vous devez connaître :

   - [Copie en parallèle](#parallel-copy)
   - [Unités d’intégration de données](#data-integration-units)
   - [Copie intermédiaire](#staged-copy)
   - [Extensibilité du runtime d’intégration auto-hébergé](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Étendez la configuration à l’ensemble de votre jeu de données.** Lorsque vous êtes satisfait des résultats et des performances de l’exécution, vous pouvez étendre la définition et le pipeline pour couvrir l’ensemble de votre jeu de données.

## <a name="copy-performance-optimization-features"></a>Fonctionnalités d’optimisation des performances de copie

Azure Data Factory fournit les fonctionnalités d’optimisation des performances suivantes :

- [Copie en parallèle](#parallel-copy)
- [Unités d’intégration de données](#data-integration-units)
- [Copie intermédiaire](#staged-copy)

### <a name="data-integration-units"></a>Unités d’intégration de données

Une unité d’intégration de données est une mesure qui représente la puissance (combinaison de l’allocation de ressources de processeur, de mémoire et de réseau) d’une seule unité dans Azure Data Factory. Une unité d’intégration de données s’applique seulement à [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), et non pas au [runtime d’intégration auto-hébergé](concepts-integration-runtime.md#self-hosted-integration-runtime).

Vous serez facturé **nombre d’unités d’intégration de données utilisées \* durée de copie \* prix unitaire/DIU-heure**. Consultez les tarifs actuels [ici](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Une monnaie locale et une remise distincte peuvent s’appliquer par type d’abonnement.

Le nombre d’unités d’intégration de données autorisées pour dynamiser l’exécution d’une activité de copie se situe **entre 2 et 256**. S’il n’est pas spécifié ou si vous choisissez « Auto » dans l’interface utilisateur, Data Factory applique dynamiquement le paramètre d’unités d’intégration de données optimal en fonction de la paire source-récepteur et du modèle de données. Le tableau suivant liste les unités d’intégration de données par défaut utilisées dans différents scénarios de copie :

| Scénario de copie | Unités d’intégration de données par défaut déterminées par service |
|:--- |:--- |
| Copie de données entre des magasins basés sur des fichiers | Entre 4 et 32, selon le nombre et la taille des fichiers |
| Copie de données dans Azure SQL Database ou Azure Cosmos DB |Entre 4 et 16, selon le niveau d’Azure SQL Database ou de Cosmos DB récepteur (nombre d’unités d’intégration de données/unités de requête) |
| Tous les autres scénarios de copie | 4 |

Pour remplacer cette valeur par défaut, spécifiez comme suit une valeur pour la propriété **dataIntegrationUnits**. Le *nombre réel d’unités d’intégration de données* que l’opération de copie utilise au moment de l’exécution est égal ou inférieur à la valeur configurée, en fonction de votre modèle de données.

Vous pouvez voir les unités d’intégration de données utilisées pour chaque exécution de copie dans la sortie de l’activité de copie lors de l’analyse d’une exécution d’activité. Pour plus d’informations, consultez [Surveillance de l’activité de copie](copy-activity-overview.md#monitoring).

> [!NOTE]
> Actuellement, un nombre d’unités d’intégration de données supérieur à quatre s’applique uniquement quand vous copiez plusieurs fichiers à partir du Stockage Blob Azure, d’ADLS Gen1, d’ADLS Gen2, d’Amazon S3, de Google Cloud Storage, d’un FTP cloud, d’un SFTP cloud ou d’un magasin de données relationnelles cloud avec l’option de partition activée (y compris [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)) vers d’autres magasins de données cloud.

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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>Copie en parallèle

Vous pouvez utiliser la propriété **parallelCopies** pour indiquer le parallélisme que vous voulez que l’activité de copie utilise. Vous pouvez penser cette propriété comme le nombre maximum de threads dans une activité de copie qui peut lire dans votre source ou écrire dans vos banques de données réceptrices en parallèle.

Pour chaque exécution d’activité de copie, Azure Data Factory détermine le nombre de copies en parallèle à utiliser pour copier les données depuis la banque de données source et vers la banque de données de destination. Le nombre de copies en parallèle par défaut qu’il utilise dépend du type de source et de récepteur que vous utilisez.

| Scénario de copie | Nombre de copie en parallèle par défaut déterminé par le service |
| --- | --- |
| Copie de données entre des magasins basés sur des fichiers |Dépend de la taille des fichiers et du nombre d’unités d’intégration de données utilisées pour copier des données entre les deux banques de données cloud, ou de la configuration physique de l’ordinateur du runtime d’intégration auto-hébergé. |
| Copiez à partir du magasin de données relationnel avec l'option de partition activée (y compris [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source) et [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source))|4 |
| Copie de données de n’importe quelle banque source vers le stockage Table Azure |4 |
| Tous les autres scénarios de copie |1 |

> [!TIP]
> Lorsque vous copiez des données entre banques basées sur fichier, le comportement par défaut offre généralement le meilleur débit. Le comportement par défaut est déterminé automatiquement en fonction de votre modèle de fichier source.

Pour contrôler la charge sur les machines qui hébergent vos banques de données ou pour optimiser les performances de copie, vous pouvez remplacer la valeur par défaut et spécifier une valeur pour la propriété **parallelCopies**. La valeur doit être un nombre entier supérieur ou égal à 1. Au moment de l’exécution, pour des performances optimales, l’activité de copie utilise une valeur inférieure ou égale à la valeur que vous avez définie.

**Points à noter :**

- Lorsque vous copiez des données entre des banques basées sur fichier, **parallelCopies** détermine le parallélisme au niveau du fichier. La segmentation dans un seul fichier se produit en arrière-plan de manière automatique et transparente. Elle est conçue pour utiliser la meilleure taille de segment appropriée pour un type de banque de données source donnée pour charger des données de manière parallèle et orthogonale à la valeur **parallelCopies**. Le nombre réel de copies en parallèle que le service de déplacement de données utilise pour l’opération de copie au moment de l’exécution ne peut pas être supérieur au nombre de fichiers dont vous disposez. Si le comportement de copie est défini sur **mergeFile**, l’activité de copie ne peut pas tirer parti du parallélisme au niveau du fichier.
- Lorsque vous copiez des données à partir de magasins qui ne sont pas basés sur des fichiers (sauf [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source) et le connecteur [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) en tant que source avec le partitionnement des données activé) vers des magasins qui sont basés sur des fichiers, le service de déplacement des données ignore la propriété **parallelCopies**. Même si le parallélisme est spécifié, il n’est pas appliqué dans ce cas.
- La propriété **parallelCopies** est orthogonale par rapport à **dataIntegrationUnits**. La première valeur est calculée à partir de toutes les unités d’intégration de données.
- Lorsque vous spécifiez une valeur pour la propriété **parallelCopies**, songez à l’augmentation de la charge sur vos banques de données source et réceptrice. Envisagez également l’augmentation de la charge pour le runtime d’intégration auto-hébergé si l’activité de copie repose sur celui-ci, par exemple, pour une copie hybride. Cela se produit en particulier lorsque plusieurs activités ou exécutions simultanées des mêmes activités ont lieu en même temps dans la même banque de données. Si vous remarquez que la banque de données ou le runtime d’intégration auto-hébergé est submergé par la charge, diminuez la valeur **parallelCopies** pour alléger la charge.

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

### <a name="staged-copy"></a>copie intermédiaire

Lorsque vous copiez des données entre une banque de données source et une banque de données réceptrice, vous pouvez choisir d’utiliser le stockage Blob comme banque intermédiaire. La fonctionnalité intermédiaire est particulièrement utile dans les cas suivants :

- **Vous voulez recevoir des données à partir de diverses banques de données dans SQL Data Warehouse via PolyBase**. SQL Data Warehouse utilise PolyBase comme un mécanisme de haut débit pour charger des données volumineuses dans SQL Data Warehouse. La source de données doit se trouver dans le stockage d’objets blob ou Azure Data Lake Store, et se conformer à des critères supplémentaires. Lorsque vous chargez des données à partir d’un magasin de données autre que le stockage Blob ou Azure Data Lake Store, vous pouvez activer la copie de données via un stockage Blob intermédiaire. Dans ce cas, Azure Data Factory effectue les transformations de données requises pour garantir la conformité vis-à-vis des exigences de PolyBase. Ensuite, il utilise PolyBase pour charger efficacement des données dans SQL Data Warehouse. Pour plus d’informations, consultez [Utiliser PolyBase pour charger des données dans Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Il peut être assez long parfois d’effectuer des déplacements de données hybrides (c’est-à-dire, de copier à partir d’une banque de données locale vers une banque de données cloud) sur une connexion réseau lente**. Pour améliorer les performances, vous pouvez utiliser une copie intermédiaire pour compresser les données locales afin de réduire le temps nécessaire pour déplacer des données vers la banque de données intermédiaire dans le cloud. Ensuite, vous pouvez décompresser les données dans la banque intermédiaire avant de charger dans la banque de données de destination.
- **Vous ne souhaitez pas ouvrir des ports autres que le port 80 et le port 443 dans votre pare-feu, en raison des stratégies informatiques d’entreprise**. Par exemple, lorsque vous copiez des données d’une banque de données locale vers un récepteur Azure SQL Database ou un récepteur Azure SQL Data Warehouse, vous devez activer les communications TCP sortantes sur le port 1433 pour le pare-feu Windows et votre pare-feu d’entreprise. Dans ce scénario, une copie intermédiaire peut tirer parti de la passerelle du runtime d’intégration auto-hébergé pour commencer par copier les données dans une instance de stockage blob intermédiaire via HTTP ou HTTPS sur le port 443. Ensuite, elle peut charger les données dans SQL Database ou SQL Data Warehouse à partir du stockage d’objet blob intermédiaire. Dans ce flux, vous n’avez pas besoin d’activer le port 1433.

#### <a name="how-staged-copy-works"></a>Fonctionnement de la copie intermédiaire

Lorsque vous activez la fonctionnalité intermédiaire, les données sont d’abord copiées à partir du magasin de données source vers le stockage Blob intermédiaire (indiquez le vôtre). Ensuite, les données sont copiées à partir de la banque de données intermédiaire dans la banque de données de réceptrice. Azure Data Factory gère automatiquement le flux à deux étapes pour vous. Azure Data Factory nettoie également les données temporaires du stockage intermédiaire une fois le déplacement de données terminé.

![copie intermédiaire](media/copy-activity-performance/staged-copy.png)

Lorsque vous activez le déplacement de données à l’aide d’une banque de données intermédiaire, vous pouvez indiquer si vous souhaitez compresser les données avant de les déplacer de la banque de données source vers une banque de données intermédiaire, et les décompresser avant leur transfert d’une banque de données intermédiaire vers une banque de données réceptrice.

Actuellement, vous ne pouvez pas copier des données entre deux banques de données qui sont connectées via différents runtimes d’intégration auto-hébergés, ni avec ni sans copie intermédiaire. Dans ce scénario, vous pouvez configurer deux activités de copie explicitement chaînées pour copier à partir de la source dans la banque intermédiaire, puis de la banque intermédiaire dans la banque réceptrice.

#### <a name="configuration"></a>Configuration

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

#### <a name="staged-copy-billing-impact"></a>Impact sur la facturation de la copie intermédiaire

Vous êtes facturé en fonction de deux étapes : la durée de la copie et le type de copie.

* Lorsque vous utilisez la copie intermédiaire lors d’une copie dans le cloud (qui copie des données à partir d’une banque de données cloud vers une autre banque de données cloud, les deux étapes utilisant le runtime d’intégration Azure), vous êtes facturé au prix de [somme de la durée de copie pour les étapes 1 et 2] x [prix unitaire de la copie dans le cloud].
* Lorsque vous utilisez la copie intermédiaire lors d’une copie hybride (qui copie des données à partir d’une banque de données locale vers une banque de données cloud, une étape utilisant le runtime d’intégration auto-hébergé), vous êtes facturé au prix de [durée de la copie hybride] x [prix unitaire de la copie hybride] + [durée de la copie cloud] x [prix unitaire de la copie cloud].

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
- [Utiliser Azure Data Factory pour migrer des données de Data Lake ou Data Warehouse vers Azure](data-migration-guidance-overview.md)
- [Migrer des données d’Amazon S3 vers le stockage Azure](data-migration-guidance-s3-azure-storage.md)
