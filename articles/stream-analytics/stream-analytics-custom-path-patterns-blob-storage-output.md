---
title: Partitionnement personnalisé de sortie BLOB dans Azure Stream Analytics
description: Cet article décrit les fonctionnalités de modèles de chemin DateTime personnalisés et de champs ou attributs personnalisés pour la sortie de stockage d’objets blob à partir de travaux Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/30/2021
ms.custom: seodec18
ms.openlocfilehash: 59226a105df2a05e693c7d83f2488a43143914b6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525579"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Partitionnement personnalisé de sortie BLOB dans Azure Stream Analytics

Azure Stream Analytics prend en charge le partitionnement personnalisé de sortie BLOB avec des champs ou attributs personnalisés et des modèles de chemin DateTime personnalisés. 

## <a name="custom-field-or-attributes"></a>Champs ou attributs personnalisé

Des champs ou attributs d’entrée personnalisés améliorent en aval le traitement de données et les flux de travail de génération de rapports en permettant de mieux contrôler la sortie.

### <a name="partition-key-options"></a>Options de clé de partition

La clé de partition, ou nom de colonne, utilisée pour partitionner les données d’entrée peut contenir n’importe quel caractère accepté pour les [noms d’objets blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata). Il n’est pas possible d’utiliser des champs imbriqués comme clé de partition, sauf s’ils sont utilisés conjointement à des alias, mais vous pouvez utiliser certains caractères pour créer une hiérarchie de fichiers. Par exemple, vous pouvez utiliser la requête suivante pour créer une colonne qui combine des données de deux autres colonnes afin de créer une clé de partition unique.

```sql
SELECT name, id, CONCAT(name, "/", id) AS nameid
```

La clé de partition doit être de type NVARCHAR (MAX), BIGINT, FLOAT ou BIT (niveau de compatibilité 1.2 ou supérieur). Les types DateTime, Array et Records ne sont pas pris en charge, mais peuvent être utilisés comme clés de partition s’ils sont convertis en chaînes. Pour plus d’informations, consultez [Types de données Azure Stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics).

### <a name="example"></a>Exemple

Supposons qu’un travail prenne des données d’entrée de sessions utilisateur actives connectées à un service externe de jeu vidéo où les données ingérées contient une colonne **client_id** pour identifier les sessions. Pour partitionner les données par **client_id**, définissez le champ Modèle de chemin d’accès d’objet blob de façon à inclure un jeton de partition **{client_id}** dans les propriétés de sortie BLOB lors de la création d’un travail. Les données avec différentes valeurs **client_id** transitant par le travail Stream Analytics, les données de sortie sont enregistrées dans des dossiers distincts sur la base d’une seule valeur **client_id** par dossier.

