---
title: 'Tutoriel : Créer des applications Machine Learning avec Microsoft Machine Learning pour Apache Spark (préversion)'
description: Découvrez comment utiliser Microsoft Machine Learning pour Apache Spark afin de créer des applications Machine Learning dans Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruxu
ms.author: ruxu
ms.openlocfilehash: a3899b83133b3f951547fae0b11c044bfa85a5fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589597"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Tutoriel : Créer des applications Machine Learning avec Microsoft Machine Learning pour Apache Spark (préversion)

Dans cet article, vous allez apprendre à utiliser Microsoft Machine Learning pour Apache Spark ([MMLSpark](https://github.com/Azure/mmlspark)) afin de créer des applications Machine Learning. MMLSpark étend la solution de Machine Learning distribuée d’Apache Spark en ajoutant de nombreux outils de Deep Learning et de science des données, comme [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) et plus encore.  MMLSpark vous permet de créer des modèles analytiques et prédictifs puissants et hautement scalables à partir de diverses sources de données Spark.
Synapse Spark fournit des bibliothèques MMLSpark intégrées, notamment :

- [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit) : services de bibliothèque pour le Machine Learning permettant d’activer l’analytique de texte comme l’analyse des sentiments dans les tweets.
- [Cognitive Services sur Spark](../../cognitive-services/big-data/cognitive-services-for-big-data.md) : pour combiner la fonctionnalité d’Azure Cognitive Services dans les pipelines SparkML afin de dériver la conception de la solution pour les services de modélisation des données cognitifs, tels que la détection d’anomalies.
- [LightBGM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) : modèle Machine Learning permettant d’activer l’entraînement du modèle pour l’analyse prédictive telle que la détection d’ID de visage.
- KNN conditionnel : modèles KNN scalables avec des requêtes conditionnelles.
- [HTTP sur Spark](https://github.com/Azure/mmlspark/blob/master/docs/http.md) : permet l’orchestration des microservices distribués dans l’intégration de l’accessibilité basée sur le protocole Spark et HTTP.

Ce tutoriel présente des exemples utilisant Azure Cognitive Services dans MMLSpark pour les fonctionnalités suivantes : 

- Analyse de texte : obtenir le sentiment (ou l’humeur) d’un ensemble de phrases.
- Vision par ordinateur : obtenir les étiquettes (descriptions en un mot) associées à un ensemble d’images.
- Recherche d’images Bing : rechercher sur le web des images associées à une demande en langage naturel.
- Détecteur d’anomalies : détecter les anomalies dans des données de séries chronologiques.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis 

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
- Pool Spark dans votre espace de travail Azure Synapse Analytics. Pour plus d’informations, consultez [Créer un pool Spark dans Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Avoir effectué les étapes de pré-configuration décrites dans le tutoriel [Configurer Cognitive Services dans Azure Synapse](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>Bien démarrer
Pour commencer, importez mmlspark et configurez les clés de service.

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 


cognitive_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", anomaly_key)

```

## <a name="text-analytics-sample"></a>Exemple d’analytique de texte

Le service [Analyse de texte](../../cognitive-services/text-analytics/index.yml) fournit plusieurs algorithmes permettant d’extraire des insights intelligents à partir d’un texte. Par exemple, nous pouvons trouver le sentiment d’un texte d’entrée donné. Le service retourne un score compris entre 0.0 et 1.0, où un score faible indique un sentiment négatif et où un score élevé indique un sentiment positif. Cet exemple utilise trois phrases simples et retourne le sentiment pour chacune d’elles.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Résultats attendus

|texte | sentiment|
|--|--|
| I am frustrated by this rush hour traffic ! (Cette circulation aux heures de pointe m’agace !) | négatif |
| this is a dog (c’est un chien) | neutre |
| I am so happy today, its sunny! (Je suis trop content aujourd’hui, il fait beau !) | positif |

## <a name="computer-vision-sample"></a>Exemple API Vision par ordinateur
Le service [Vision par ordinateur](../../cognitive-services/computer-vision/index.yml) analyse des images pour identifier une structure comme des visages, des objets et des descriptions en langage naturel. Dans cet exemple, nous étiquetons l’image suivante. Les étiquettes sont des descriptions en un mot des choses figurant sur l’image, comme des objets, des personnes, un paysage et des actions reconnaissables.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Résultats attendus

|image | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [skate, personne, homme, extérieur, monter, sport, skateboard, jeune, planche, maillot, air, parc, garçon, côté, saut, rampe, figure, action, vol] |

## <a name="bing-image-search-sample"></a>Exemple de recherche d’images Bing
La [Recherche d’images Bing](../../cognitive-services/bing-image-search/overview.md) explore le web pour récupérer des images associées à une demande en langage naturel d’un utilisateur. Dans cet exemple, nous utilisons une demande texte qui recherche des images avec des guillemets. Elle retourne une liste d’URL d’images qui contiennent des photos liées à notre demande.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Résultats attendus

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Exemple de détecteur d’anomalies

Le [Détecteur d’anomalies](../../cognitive-services/anomaly-detector/index.yml) est idéal pour détecter les irrégularités dans vos données de séries chronologiques. Dans cet exemple, nous utilisons le service pour rechercher des anomalies dans l’ensemble de la série chronologique.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Résultats attendus

|timestamp | value | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826.0|false|
|1972-02-01T00:00:00Z|799.0|false|
|1972-03-01T00:00:00Z|890.0|false|
|1972-04-01T00:00:00Z|900.0|false|
|1972-05-01T00:00:00Z|766.0|false|
|1972-06-01T00:00:00Z|805.0|false|
|1972-07-01T00:00:00Z|821.0|false|
|1972-08-01T00:00:00Z|20000.0|true|
|1972-09-01T00:00:00Z|883.0|false|
|1972-10-01T00:00:00Z|898.0|false|
|1972-11-01T00:00:00Z|957.0|false|
|1972-12-01T00:00:00Z|924.0|false|
|1973-01-01T00:00:00Z|881.0|false|
|1973-02-01T00:00:00Z|837.0|false|
|1973-03-01T00:00:00Z|9000.0|true|

## <a name="next-steps"></a>Étapes suivantes

* [Consultez les exemples de notebooks Synapse](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [Dépôt GitHub MMLSpark](https://github.com/Azure/mmlspark)