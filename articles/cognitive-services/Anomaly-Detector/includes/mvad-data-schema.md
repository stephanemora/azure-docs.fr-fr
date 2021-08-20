---
title: Schéma de données MVAD
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 3be9d5d50d34eee35de38b7b28dc1d42217ace24
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292643"
---
### <a name="input-data-schema"></a>Schéma de données d’entrée

MVAD détecte les anomalies à partir d’un groupe de métriques. Nous appelons chaque métrique **variable** ou série chronologique.

* Vous pouvez télécharger l’exemple de fichier de données de Microsoft pour vérifier le schéma accepté à partir de : [https://aka.ms/AnomalyDetector/MVADSampleData](https://aka.ms/AnomalyDetector/MVADSampleData)
* Chaque variable doit avoir deux champs, et seulement deux, `timestamp` et `value`. Elle doit être stockée dans un fichier de valeurs séparées par des virgules (CSV).
* Les noms des colonnes du fichier CSV doivent être exactement `timestamp` et `value`, et sont sensibles à la casse.
* Les valeurs `timestamp` doivent être conformes à la norme ISO 8601. La colonne `value` peut contenir des entiers ou des nombres décimaux avec n’importe quel nombre de décimales.
    Voici un bon exemple du contenu d’un fichier CSV :

    |timestamp | value|
    |-------|-------|
    |2019-04-01T00:00:00Z| 5|
    |2019-04-01T00:01:00Z| 3.6|
    |2019-04-01T00:02:00Z| 4|
    |...| ...|

    > [!NOTE]
    > Si vos horodateurs contiennent des heures, des minutes et/ou des secondes, assurez-vous qu’ils sont correctement arrondis à la valeur supérieure avant d’appeler les API.
    >
    > Par exemple, si votre fréquence de données est censée être un point de données toutes les 30 secondes, mais que vous voyez des horodateurs comme « 12:00:01 » et « 12:00:28 », il s’agit d’un signal fort que vous devez effectuer le prétraitement des horodateurs pour obtenir de nouvelles valeurs comme « 12:00:00 » et « 12:00:30 ».
    >
    > Pour plus d’informations, reportez-vous à la [section « Arrondir les horodateurs à la valeur supérieure »](../concepts/best-practices-multivariate.md#timestamp-round-up) dans le document sur les meilleures pratiques.
* Le nom du fichier CSV est utilisé comme nom de la variable et doit être unique. Par exemple, « température.csv » et « humidité.csv ».
* Les variables pour l’apprentissage et les variables pour l’inférence doivent être cohérentes. Par exemple, si vous utilisez `series_1`, `series_2`, `series_3`, `series_4` et `series_5` pour l’apprentissage, vous devez fournir exactement les mêmes variables pour l’inférence.
* Les fichiers CSV doivent être compressés dans un fichier zip et chargés sur un conteneur d’objets blob Azure. Le fichier zip peut porter le nom de votre choix.

#### <a name="folder-structure"></a>Structure de dossiers

Une erreur courante dans la préparation des données est l’ajout de dossiers supplémentaires dans le fichier zip. Imaginons par exemple que le nom du fichier zip est `series.zip`. Après avoir décompressé les fichiers dans un nouveau dossier `./series`, le chemin **correct** d’accès aux fichiers CSV est `./series/series_1.csv`. Un chemin **incorrect** pourrait être `./series/foo/bar/series_1.csv`.

Exemple correct de l’arborescence de répertoires après avoir décompressé le fichier zip dans Windows

```bash
.
└── series
    ├── series_1.csv
    ├── series_2.csv
    ├── series_3.csv
    ├── series_4.csv
    └── series_5.csv
```

Exemple incorrect de l’arborescence de répertoires après avoir décompressé le fichier zip dans Windows

```bash
.
└── series
    └── series
        ├── series_1.csv
        ├── series_2.csv
        ├── series_3.csv
        ├── series_4.csv
        └── series_5.csv
```