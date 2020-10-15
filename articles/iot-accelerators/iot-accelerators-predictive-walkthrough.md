---
title: Présentation de l’accélérateur de solution de maintenance prédictive - Azure | Microsoft Docs
description: Vue d’ensemble de l’accélérateur de solution Maintenance prédictive Azure IoT qui prévoit le moment auquel il risque de se produire une défaillance dans un scénario professionnel.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73827410"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Présentation de l’accélérateur de solution de maintenance prédictive

L’accélérateur de solution Maintenance prédictive est une solution de bout en bout pour un scénario d’entreprise qui prévoit le moment auquel une défaillance est susceptible de se produire. Vous pouvez utiliser cet accélérateur de solution de manière proactive pour des activités telles que l’optimisation de la maintenance. La solution combine des services d’accélérateur de solution Azure IoT clés, notamment IoT Hub et un espace de travail [Azure Machine Learning][lnk-machine-learning]. Cet espace de travail contient un modèle basé sur un échantillon de données publiques, permettant de prédire la durée de vie utile restante (VUR) d’un moteur d’avion. La solution implémente complètement le scénario professionnel IoT comme point de départ, pour vous permettre de planifier et de mettre en œuvre une solution qui réponde à vos besoins professionnels.

Le code de l’accélérateur de solution de maintenance prédictive est [disponible sur GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Architecture logique

Le diagramme suivant décrit les composants logiques de l’accélérateur de solution :

![Architecture logique][img-architecture]

Les éléments en bleu sont des services Azure configurés dans la région dans laquelle vous avez déployé l’accélérateur de solution. La liste des régions dans lesquelles il est possible de déployer l’accélérateur de solution apparaît sur la [page de configuration][lnk-azureiotsolutions].

L’élément vert simule un moteur d’avion. Des informations supplémentaires sur ces appareils simulés sont disponibles dans la section [Simulations d’appareils](#simulated-devices).

Les éléments gris sont des composants qui implémentent les fonctionnalités de *gestion des appareils* . La version actuelle de l’accélérateur de solution Maintenance prédictive ne configure pas ces ressources. Pour plus d’informations sur la gestion des appareils, voir [Accélérateur de solution de supervision à distance][lnk-remote-monitoring].

## <a name="azure-resources"></a>Ressources Azure

Dans le portail Azure, accédez au groupe de ressources portant le nom de solution que vous avez choisi, pour afficher vos ressources configurées.

![Ressources de l’accélérateur][img-resource-group]

Lorsque vous approvisionnez l’accélérateur de solution, vous recevez un e-mail contenant un lien vers l’espace de travail Machine Learning. Vous pouvez également accéder à l’espace de travail Machine Learning sur la page [Accélérateurs de solution Microsoft Azure IoT][lnk-azureiotsolutions]. Une vignette est disponible sur cette page lorsque le statut de la solution est **Prêt**.

![Modèle Machine Learning][img-machine-learning]

## <a name="simulated-devices"></a>Simulations d’appareils

Dans l’accélérateur de solution, un appareil simulé est un moteur d’avion. La solution est configurée avec deux moteurs correspondant à un même avion. Chaque moteur émet quatre types de données de télémétrie : Capteur 9, Capteur 11, Capteur 14 et Capteur 15 fournissent les données nécessaires au modèle Machine Learning pour calculer la durée de vie utile restante du moteur. Chaque appareil simulé envoie les messages de télémétrie suivants à l’IoT Hub :

*Nombre de cycles*. Un cycle est un vol d’une durée comprise entre deux et dix heures. Pendant le vol, les données de télémétrie sont collectées toutes les demi-heures.

*Télémétrie*. Quatre capteurs enregistrent les attributs du moteur. Les capteurs sont nommés de manière générique Capteur 9, Capteur 11, Capteur 14 et Capteur 15. Ces quatre capteurs envoie une télémétrie suffisante pour obtenir des résultats utiles à partir du modèle de durée de vie utile restante. Le modèle utilisé dans l’accélérateur de solution est créé à partir d’un jeu de données publiques qui inclut des données réelles de capteurs de moteur. Pour plus d’informations sur la création du modèle à partir du jeu de données d’origine, voir [Modèle de maintenance prédictive Cortana Intelligence Gallery][lnk-cortana-analytics].

Les appareils simulés peuvent prendre en charge les commandes suivantes, envoyées à partir d’IoT Hub dans la solution :

| Commande | Description |
| --- | --- |
| StartTelemetry |Contrôle l’état de la simulation.<br/>Commande à l’appareil d’envoyer des données de télémétrie |
| StopTelemetry |Contrôle l’état de la simulation.<br/>Commande à l’appareil de cesser l’envoi de données de télémétrie |

IoT Hub fournit un accusé de réception de la commande de l’appareil.

## <a name="azure-stream-analytics-job"></a>Tâche Azure Stream Analytics

**Tâche : Télémétrie** agit sur le flux entrant de télémétrie de l’appareil avec deux instructions :

* La première sélectionne toutes les données de télémétrie à partir des appareils et envoie ces données au Blob Storage. D’ici, elles sont visualisées dans l’application web.
* La seconde calcule les valeurs de capteurs moyennes sur une fenêtre glissante de deux minutes et les envoie via l’Event Hub à un **processeur d’événements**.

## <a name="event-processor"></a>Processeur d’événements
**L’hôte de processeur d’événements** s’exécute dans une tâche web Azure. Le **processeur d’événements** mémorise les valeurs moyennes des capteurs sur un cycle terminé. Il les transmet à un modèle formé pour calculer la durée de vie utile restante d’un moteur. Une API permet d’accéder au modèle dans un espace de travail Machine Learning qui fait partie de la solution.

## <a name="machine-learning"></a>Machine Learning
Le composant Machine Learning utilise un modèle dérivé des données collectées à partir des moteurs d’avion réels. Vous pouvez accéder à l’espace de travail Machine Learning grâce à la vignette de votre solution sur la page [azureiotsolutions.com][lnk-azureiotsolutions]. La vignette est disponible lorsque le statut de la solution est **Prêt**.

Le modèle Machine Learning est disponible comme modèle qui montre comment utiliser les données télémétriques recueillies via les services des accélérateurs de solution IoT. Microsoft a créé un [modèle de régression][lnk_regression_model] d’un moteur d’avion à partir de données disponibles publiquement<sup>\[1\]</sup>, ainsi que des instructions d’utilisation détaillées.

L’accélérateur de solution de maintenance prédictive Azure IoT utilise le modèle de régression créé à partir de ce modèle. Le modèle est déployé dans votre abonnement Azure et mis a disposition via une API générée automatiquement. La solution inclut un sous-ensemble des données de tests pour 4 (sur un total 100) moteurs et 4 (sur un total 21) flux de données de capteurs. Ces données sont suffisantes pour fournir un résultat exact du modèle formé.

*\[1\] A. Saxena and K. Goebel (2008). « Turbofan Engine Degradation Simulation Data Set », NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les composants clés de l’accélérateur de solution Maintenance prédictive, vous pouvez passer à la personnalisation.

Vous pouvez également explorer certaines des autres fonctionnalités des accélérateurs de solution IoT :

* [Questions fréquentes sur les accélérateurs de solution IoT][lnk-faq]
* [Sécurisation de l’Internet des objets de bout en bout][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
