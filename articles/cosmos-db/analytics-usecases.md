---
title: Cas d'utilisation pour l’analytique intégrée à Azure Cosmos DB.
description: Découvrez comment utiliser l’analytique intégrée à Azure Cosmos DB dans différents cas d’utilisation.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 04/20/2020
ms.reviewer: sngun
ms.openlocfilehash: 9b1c3435222ada52c01f21c2c242dc886f566a81
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192848"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Cas d’utilisation de l’analytique intégrée à Azure Cosmos DB

Les cas d'utilisation suivants peuvent être obtenus à l'aide de l'analyse intégrée à Apache Spark dans Azure Cosmos DB:

## <a name="htap-scenarios"></a>Scénarios HTAP

L'analytique intégrée à Azure Cosmos DB permet ce qui suit :

* Détecter la fraude lors du traitement transactionnel.
* Proposer des recommandations aux acheteurs qui consultent un magasin de commerce électronique.
* Alerter les opérateurs en cas de défaillance imminente d’un équipement de fabrication essentiel critique.
* Créer des insights rapides et exploitables en intégrant l’analytique en temps réel directement aux données opérationnelles.

Azure Cosmos DB prend en charge les scénarios de traitement transactionnel et analytique hybride (HTAP) à l’aide d'une intégration native d'Apache Spark. Azure Cosmos DB supprime la séparation opérationnelle et analytique fournie avec les systèmes traditionnels.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Lac de données distribuées à l'échelle mondiale sans ETL

Grâce à l'intégration native d'Apache Spark, Azure Cosmos DB offre un moyen rapide, simple et évolutif pour créer un lac de données distribuées à l’échelle mondiale fournissant une image système unique. Moyennant des données multimodèles distribuées à l’échelle mondiale, il n'est plus nécessaire d’investir dans de coûteux pipelines ETL et mises à l'échelle à la demande, ce qui révolutionne la manière dont les équipes en charge des données analysent leurs jeux de données.

## <a name="time-series-analytics-over-historic-data"></a>Analyse de série chronologique et données historiques

Vous serez peut-être amené à devoir répondre à des questions portant sur les données, comme l'exécution d'événements à des moments précis. Par exemple, pour obtenir le nombre d’états d’activité CRM à une certaine date. Si vous avez exécuté le rapport il y a une semaine, le nombre d’états correspond aux états de chaque activité à un moment donné. En exécutant le même rapport aujourd’hui, vous obtiendrez le nombre d’activités dont les états correspondent au jour, ce qui peut avoir changé depuis la semaine précédente, au fil de leur cycle de vie, de l’ouverture à la fermeture. Dès lors, vous devez reporter sur l'instantané chaque étape du cycle de vie du cas.

Avec Azure Cosmos DB, les utilisateurs ont la possibilité d’implémenter le concept de voyage dans le temps, d'interroger et d’exécuter des analyses sur les données de manière rétrospective et d'en savoir plus sur les données à un moment précis de l’historique. Ainsi, les utilisateurs peuvent facilement afficher les vues actuelles et historiques des données et y exécuter des analyses.

## <a name="globally-distributed-machine-learning-and-ai"></a>Machine Learning et IA distribués à l'échelle mondiale

Alors que les entreprises sont confrontées à des volumes de données toujours croissants et variés en termes de types et de formats, la possibilité d'obtenir des insights plus détaillés et plus précis est quasiment impossible à l'échelle du pétaoctet dans le monde entier. Grâce à l'intégration native d'Apache Spark, Azure Cosmos DB fournit une plateforme d’analyse distribuée à l’échelle mondiale qui offre une bibliothèque complète d’algorithmes de Machine Learning. Vous pouvez utiliser des notebooks Jupyter interactifs pour créer et effectuer l'apprentissage de modèles, ainsi que des fonctionnalités de gestion de cluster. Ces fonctionnalités vous permettent d’approvisionner des clusters Spark hautement précis et auto-élastiques à la demande.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Deep Learning sur les données multimodèles distribuées à l'échelle mondiale

