---
title: Tolérance de panne de l’activité de copie dans Azure Data Factory
description: Découvrez comment ajouter une tolérance de panne à l’activité de copie dans Azure Data Factory en ignorant les lignes incompatibles.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: 766520fe44047eee76029adf8ee1683c7b8008a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417859"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolérance de panne de l’activité de copie dans Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Version actuelle](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Avec l’activité de copie dans Azure Data Factory, vous avez deux moyens de traiter les lignes incompatibles lors de la copie de données entre les magasins de données source et récepteur :

- Vous pouvez abandonner et laisser en échec l’activité de copie en cas de détection de données incompatibles (comportement par défaut).
- Vous pouvez continuer à copier toutes les données en ajoutant une tolérance de panne et en ignorant les lignes de données incompatibles. Vous avez également la possibilité de journaliser les lignes incompatibles dans le stockage Blob Azure ou Azure Data Lake Store. afin de pouvoir examiner la cause de l’échec dans le journal, corriger les données sur la source de données, puis effectuer une nouvelle tentative de copie.

## <a name="supported-scenarios"></a>Scénarios pris en charge
L’activité de copie offre la possibilité de détecter, d’ignorer et de journaliser les incompatibilités de données suivantes :

- **Incompatibilité entre le type de données sources et le type natif récepteur** 

    Exemple : copie de données d’un fichier CSV du stockage Blob dans une base de données SQL avec une définition de schéma contenant trois colonnes de type INT. Les lignes du fichier CSV qui contiennent des données numériques, telles que 123 456 789, sont correctement copiées dans le magasin récepteur. En revanche, les lignes qui contiennent des valeurs non numériques, telles que 123 456, abc, sont considérées comme incompatibles et ignorées.

- **Incompatibilité du nombre de colonnes entre la source et le récepteur**

    Exemple : copie de données d’un fichier CSV du stockage Blob Azure dans une base de données SQL avec une définition de schéma contenant six colonnes. Les lignes du fichier CSV qui contiennent six colonnes sont correctement copiées dans le magasin récepteur. Les lignes du fichier CSV qui contiennent plus de six colonnes sont considérées comme incompatibles et ignorées.

- **Violation de clé primaire lors de l’écriture dans SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Exemple : copie de données depuis un serveur SQL dans une base de données SQL. Il existe une clé primaire définie dans la base de données SQL réceptrice, mais aucune clé primaire correspondante n’est définie dans le serveur SQL source. Les lignes en double qui peuvent exister dans la source ne sont pas copiées dans le récepteur. L’activité de copie ne copie que la première ligne des données sources dans le récepteur. Toutes les lignes sources suivantes contenant une valeur de clé primaire en double sont considérées comme incompatibles et ignorées.

>[!NOTE]
>- Pour charger des données dans SQL Data Warehouse avec PolyBase, configurez les paramètres natifs de la tolérance de panne de PolyBase en spécifiant les règles de rejet « [polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink) » dans l’activité de copie. Vous pouvez toujours activer la redirection des lignes PolyBase incompatibles vers Blob ou ADLS normalement, comme indiqué ci-dessous.
>- Cette fonctionnalité ne s’applique pas lorsque l’activité de copie est configurée de sorte à appeler [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Cette fonctionnalité ne s’applique pas lorsque l’activité de copie est configurée pour appeler une [procédure stockée à partir d’un récepteur SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Configuration
L’exemple suivant fournit une définition JSON pour configurer la manière d’ignorer les lignes incompatibles dans le cadre de l’activité de copie :

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Indique s’il faut ignorer ou non les lignes incompatibles durant la copie. | True<br/>False (valeur par défaut) | Non
redirectIncompatibleRowSettings | Groupe de propriétés qui peuvent être spécifiées lorsque vous souhaitez journaliser les lignes incompatibles. | &nbsp; | Non
linkedServiceName | Service lié de [Stockage Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) pour stocker le journal contenant les lignes ignorées. | Nom d’un service lié de type `AzureStorage` ou `AzureDataLakeStore` faisant référence à l’instance que vous souhaitez utiliser pour stocker le fichier journal. | Non
path | Chemin d’accès du fichier journal contenant les lignes ignorées. | Spécifiez le chemin que vous souhaitez utiliser pour journaliser les données incompatibles. Si vous ne spécifiez pas le chemin d’accès, le service crée un conteneur à votre place. | Non

## <a name="monitor-skipped-rows"></a>Effectuer le monitoring des lignes ignorées
Une fois l’activité de copie exécutée, vous pouvez voir le nombre de lignes ignorées dans la sortie de l’activité de copie :

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Si vous effectuez la configuration de manière à enregistrer les lignes incompatibles, vous pouvez trouver le fichier journal sous le chemin d’accès suivant : `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Les fichiers journaux peuvent être uniquement les fichiers csv. Les données d’origine ignorées seront enregistrées avec la virgule comme délimiteur de colonne si nécessaire. Nous ajoutons deux colonnes supplémentaires « ErrorCode » et « ErrorMessage » en plus des données sources d’origine dans le fichier journal, où vous pouvez voir la cause racine de l’incompatibilité. Les valeurs ErrorCode et ErrorMessage figurent entre guillemets doubles. 

Voici un exemple de contenu de fichier journal :

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Étapes suivantes
Voir les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Performances de l’activité de copie](copy-activity-performance.md)


