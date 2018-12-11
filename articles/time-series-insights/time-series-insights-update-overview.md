---
title: Vue d’ensemble d’Azure Time Series Insights | Microsoft Docs
description: Vue d’ensemble d’Azure Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/03/2018
ms.openlocfilehash: 83513ab9f7bee5c51fc909e5f4246105b6f145ca
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876129"
---
# <a name="azure-time-series-insights-overview"></a>Vue d’ensemble d’Azure Time Series Insights

Azure Time Series Insights (TSI) est une plateforme PaaS (platform-as-a-service) complète destinée à ingérer, traiter, stocker et interroger des données IoT hautement contextualisées et optimisées pour les séries chronologiques. Ainsi, Azure TSI est idéal pour l’exploration de données ad hoc et l’analyse opérationnelle. TSI est une offre de service personnalisée et extensible de manière unique qui répond aux besoins étendus des déploiements IoT sectoriels.

## <a name="what-is-iot-data"></a>En quoi consistent les données IoT ?

Les données IoT sont des données « sectorielles » qui se trouvent dans des organisations qui utilisent un nombre considérable de ressources. Les données IoT sont souvent très peu structurées, car elles sont issues d’une grande variété de ressources qui enregistrent des mesures assez bruyantes comme la température, le mouvement ou l’humidité. De plus, ces flux de données se caractérisent souvent par des lacunes importantes, des messages corrompus et des relevés erronés. Les données de ces flux doivent être nettoyées avant de pouvoir faire l’objet d’une analyse. Les données IoT sont souvent pertinentes quand elles sont couplées à d’autres entrées de données extraites de sources de données internes (CRM ou ERP) ou tierces (par exemple, météo ou emplacement).

De ce fait, seule une fraction négligeable de ces données est utilisée à des fins opérationnelle et commerciales. Ces données sont des sources d’information cohérentes, complètes, actuelles et fiables qui permettent de créer des rapports d’entreprise et de procéder à des analyses. Autrement dit, pour transformer les données IoT collectées en insights exploitables, il est indispensable de :

* Traiter les données pour nettoyer, filtrer, interpoler, transformer et préparer les données en vue de leur analyse
* Structurer les données pour pouvoir les explorer et les comprendre (normalisation et contextualisation des données)
* Stocker à peu de frais l’équivalent de plusieurs décennies de données traitées (dérivées) et brutes dans l’optique d’une conservation longue/infinie

