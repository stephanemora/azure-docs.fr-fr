---
title: Utilisation des exemples de jeux de données dans le concepteur Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez-en plus sur les exemples de jeux de données dans le concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037307"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Utilisation des exemples de jeux de données dans le concepteur Azure Machine Learning (préversion)

Quand vous créez un pipeline dans le concepteur Azure Machine Learning (préversion), vous disposez par défaut d’un certain nombre d’exemples de jeux de données. Ces exemples de jeux de données sont utilisés par les exemples de pipelines dans la page d’accueil du concepteur. 

Les exemples de jeux de données sont disponibles dans la catégorie **Jeux de données**-**Exemples**. Vous les trouverez dans la palette de modules à gauche du canevas du concepteur. Vous pouvez utiliser l’un de ces jeux de données dans votre propre pipeline en le faisant glisser sur le canevas.

## <a name="datasets"></a>Groupes de données


| Nom du&nbsp;jeu de données&nbsp;&nbsp;&nbsp;&nbsp;| Description du jeu de données |
|-------------|:--------------------|
| Jeu de données Adult Census Income Binary Classification | Sous-ensemble de la base de données Census de 1994, qui recense les adultes de plus de 16 ans en activité avec un index des revenus ajustés supérieur à 100.<br/>**Utilisation**: classifier des personnes en utilisant des données démographiques pour prédire si une personne gagne plus de 50 000 $ par an.<br/> **Recherche associée :** Kohavi, R., Becker, B., (1996). [Dépôt Machine Learning UCI](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science|
|Données sur le prix des véhicules automobiles (brutes)|Informations sur les véhicules automobiles par marque et modèle, incluant le prix, des caractéristiques telles que le nombre de cylindres et de litres au 100, et une note de risque d'assurance.<br/> La note de risque est initialement associée au prix de l’automobile. Elle est ensuite ajustée en fonction du risque réel selon un processus que les actuaires connaissent sous le nom de symbolisation. La valeur +3 indique que le véhicule est à risque et la valeur -3 qu’il est plutôt sûr.<br/>**Utilisation** :</b> prédire le score de risque en fonction des caractéristiques, en utilisant une classification de régression ou multivariée.<br/>**Recherche associée :** </b> Schlimmer, J.C. (1987). [Dépôt Machine Learning UCI](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science. |
| Étiquettes de l'appétence CRM partagées |Étiquettes provenant du KDD Cup 2009 Customer Relationship Prediction Challenge ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|Étiquettes de l'attrition CRM partagées|Étiquettes provenant du KDD Cup 2009 Customer Relationship Prediction Challenge ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|Jeu de données CRM partagé | Ces données proviennent du KDD Cup 2009 Customer Relationship Prediction Challenge ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Le jeu de données contient 50 000 clients de la société de télécoms française Orange. Chaque client possède 230 caractéristiques rendues anonymes, dont 190 sont numériques et 40 sont catégorielles. Elles sont très fragmentées. |
|Étiquettes de vente incitative CRM partagées|Étiquettes provenant du KDD Cup 2009 Customer Relationship Prediction Challenge ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Données relatives aux vols retardés|Données de ponctualité des vols passagers provenant de la collection de données TranStats du ministère Ministère des transports des États-Unis ([à l’heure](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>Le jeu de données couvre la période d’avril à octobre 2013. Avant son chargement dans le concepteur, le jeu de données a été traité comme suit : <br/>- Le jeu de données a été filtré afin de prendre uniquement en compte les 70 aéroports les plus fréquentés aux États-Unis <br/>- Les vols annulés ont été considérés comme ayant été retardés de plus de 15 minutes <br/>- Les vols déviés ont été supprimés <br/>- Les colonnes suivantes ont été sélectionnées : Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled|
|Jeu de données d'UCI pour une carte de crédit allemande|Jeu de données UCI Statlog (carte de crédit allemande) ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))), utilisant le fichier german.data.<br/>Le jeu de données classe les gens, décrits par un ensemble d’attributs, par risque de crédit (faible ou élevé). Chaque exemple représente une personne. Il y a 20 caractéristiques, numériques et catégoriques, ainsi qu’une étiquette binaire (la valeur du risque de crédit). Les entrées avec un risque de crédit élevé portent une étiquette = 2, tandis que les entrées avec un risque de crédit faible portent une étiquette = 1. Le coût d’une erreur de classification d’un risque de crédit faible en risque élevé est de 1 tandis qu’il est de 5 dans le cas inverse (classification d’un risque de crédit élevé en risque faible).|
|Titres de films IMDB|Ce jeu de données contient des informations sur les films évalués dans des tweets sur Twitter : ID de film IMDB, titre du film, genre et année de production. Ce jeu de données contient 17 000 films. Le jeu de données provient du document « S. Dooms, T. De Pessemier et L. Martens. MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013. »|
|Classifications des films|Ce jeu de données est une version étendue du jeu de données Movie Tweetings. Il contient 170 000 évaluations de film, tirées de tweets structurés publiés sur Twitter. Chaque instance correspond à un tweet et constitue un tuple : ID utilisateur, ID IMDB, évaluation, horodatage, nombre de favoris pour ce tweet et nombre de retweets pour ce tweet. Ce jeu de données a été fourni par A. Said, S. Dooms, B. Loni et D. Tikk dans le cadre du Recommender Systems Challenge 2014.|
|Jeu de données météorologiques|Observations météorologiques terrestres effectuées toutes les heures par la NOAA ([données fusionnées, de 201304 à 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Les données météorologiques couvrent les observations effectuées depuis les stations météo des aéroports, entre les mois d’avril et octobre 2013. Avant son chargement dans le concepteur, le jeu de données a été traité comme suit :    <br/> - Les identifiants des stations météo ont été alignés sur les identifiants des aéroports correspondants    <br/> - Les stations météo non associées à l’un des 70 aéroports les plus fréquentés ont été supprimées    <br/> - La colonne Date a été divisée en plusieurs colonnes distinctes indiquant l’année, le mois et le jour    <br/> - Les colonnes suivantes ont été sélectionnées : AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Jeu de données Wikipedia concernant le SP 500|Les données sont extraites de Wikipédia (https://www.wikipedia.org/) ), notamment d’articles sur chaque société cotée au S&P 500 et sont stockées sous forme de données XML.    <br/>Avant son chargement dans le concepteur, le jeu de données a été traité comme suit :    <br/> - Extraction du contenu textuel de chaque société particulière    <br/> - Suppression de la mise en forme wiki    <br/> - Suppression des caractères non alphanumériques    <br/> - Conversion de tout le texte en minuscule    <br/> - Ajout d’autres catégories de sociétés connues    <br/>Notez que pour certaines sociétés, aucun article n’a pu être trouvé, donc le nombre d’enregistrements est inférieur à 500.|

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les principes fondamentaux de l’analytique prédictive et du Machine Learning à l’aide du [Tutoriel : Prédire le prix de voitures avec le concepteur](tutorial-designer-automobile-price-train-score.md)

* Découvrez comment modifier les [exemples de concepteur](samples-designer.md) pour les adapter à vos besoins.
