---
title: Solutions utilisant l’analytique distribuée à l’échelle mondiale dans Azure Cosmos DB.
description: Découvrez les solutions qui peuvent être créées à l’aide de l’analytique distribuée à l’échelle mondiale dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d50b946e67bbcc171850b71021165356011f58e3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755199"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Solutions utilisant l’analytique distribuée à l’échelle mondiale dans Azure Cosmos DB

Cet article décrit les solutions qui peuvent être créées à l’aide de l’analytique distribuée à l’échelle mondiale dans Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Vente au détail et produits de consommation courante

Vous pouvez utiliser la prise en charge de Spark dans Azure Cosmos DB pour fournir des recommandations et des offres en temps réel. Vous pouvez aider les clients à découvrir les éléments dont ils auront besoin avec des recommandations de personnalisation et de produit en temps réel.

* Vous pouvez utiliser la prise en charge intégrée du Machine Learning fournie par le runtime Apache Spark pour générer des recommandations en temps réel parmi les catalogues de produits.

* Vous pouvez explorer les données de flux de clics, les données d’achats et les données client pour fournir des recommandations ciblées qui procurent une valeur tout au long du cycle de vie.

* Grâce à la fonctionnalité de distribution mondiale d’Azure Cosmos DB, des volumes élevés de données de produits répartis entre les régions peuvent être analysés en quelques millisecondes.

* Vous pouvez obtenir rapidement des informations pour les utilisateurs et les données distribués géographiquement. Vous pouvez améliorer le taux de conversion des promotions en proposant la bonne publicité à l’utilisateur approprié au bon moment.

* Vous pouvez tirer parti de la fonctionnalité de streaming intégrée de Spark pour enrichir les données actives en les combinant à des données client statiques. De cette façon, vous pouvez fournir des publicités plus personnalisées et ciblées en temps réel et dans le contexte de ce que font les clients.

L’illustration suivante montre comment la prise en charge de Spark dans Azure Cosmos DB est utilisée pour optimiser la tarification et les promotions :

![Prise en charge de Spark par Azure Cosmos DB pour optimiser la tarification et les promotions](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


L’illustration suivante montre comment la prise en charge de Spark dans Azure Cosmos DB est utilisée dans le moteur de recommandations en temps réel :

![Prise en charge de Spark par Azure Cosmos DB dans le moteur de recommandations en temps réel](./media/spark-api-introduction/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Fabrication et IoT

La plateforme d’analytique intégrée d’Azure Cosmos DB vous permet d’activer l’analyse en temps réel des données IoT à partir de millions d’appareils à l’échelle mondiale. Vous pouvez implémenter des innovations modernes telles que la prédiction de modèles météorologiques, l’analyse prédictive et les optimisations d’énergie.

* Grâce à Azure Cosmos DB, vous pouvez explorer des données telles que des métriques de ressources en temps réel et des facteurs météorologiques, puis appliquer une analytique de grille intelligente pour optimiser les performances des appareils connectés dans le champ. L’analytique de grille intelligente est essentielle au contrôle des coûts d’exploitation, à l’amélioration de la fiabilité de la grille et à la fourniture de services d’énergie personnalisés aux consommateurs.

L’illustration suivante montre comment la prise en charge de Spark dans Azure Cosmos DB est utilisée pour lire des métriques à partir d’appareils IoT et appliquer l’analytique de grille intelligente :

![Prise en charge de Spark dans Azure Cosmos DB pour lire des métriques à partir d’appareils IoT](./media/spark-api-introduction/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Maintenance prédictive

* La gestion des ressources telles que les compresseurs utilisés dans les petites plateformes de forage comme dans les plateformes très profondes est un effort complexe. Ces ressources sont situées dans le monde entier et génèrent plusieurs pétaoctets de données. Avec Azure Cosmos DB, vous pouvez créer un pipeline de données prédictif de bout en bout qui utilise le streaming Spark pour traiter de grandes quantités de données de télémétrie de capteur, des parties de ressources de magasins et des données de mappages de capteurs.

* Vous pouvez créer et déployer des modèles de Machine Learning pour prédire les défaillances de ressources avant qu’elles ne se produisent, et émettre des ordres de travail de maintenance avant que la défaillance ne se produise.

L’illustration suivante montre comment la prise en charge de Spark dans Azure Cosmos DB est utilisée pour créer un système de maintenance prédictive :

![Prise en charge de Spark dans Azure Cosmos DB pour créer un système de maintenance prédictive](./media/spark-api-introduction/predictive-maintenance-system.png)

L’illustration suivante montre comment la prise en charge de Spark dans Azure Cosmos DB est utilisée pour créer un système de diagnostic de véhicule en temps réel :

![Prise en charge de Spark dans Azure Cosmos DB pour créer un système de diagnostic de véhicule en temps réel](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Jeux

* Avec la prise en charge intégrée de Spark, Azure Cosmos DB vous permet de créer, de mettre à l’échelle et de déployer facilement une analytique avancée et des modèles de Machine Learning en quelques minutes, afin de créer la meilleure expérience de jeu possible.

* Vous pouvez analyser les données relatives aux joueurs, aux achats et aux comportements afin de créer des offres personnalisées pertinentes et atteindre des taux de conversion élevés.

* Avec le Machine Learning Spark, vous pouvez analyser et obtenir des Insights sur les données de télémétrie de jeu. Vous pouvez diagnostiquer et prévenir les lenteurs de chargement et les problèmes de jeu.

L’illustration suivante montre comment la prise en charge de Spark par Azure Cosmos DB est utilisée dans l’analytique de jeu :

![Prise en charge de Spark par Azure Cosmos DB dans l’analytique de jeu](./media/spark-api-introduction/gaming-analytics.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les avantages d’Azure Cosmos DB, voir l’article [Vue d’ensemble](introduction.md).
* [Bien démarrer avec l’API Azure Cosmos DB pour MongoDB](mongodb-introduction.md)
* [Bien démarrer avec l’API Cassandra Azure Cosmos DB](cassandra-introduction.md)
* [Bien démarrer avec l’API Gremlin Azure Cosmos DB](graph-introduction.md)
* [Bien démarrer avec l’API Table Azure Cosmos DB](table-introduction.md)