Deep Learning est le moyen idéal pour fournir des solutions d'analyse prédictive Big Data au fur et à mesure que le volume et la complexité des données augmentent. Grâce à Deep Learning, les entreprises peuvent exploiter le potentiel de données non structurées et semi-structurées pour proposer des cas d'utilisation tirant parti de technologies telles que l'IA, le traitement en langage naturel, etc.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Création de rapports (intégration à Power Apps, Power BI)

Power BI fournit des visualisations interactives avec des fonctionnalités de décisionnel libre-service, ce qui permet aux utilisateurs finaux de créer eux-mêmes des rapports et des tableaux de bord. En utilisant le connecteur Spark intégré, vous pouvez connecter Power BI Desktop à des clusters Apache Spark dans Azure Cosmos DB. Ce connecteur vous permet d’utiliser une requête directe pour décharger le traitement sur Apache Spark dans Azure Cosmos DB, ce qui s'avère particulièrement intéressant en présence d'une importante quantité de données que vous ne souhaitez pas charger dans Power BI ou lorsque vous souhaitez effectuer des analyses en temps quasi-réel.

## <a name="iot-analytics-at-global-scale"></a>Analyse IoT à l'échelle mondiale

Les données générées par les capteurs de réseau toujours plus nombreux offrent une visibilité sans précédent sur les systèmes et processus précédemment opaques. La clé consiste à trouver des insights exploitables dans ce flot d'informations, quel que soit le lieu où les appareils IoT sont distribués à l'échelle mondiale. Azure Cosmos DB permet aux entreprises IoT d’analyser le capteur à haute vitesse et les données de série chronologique en temps réel partout dans le monde. Vous pouvez ainsi exploiter tout le potentiel d’un monde interconnecté pour offrir des expériences client et une efficacité opérationnelle améliorées, et de nouvelles opportunités de revenus.

## <a name="stream-processing-and-event-analytics"></a>Traitement des flux de données et analyse des événements 

Des fichiers journaux aux données de capteur, les entreprises font face à des flux de données toujours plus importants. Ces données arrivent en flux constant, souvent simultanément en provenance de plusieurs sources. Bien qu’il soit possible de stocker ces flux de données sur disque et de les analyser de manière rétrospective, il s'avère parfois plus judicieux de traiter et d'agir sur ces données au fur et à mesure qu’elles arrivent. Par exemple, les flux de données liés aux transactions financières peuvent être traités en temps réel afin d'identifier et de refuser des transactions potentiellement frauduleuses.

## <a name="interactive-analytics"></a>Analytique interactive

En plus d’exécuter des requêtes prédéfinies afin de créer des tableaux de bord statiques pour les ventes, la productivité ou les cotations boursières, vous souhaiterez peut-être explorer les données de manière interactive. L'analytique interactive vous permet de poser des questions, d’afficher les résultats, de modifier la question initiale selon la réponse ou d’approfondir les résultats. Apache Spark dans Azure Cosmos DB prend en charge les requêtes interactives en y répondant et en s'y adaptant rapidement.

## <a name="data-exploration-using-jupyter-notebooks"></a>Exploration des données à l’aide des notebooks Jupyter

En présence d'un nouveau jeu de données, et avant d'exécuter des modèles et des tests, vous devez inspecter ces données. En d’autres termes, vous devez les soumettre à une analyse exploratoire. L’exploration des données facilite la prise de décision. Par exemple, vous pouvez trouver des détails tels que les méthodes susceptibles d'être utilisées sur vos données, déterminer si les données répondent à certaines hypothèses de modélisation, si elles doivent être nettoyées, restructurées, etc. En utilisant les notebooks Jupyter et Apache Spark intégrés à Azure Cosmos DB, vous pouvez effectuer une analyse exploratoire rapide et efficace des données transactionnelles et analytiques.

## <a name="next-steps"></a>Étapes suivantes

Pour vous familiariser avec ces cas d’utilisation, consultez les articles suivants :

* [Notebooks Jupyter intégrés dans Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Activer les notebooks pour les comptes Azure Cosmos](enable-notebooks.md)
* [Créer un notebook pour analyser et visualiser les données](create-notebook-visualize-data.md)