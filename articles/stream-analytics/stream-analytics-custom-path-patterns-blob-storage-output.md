---
title: Modèles de chemin DateTime pour la sortie du stockage d’objets blob d’Azure Stream Analytics (préversion)
description: Cet article décrit la fonctionnalité de modèles de chemin DateTime personnalisés pour la sortie de stockage d’objets blob à partir des tâches Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ba386539c3f3c6740b843575bbccd4b028b8a5a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090776"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Modèles de chemin DateTime personnalisés pour les sorties du stockage Blob d’Azure Stream Analytics (préversion)

Azure Stream Analytics prend en charge l’utilisation de spécificateurs de format date/heure personnalisés dans le chemin de fichier pour les sorties du stockage Blob. Avec les modèles de chemin DateTime personnalisés, vous pouvez spécifier un format de sortie conforme aux conventions Hive Streaming, ce qui permet à Azure Stream Analytics d’envoyer des données à Azure HDInsight et à Azure Databricks pour un traitement en aval. Les modèles de chemin DateTime personnalisés s’implémentent facilement en ajoutant le mot clé `datetime` dans le champ Préfixe de chemin de votre sortie Blob, ainsi que le spécificateur de format. Par exemple : `{datetime:yyyy}`.

Utilisez ce lien d’accès au [portail Azure](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) pour activer l’indicateur de fonctionnalité qui autorise les modèles de chemin DateTime personnalisés pour les sorties du stockage Blob (préversion). Cette fonctionnalité sera bientôt activée dans le portail principal.

## <a name="supported-tokens"></a>Jetons pris en charge

Les jetons de spécificateur de format suivants peuvent être utilisés individuellement ou en combinaison pour créer des formats DateTime personnalisés :

|Spécificateur de format   |Description   |Résultats pour l’exemple date/heure 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Année sous la forme d’un nombre à quatre chiffres|2018|
|{datetime:MM}|Mois de 01 à 12|01|
|{datetime:M}|Mois de 1 à 12|1|
|{datetime:dd}|Jour de 01 à 31|02|
|{datetime:d}|Jour de 1 à 31|2|
|{datetime:HH}|Heure au format 24 heures, de 00 à 23|10|
|{datetime:mm}|Minutes de 00 à 24|06|
|{datetime:m}|Minutes de 0 à 24|6.|
|{datetime:ss}|Secondes de 00 à 60|08|

Si vous ne souhaitez pas utiliser des modèles DateTime personnalisés, vous pouvez ajouter le jeton {date} et/ou {time} au préfixe de chemin. Vous obtenez ainsi une liste déroulante contenant les formats DateTime prédéfinis.

![Anciens formats DateTime de Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>Extensibilité et restrictions

Vous pouvez utiliser autant de jetons `{datetime:<specifier>}` que vous le souhaitez dans le modèle de chemin, sans toutefois dépasser la limite de caractères du préfixe de chemin. Vous ne pouvez pas créer d’autres combinaisons de spécificateurs de format dans un jeton que celles déjà définies dans les listes déroulantes de date et d’heure. 

Exemple de partition de chemin de `logs/MM/dd` :

|Expression valide   |Expression non valide   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Vous pouvez utiliser le même spécificateur de format plusieurs fois dans le chemin de préfixe. Le jeton doit alors être répété à chaque occurrence.

## <a name="hive-streaming-conventions"></a>Conventions Hive Streaming

Les modèles de chemin personnalisés pour le stockage Blob peuvent respecter la convention Hive Streaming, selon laquelle les noms de dossier doivent contenir l’étiquette `column=`.

Par exemple : `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Une sortie personnalisée vous évite d’avoir à modifier les tables et à ajouter manuellement des partitions aux données de port entre Azure Stream Analytics et Hive. À la place, un grand nombre de dossiers peuvent être ajoutés automatiquement de la façon suivante :

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exemples

Créez un compte de stockage, un groupe de ressources, un travail Stream Analytics et une source d’entrée en vous aidant du guide de démarrage rapide [Créer un travail Stream Analytics à l’aide du portail Azure](stream-analytics-quick-create-portal.md). Utilisez les mêmes exemples de données que dans le guide de démarrage rapide (également disponibles sur [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)).

Créez un récepteur de sortie Blob avec la configuration suivante :

![Créer un récepteur de sortie Blob Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Le modèle de chemin complet est le suivant :


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Quand vous démarrez le travail, une structure de dossiers basée sur le modèle de chemin est automatiquement créée dans votre conteneur d’objets blob. Vous pouvez descendre dans la hiérarchie jusqu’au jour souhaité.

![Sortie Blob Stream Analytics avec le modèle de chemin personnalisé](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre les sorties d’Azure Stream Analytics](stream-analytics-define-outputs.md)