![Modèle de chemin d’accès avec id client](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

De même, si l’entrée du travail était constituée de données provenant de millions de capteurs où chaque capteur avait un **sensor_id**, le modèle de chemin d’accès serait **{sensor_id}** pour partitionner les données de chaque capteur dans des dossiers distincts.  


En utilisant l’API REST, la section de sortie d’un fichier JSON utilisé pour cette requête peut ressembler à ce qui suit :  

![Sortie de l’API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Lorsque l’exécution du travail commence, le conteneur *clients* peut ressembler à ce qui suit :  

![Conteneur clients](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Chaque dossier peut contenir plusieurs objets blob contenant chacun un ou plusieurs enregistrements. Dans l’exemple ci-dessus, il y a un seul blob dans un dossier étiqueté « 06000000 » avec le contenu suivant :

![Contenu d’objet blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Notez que chaque enregistrement dans l’objet blob comporte une colonne **client_id** correspondant au nom de dossier du fait que la colonne utilisée pour partitionner la sortie dans le chemin d’accès de sortie était **client_id**.

### <a name="limitations"></a>Limites

1. Une seule clé de partition personnalisée est autorisée dans la propriété de sortie d’objet blob Modèle de chemin d’accès. Tous les modèles de chemin d’accès suivants sont valides :

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData}

2. Si les clients souhaitent utiliser plusieurs champs d’entrée, ils peuvent créer une clé composite dans la requête pour une partition de chemin personnalisée dans la sortie d’objet blob à l’aide de **CONCAT**. Par exemple : **select concat (col1, col2) as compositeColumn into blobOutput from input**. Ils peuvent ensuite spécifier **compositeColumn** comme chemin d’accès personnalisé dans le stockage d’objets blob.
   
3. Les clés de partition ignorant la casse, les clés « John » et « john » sont équivalentes. En outre, des expressions ne peuvent pas être utilisées comme clés de partition. Par exemple, **{columnA + columnB}** ne fonctionne pas.  

4. Quand un flux d’entrée se compose d’enregistrements avec une cardinalité de clé de partition inférieure à 8 000, les enregistrements sont ajoutés à des objets blob existants et ne créent de nouveaux objets blob que lorsque cela est nécessaire. Si la cardinalité est supérieure à 8 000, il n’est nullement garanti qu’une écriture sera effectuée dans des objets blob existants, et que de nouveaux objets blob ne seront pas créés pour un nombre arbitraire d’enregistrements avec la même clé de partition.

5. Si la sortie d’objet blob est [configurée comme non modifiable](../storage/blobs/immutable-storage-overview.md), Stream Analytics crée un objet blob chaque fois que des données sont envoyées.

## <a name="custom-datetime-path-patterns"></a>Modèles de chemin DateTime personnalisés

Avec les modèles de chemin DateTime personnalisés, vous pouvez spécifier un format de sortie conforme aux conventions Hive Streaming, ce qui permet à Azure Stream Analytics d’envoyer des données à Azure HDInsight et à Azure Databricks pour un traitement en aval. Les modèles de chemin DateTime personnalisés s’implémentent facilement en ajoutant le mot clé `datetime` dans le champ Préfixe de chemin de votre sortie Blob, ainsi que le spécificateur de format. Par exemple : `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Jetons pris en charge

Les jetons de spécificateur de format suivants peuvent être utilisés individuellement ou en combinaison pour créer des formats DateTime personnalisés :

|Spécificateur de format   |Description   |Résultats pour l’exemple date/heure 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Année sous la forme d’un nombre à quatre chiffres|2018|
|{datetime:MM}|Mois de 01 à 12|01|
|{datetime:M}|Mois de 1 à 12|1|
|{datetime:dd}|Jour de 01 à 31|02|
|{datetime:d}|Jour de 1 à 31|2|
|{datetime:HH}|Heure au format 24 heures, de 00 à 23|10|
|{datetime:mm}|Minutes de 00 à 60|06|
|{datetime:m}|Minutes de 0 à 60|6|
|{datetime:ss}|Secondes de 00 à 60|08|

Si vous ne souhaitez pas utiliser des modèles DateTime personnalisés, vous pouvez ajouter le jeton {date} et/ou {time} au préfixe de chemin. Vous obtenez ainsi une liste déroulante contenant les formats DateTime prédéfinis.

![Anciens formats DateTime de Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Extensibilité et restrictions

Vous pouvez utiliser autant de jetons `{datetime:<specifier>}` que vous le souhaitez dans le modèle de chemin, sans toutefois dépasser la limite de caractères du préfixe de chemin. Vous ne pouvez pas créer d’autres combinaisons de spécificateurs de format dans un jeton que celles déjà définies dans les listes déroulantes de date et d’heure. 

Exemple de partition de chemin de `logs/MM/dd` :

|Expression valide   |Expression non valide   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Vous pouvez utiliser le même spécificateur de format plusieurs fois dans le chemin de préfixe. Le jeton doit alors être répété à chaque occurrence.

### <a name="hive-streaming-conventions"></a>Conventions Hive Streaming

Les modèles de chemin personnalisés pour le stockage Blob peuvent respecter la convention Hive Streaming, selon laquelle les noms de dossier doivent contenir l’étiquette `column=`.

Par exemple : `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Une sortie personnalisée vous évite d’avoir à modifier les tables et à ajouter manuellement des partitions aux données de port entre Azure Stream Analytics et Hive. À la place, un grand nombre de dossiers peuvent être ajoutés automatiquement de la façon suivante :

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exemple

Créez un compte de stockage, un groupe de ressources, un travail Stream Analytics et une source d’entrée conformément au guide de démarrage rapide [du Portail Azure d’Azure Stream Analytics](stream-analytics-quick-create-portal.md). Utilisez les mêmes exemples de données que dans le guide de démarrage rapide (également disponibles sur [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)).

Créez un récepteur de sortie Blob avec la configuration suivante :

![Créer un récepteur de sortie Blob Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Le modèle de chemin complet est le suivant :


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Quand vous démarrez le travail, une structure de dossiers basée sur le modèle de chemin est automatiquement créée dans votre conteneur d’objets blob. Vous pouvez descendre dans la hiérarchie jusqu’au jour souhaité.

![Sortie Blob Stream Analytics avec le modèle de chemin personnalisé](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les sorties d’Azure Stream Analytics](stream-analytics-define-outputs.md)