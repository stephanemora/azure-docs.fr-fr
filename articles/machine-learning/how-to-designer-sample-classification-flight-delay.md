---
title: 'Concepteur : Prédire un exemple de retard déployé en mode Flighting'
titleSuffix: Azure Machine Learning
description: Générez un classifieur et utilisez un code R personnalisé pour prédire les retards déployés en mode Flighting avec le concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: d459350572d68cc5dcbbfd56933483404b94f918
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963274"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Générer un classifieur et utiliser R pour prédire des retards déployés en mode Flighting avec le concepteur Azure Machine Learning

**Exemple 6 du concepteur (préversion)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ce pipeline utilise des données historiques de vols et météorologiques pour déterminer si un vol prévu risque d'être retardé de plus de 15 minutes. Ce problème peut être abordé en tant que problème de classification, prédisant deux classes : retardé ou à l'heure.

Voici le graphique final du pipeline pour cet échantillon :

[![Graphique du pipeline](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Cliquez sur l’exemple 6 pour l’ouvrir.

## <a name="get-the-data"></a>Obtenir les données

Cet échantillon utilise le jeu de données **Données relatives aux vols retardés**. Il fait partie de la collection de données TranStats du ministère des transports des États-Unis. Le jeu de données contient les informations de retard de vol d'avril à octobre 2013. Le jeu de données a été pré-traité comme suit :

* Elles ont été filtrées pour inclure les 70 aéroports les plus fréquentés des États-Unis.
* Pour les vols annulés et ré étiquetés comme ayant un retard de plus de 15 minutes.
* Les vols déviés ont été supprimés.
* 14 colonnes ont été sélectionnées.

Pour compléter les données de vol, le **jeu de données météorologiques** est utilisé. Les données météorologiques contiennent les observations météorologiques terrestres de la NOAA et représentent les observations issues des stations météo des aéroports, couvrant la même période comme le jeu de données des vols. Il a été pré-traité comme suit :

* Les identifiants des stations météo ont été alignés sur les identifiants des aéroports correspondants.
* Les stations météo non associées à un des 70 aéroports sélectionnés ont été supprimées.
* La colonne Date a été fractionnée en plusieurs colonnes distinctes : Année, Mois et Jour.
* 26 colonnes sélectionnées.

## <a name="pre-process-the-data"></a>Prétraitement des données

Pour pouvoir être analysé, un jeu de données nécessite généralement un traitement préalable.

![traitement des données](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>Données de vol

Les colonnes **Carrier**, **OriginAirportID** et **DestAirportID** sont enregistrés en tant que valeurs entières. Il s'agit cependant d'attributs catégoriels. Utilisez le module **Modifier des métadonnées** pour les convertir en valeurs catégorielles.

![modifier les métadonnées](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

Utilisez ensuite **Sélection des colonnes** dans le module de jeu de données pour exclure les colonnes de jeu de données susceptibles de poser problème : **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled**, **Year**. 

Pour joindre les enregistrements de vol aux enregistrements météo toutes les heures, utilisez l’heure de départ prévue en tant que clé de jointure. Pour effectuer la jointure, la colonne CSRDepTime doit être arrondie à l’heure la plus proche, ce qui se fait via le module **Exécuter un script R**. 

### <a name="weather-data"></a>Données météorologiques

Les colonnes présentant une grande quantité de valeurs manquantes sont exclues à l'aide du module **Colonnes de projet**. Ces colonnes comprennent toutes les colonnes à valeur de chaîne : **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure** et **StationPressure**.

Le module **Nettoyage des données manquantes** est ensuite appliqué aux colonnes restantes pour supprimer les lignes présentant des données manquantes.

Les heures d’observation météo sont arrondies à l’heure complète la plus proche. Les heures de vol prévues et les heures d'observation météo sont arrondies dans des directions opposées pour veiller à ce que le modèle utilise les données météo avant les heures de vol. 

Les données météo étant indiquées en heure locale, les différences de fuseau horaire sont prises en compte en soustrayant les colonnes de fuseau horaire de l'heure de départ prévue et de l'heure d'observation météo. Ces opérations se font à l’aide du module **Exécuter un script R**.

### <a name="joining-datasets"></a>Joindre des jeux de données

Les enregistrements de vol sont joints aux données météo à l'aide de la provenance du vol (**OriginAirportID**) à l’aide du module **Joindre des données**.

 ![joindre des enregistrements de vol et des données météo par provenance](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


Les enregistrements de vol sont joints aux données météo à l’aide de la destination du vol (**DestAirportID**).

 ![Joindre les enregistrements de vol et les données météo par destination](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Préparation de la formation et des exemples de tests

Le module **Fractionner les données** fractionne les données en enregistrements entre avril et septembre pour la formation, et en octobre pour les tests.

 ![Fractionner les données de formation et de test](./media/how-to-designer-sample-classification-flight-delay/split.png)

Les colonnes Année, Mois et Fuseau horaire sont supprimées du jeu de données de formation à l'aide du module Sélectionner des colonnes.

## <a name="define-features"></a>Définition des fonctionnalités

Dans Machine Learning, les fonctionnalités sont des propriétés individuelles mesurables d’un élément qui vous intéresse. La recherche d'un ensemble fort de fonctionnalités requiert expérimentation et connaissance du domaine. Certaines fonctionnalités sont mieux adaptées à la prévision que d’autres. En outre, certaines fonctionnalités peuvent présenter une forte corrélation avec d’autres fonctionnalités, et n'ajoutent pas de nouvelles informations au modèle. Ces fonctionnalités peuvent être supprimées.

Pour générer un modèle, vous pouvez utiliser toutes les fonctionnalités disponibles, ou sélectionner un sous-ensemble des fonctionnalités.

## <a name="choose-and-apply-a-learning-algorithm"></a>Sélection et application d’un algorithme d’apprentissage

Créez un modèle à l’aide du module **Régression logistique à deux classes** et effectuez son apprentissage sur le jeu de données d’apprentissage. 

Le résultat du module **Effectuer l'apprentissage d'un modèle** est un modèle de classification formé qui permet de noter de nouveaux exemples pour effectuer des prédictions. Utilisez le jeu de test pour générer des scores à partir des modèles formés. Utilisez ensuite le module **Évaluer un modèle** pour analyser et comparer la qualité des modèles.
Une fois l'expérience exécutée, vous pouvez afficher la sortie du module **Modèle de score** en cliquant sur le port de sortie et en sélectionnant **Visualiser**. La sortie inclut les étiquettes notées et les probabilités des étiquettes.

Enfin, pour tester la qualité des résultats, ajoutez le module **Évaluer le modèle** au canevas du pipeline, puis connectez le port d’entrée de gauche à la sortie du module Modèle de score. Exécutez le pipeline et affichez la sortie du module **Évaluer le modèle** en cliquant sur le port de sortie et en sélectionnant **Visualiser**.

## <a name="evaluate"></a>Évaluer
Le modèle de régression logistique présente une valeur AUC de 0,631 sur le jeu de test.

 ![evaluate](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres échantillons disponibles pour le concepteur :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification avec sélection des caractéristiques : Prédiction des revenus](how-to-designer-sample-classification-predict-income.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-designer-sample-classification-churn.md)
- [Exemple 7 - Classification de texte : Jeu de données Wikipédia SP 500](how-to-designer-sample-text-classification.md)