Le flux de données IoT classique peut être représenté comme suit :

  ![Flux de données IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights pour l’IoT sectoriel

Le paysage actuel de l’IoT est divers et varié, rassemblant des clients issus de secteurs très différents : industrie manufacturière, automobile, pétrole et gaz, production et distribution d’électricité, bâtiments intelligents, conseil. L’IoT est utilisé dans divers scénarios, notamment dans l’exploration de données ad hoc dont la forme est inconnue, mais aussi dans l’analyse opérationnelle de données schématisées (explicitement modélisées) pour booster l’efficacité opérationnelle. En général, ces scénarios coexistent et supportent différents cas d’usage. Les fonctionnalités de plateforme comme le stockage multicouche (chaud et froid), la capacité à stocker l’équivalent de plusieurs décennies de données de séries chronologiques et l’aptitude à modéliser explicitement et optimiser les requêtes pour l’intelligence opérationnelle basée sur les ressources deviennent indispensables au succès des entreprises IoT sectorielles et leur révolution numérique.

Azure TSI est une offre PaaS (platform-as-a-service) complète destinée à explorer les données IoT et à tirer des insights opérationnels. Time Series Insights offre un service cloud entièrement géré dédié à l’analyse de données de séries chronologiques IoT.

Les clients peuvent stocker des données brutes dans un magasin sans schéma en mémoire et exécuter des requêtes ad hoc interactives via un moteur de requête distribué et des API. Par ailleurs, notre expérience utilisateur élaborée leur permet de visualiser des milliards d’événements en quelques secondes. En savoir plus sur nos [fonctionnalités d’exploration de données](./time-series-insights-overview.md).

TSI propose également des fonctionnalités d’insights opérationnels qui sont pour l’instant disponibles dans une préversion publique. Grâce à l’exploration de données interactive et à l’intelligence opérationnelle, Time Series Insights permet aux clients de mieux exploiter les données collectées auprès des ressources IoT. Plus particulièrement, l’offre Préversion publique prend en charge les fonctionnalités clés suivantes :

* Magasin de données de séries chronologiques scalable, optimisé en termes de performances et de coûts, permettant à une solution IoT basée sur le cloud d’afficher les tendances de données de séries chronologiques sur plusieurs années en quelques secondes.
* Prise en charge du modèle sémantique permettant de décrire le domaine et les métadonnées associés aux signaux dérivés et non dérivés de ressources et d’appareils.
* Expérience utilisateur améliorée qui combine des insights de données basés sur des ressources et des analytiques détaillées ad hoc pour booster l’intelligence opérationnelle et commerciale.
* Intégration avec des outils de Machine Learning et d’analytique avancés comme Azure Databricks, Apache Spark, Azure Machine Learning, les notebooks Jupyter, Power BI, etc., pour aider les clients à relever les défis que représentent les données de séries chronologiques et à booster l’efficacité opérationnelle.

L’offre couplé des insights opérationnels et de l’exploration de données obéissent à un modèle de paiement à l’utilisation simple pour le traitement, le stockage et l’interrogation de données. Les clients disposent ainsi d’un modèle bien plus souple qui s’adapte à leurs besoins évolutifs.

Le diagramme de flux de données général ci-dessous décrit les fonctionnalités mises à jour :

  ![Fonctionnalités clés][2]

Avec l’introduction de ces fonctionnalités IoT sectorielles clés, Azure TSI offre les avantages suivants.

* Stockage multicouche pour les données de séries chronologiques IoT

  * Profitant d’un pipeline de traitement de données commun pour l’ingestion de données, les clients peuvent stocker les données dans un stockage chaud pour les requêtes interactives et/ou dans un stockage froid pour entreposer d’importants volumes de données et profiter de requêtes hautement performantes basées sur les ressources.

  * Le routage dynamique entre les différentes couches de stockage sera disponible prochainement.

* Modèle de série chronologique pour la contextualisation des données de télémétrie brutes et la dérivation d’insights basés sur les ressources

  * Les clients peuvent contextualiser les données de télémétrie brutes avec un modèle de série chronologique descriptif et bénéficier d’une intelligence opérationnelle élaborée avec des requêtes basées sur les appareils optimisées en termes de performances et de coûts.

* Intégration fluide avec d’autres solutions de données
  
  * Sachant que les données Azure Time Series Insights sont stockées dans des fichiers Apache Parquet open source, les clients peuvent s’intégrer facilement avec d’autres solutions de données (internes ou tierces) pour les scénarios complets incluant le décisionnel, le Machine Learning avancé, l’analyse prédictive, etc.

* Exploration de données en quasi temps réel

  * L’expérience utilisateur de l’explorateur Azure Time Series Insights permet de visualiser toutes les données diffusées en continu via le pipeline d’ingestion. Peu de temps après s’être connectés à une source d’événement, les clients peuvent afficher, explorer et interroger les données d’événement pour vérifier si un appareil émet des données comme prévu et pour surveiller l’intégrité, la productivité et l’efficacité globale d’une ressource IoT.

* Analyse de la cause première et détection des anomalies

  * L’explorateur Azure Time Series Insights prend en charge des modèles et des vues de perspective pour effectuer et enregistrer des analyses des causes racine à plusieurs étapes. Les clients peuvent combiner Time Series Insights et Azure Stream Analytics pour détecter les alertes et les anomalies en quasi temps réel.

* Créer des applications personnalisées sur la plateforme Time Series Insights

  * Azure Time Series Insights prend en charge le kit SDK JavaScript et ses contrôles élaborés et son accès simplifié aux requêtes. Les clients peuvent ainsi créer des applications IoT personnalisées sur la plateforme Time Series Insights qui répondent à leurs besoins spécifiques.
  * Les clients peuvent aussi utiliser les API de requête Time Series Insights directement pour intégrer directement des données dans des applications IoT personnalisées.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à démarrer avec Azure TSI (préversion) :

> [!div class="nextstepaction"]
> [Lire le guide de démarrage rapide](./time-series-insights-update-quickstart.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png