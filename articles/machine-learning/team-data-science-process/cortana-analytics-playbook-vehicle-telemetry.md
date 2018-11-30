---
title: Prédire l’état des véhicules et les habitudes de conduite | Microsoft Docs
description: Utilisez les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite.
services: machine-learning
author: marktab
ms.author: tdsp
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/14/2018
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 688a821667b38423b43af87b7a26525e52d9a7aa
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444157"
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Manuel de la solution Vehicle Telemetry Analytics

Aujourd’hui, les superordinateurs ne se trouvent plus dans des laboratoires mais dans des garages. Ils sont désormais placés dans des automobiles de pointe qui contiennent une multitude de capteurs. Ce capteurs leur offrent la possibilité de suivre et d’analyser des millions d’événements par seconde. D’ici 2020, la plupart de ces véhicules seront connectés à Internet. Le potentiel qu’offrent toutes ces données permet d’améliorer la sécurité, la fiabilité et le plaisir de la conduite. Microsoft fait de ce rêve une réalité en développant Cortana Intelligence.

Cortana Intelligence est une suite de traitement du Big Data et d’analyse avancée entièrement gérée, qui vous permet de convertir vos données en action intelligente. Le modèle de la solution Cortana Intelligence Vehicle Telemetry Analytics montre comment les concessionnaires, les constructeurs automobiles et les compagnies d’assurance peuvent obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite.

La solution est implémentée comme un [modèle d’architecture lambda](https://en.wikipedia.org/wiki/Lambda_architecture) montrant tout le potentiel de la plateforme Cortana Intelligence pour le traitement en temps réel et par lots.

## <a name="architecture"></a>Architecture
L’architecture de la solution Vehicle Telemetry Analytics est présentée dans ce diagramme :

![Diagramme de l’architecture de la solution](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Cette solution inclut les composants Cortana Intelligence suivants et présente leur intégration :

* **Azure Event Hubs** ingère dans Azure des millions d’événements de télémétrie associés aux véhicules.
* **Azure Stream Analytics** fournit une visibilité en temps réel sur l’état des véhicules et conserve ces données dans un stockage à long terme afin d’enrichir les analyses par lots.
* **Azure Machine Learning** détecte les anomalies en temps réel et utilise le traitement par lots pour fournir des informations prédictives.
* **Azure HDInsight** transforme les données à grande échelle.
* **Azure Data Factory** gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots.
* **POWER BI** offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

Cette solution utilise deux sources de données différentes : 

* **SIMULATION DES SIGNAUX ET DIAGNOSTICS D’UN VÉHICULE**: un simulateur télématique de véhicule émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné dans le temps. 
* **Catalogue de véhicules** : ce jeu de données de référence associe les numéros d’identification du véhicule aux modèles.

## <a name="next-steps"></a>Étapes suivantes

Pour explorer cette solution, consultez [Vehicle Telemetry Analytics : découverte approfondie de la solution](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

Pour apprendre à configurer les rapports et les tableaux de bord Power BI pour cette solution, reportez-vous aux [Instructions de configuration du tableau de bord Power BI pour le modèle de solution Vehicle Telemetry Analytics](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
