---
title: Exemples de notebooks Jupyter utilisant un jeu de données ouvert NOAA
titleSuffix: Azure Open Datasets
description: Utilisez les exemples de notebooks Jupyter pour Azure Open Datasets afin de savoir comment charger des jeux de données ouverts et les utiliser pour enrichir les données de démonstration. Les techniques utilisent Spark et Pandas à des fins de traitement des données.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: b62a2690e5879e45a14d0b06a38e8c5171dda14e
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442329"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Exemples de notebooks Jupyter montrant comment enrichir les données avec Open Datasets 
Les exemples de notebooks Jupyter pour Azure Open Datasets montrent comment charger des jeux de données ouverts et les utiliser pour enrichir les données de démonstration. Les techniques utilisent Apache Spark et Pandas à des fins de traitement des données.

>[!IMPORTANT]
>Dans un environnement autre que Spark, Azure Open Datasets permet de télécharger un seul mois de données à la fois, avec certaines classes, afin d’éviter l'erreur MemoryError liée aux jeux de données volumineux.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Charger des données ISD (Integrated Surface Database) NOAA 
|Bloc-notes        | Description                                    |
|----------------|------------------------------------------------|
|[Charger un mois récent de données météorologiques dans un dataframe Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Découvrez comment charger des données météorologiques historiques dans votre dataframe Pandas favori. |
|[Charger un mois récent de données météorologiques dans un dataframe Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Découvrez comment charger des données météorologiques historiques dans votre dataframe Spark favori.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Joindre des données de démonstration avec des données ISD NOAA 
|Bloc-notes        | Description                                    |
|----------------|------------------------------------------------|
|[Joindre des données de démonstration avec des données météorologiques - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Joignez un jeu de données de démonstration d'un mois d'emplacements de capteur avec relevés météorologiques dans un dataframe Pandas.  |
|[Joindre des données de démonstration avec des données météorologiques - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Joignez un jeu de données de démonstration d'emplacements de capteur avec relevés météorologiques dans un dataframe Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Joindre des données de taxi de New York avec des données ISD NOAA 
|Bloc-notes        | Description                                    |
|----------------|------------------------------------------------|
|[Données de trajet des taxis enrichies avec des données météorologiques - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Chargez les données de trajet des taxis de New York (sur un mois) et enrichissez-les avec des données météorologiques dans un dataframe Pandas. Cet exemple remplace la méthode `get_pandas_limit` et équilibre les performances de chargement des données avec la quantité de données.|
|[Données de trajet des taxis enrichies avec des données météorologiques - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Chargez les données de trajet des taxis de New York et enrichissez-les avec des données météorologiques dans un dataframe Spark.  |

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Modélisation de régression avec Machine Learning automatisé et jeu de données ouvert](tutorial-opendatasets-automl.md)
* [Kit de développement logiciel (SDK) Python pour Open Datasets](https://aka.ms/open-datasets-api)
* [Catalogue Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
