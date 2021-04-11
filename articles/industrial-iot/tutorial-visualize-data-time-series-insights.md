---
title: Visualiser des données OPC UA dans Azure Time Series Insights
description: Dans ce tutoriel, vous allez découvrir comment visualiser des données avec Time Series Insights.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787261"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Tutoriel : Visualiser des données avec Time Series Insights (TSI)

Le module OPC Publisher se connecte aux serveurs OPC UA et publie les données OPC UA de ces serveurs dans IoT Hub. Le processeur de télémétrie de la plateforme Industrial IoT traite ces événements et transmet des exemples contextualisés à TSI et à d’autres consommateurs.  

Ce guide montre comment visualiser et analyser la télémétrie OPC UA à l’aide de cet environnement Time Series Insights.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Tous les tutoriels incluent une liste récapitulant les étapes à effectuer
> * Chacun de ces éléments à puce s’aligne sur une clé H2
> * Utilisez ces cases à cocher vertes dans un tutoriel

## <a name="prerequisite"></a>Configuration requise

* Déployer la plateforme IIoT pour qu’un environnement Time Series Insights soit créé automatiquement
* Les données sont publiées sur IoT Hub

## <a name="time-series-insights-explorer"></a>Explorateur Time Series Insights

L’Explorateur Time Series Insights est une application web que vous pouvez utiliser pour visualiser vos données de télémétrie. Pour récupérer l’URL de l’application, ouvrez le fichier `.env`enregistré à la suite du déploiement.  Ouvrez un navigateur à l’URL de la variable `PCS_TSI_URL`.  

Avant d’utiliser l’Explorateur Time Series Insights, vous devez accorder l’accès aux données STI aux utilisateurs autorisés à visualiser les données. Notez que dans un nouveau déploiement, aucune stratégie d’accès aux données n’est définie par défaut ; par conséquent, personne ne peut voir les données. Les stratégies d’accès aux données doivent être définies dans le portail Azure, dans l’environnement Time Series Insights déployé dans le groupe de ressources déployé de la plateforme IIoT, comme suit :

   ![Explorateur Time Series Insights 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Sélectionnez les stratégies d’accès aux données :

   ![Explorateur Time Series Insights 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Affectez les utilisateurs requis :

   ![Explorateur Time Series Insights 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


Dans l’Explorateur TSI, prenez note des Instances Time Series non attribuées. Une instance TSI correspond à la série temps/valeur d’un point de données spécifique provenant d’un nœud publié dans un serveur OPC. L’instance TSI, relativement au point de données OPC UA, est identifiée de manière unique par EndpointId, SubscriptionId et NodeId. Les modèles d’instances TSI sont détectés automatiquement, et affichés dans l’Explorateur en fonction des données de télémétrie ingérées à partir du hub d’événements du processeur de télémétrie de la plateforme IIoT.

   ![Explorateur Time Series Insights 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

Vous pouvez visualiser les données de télémétrie dans le graphique en cliquant avec le bouton droit sur l’instance TSI et en sélectionnant la valeur. La période à utiliser dans le graphique peut être ajusté dans le coin supérieur droit. La valeur de plusieurs instances peut être visualisée pour la même sélection de temps.

Pour plus d’informations, consultez [Démarrage rapide : Explorer Azure Time Series Insights Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

## <a name="define-and-apply-a-new-model"></a>Définir et appliquer un nouveau modèle

Les instances de télémétrie étant pour le moment simplement au format brut, elles doivent être contextualisées. 

Pour obtenir des informations détaillées sur les modèles TSI, consultez [Modèle de série chronologique dans Azure Time Series Insights Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).

1. Étape 1 : Sous l’onglet Modèle de l’Explorateur, définissez une nouvelle hiérarchie pour les données de télémétrie ingérées. Une hiérarchie est l’arborescence logique destinée à permettre à l’utilisateur d’insérer les méta-informations nécessaires à une navigation plus intuitive parmi les instances TSI. Un utilisateur peut créer/supprimer/modifier des modèles de hiérarchie qui peuvent être instanciés ultérieurement pour les différentes instances TSI.

   ![Étape 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. Étape 2 : Définissez un nouveau type pour les valeurs. Dans notre exemple, nous ne prenons en charge que les types de données numériques.

   ![Étape 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. Étape 3 : Sélectionnez la nouvelle instance TSI qui doit être catégorisée dans la hiérarchie précédemment définie.

   ![Étape 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. Étape 4 : Renseignez les propriétés d’instance (nom, description, valeur des données) ainsi que les champs de hiérarchie afin qu’ils correspondent à la structure logique. 

   ![Étape 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. Étape 5 : Répétez l’étape 4 pour toutes les instances TSI sans catégorie.

   ![Étape 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. Étape 6 : De retour dans la page principale de l’Explorateur TSI, passez en revue la hiérarchie des instances catégorisées et sélectionnez les valeurs des points de données à analyser.

   ![Étape 6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Connecter Time Series Insights à Power BI

Vous pouvez également connecter l’environnement Time Series Insights à Power BI.  Pour plus d’informations, consultez [Guide pratique pour connecter TSI à Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) et [Visualiser des données de TSI dans Power BI](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à visualiser des données dans TSI, vous pouvez consulter le dépôt GitHub Industrial IoT :

> [!div class="nextstepaction"]
> [Dépôt GitHub de la plateforme IIoT](https://github.com/Azure/iot-edge-opc-publisher)