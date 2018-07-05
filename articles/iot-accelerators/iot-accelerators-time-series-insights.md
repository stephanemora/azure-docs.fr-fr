---
title: Visualiser les données de surveillance à distance avec Azure Time Series Insights | Microsoft Docs
description: Découvrez comment configurer votre environnement Time Series Insights pour explorer et analyser les données de la série chronologique de votre solution de surveillance à distance.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 7a0a5d4f1fbba5d7bd2813e8b9c300a37853e06c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111474"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Visualiser les données de surveillance à distance avec Time Series Insights

Un opérateur peut étendre davantage la visualisation des données prête à l’emploi fournie par la solution préconfigurée de surveillance à distance. Notre accélérateur de solution propose une intégration prête à l’emploi avec TSI. Dans cette procédure, vous allez apprendre à configurer Time Series Insights pour analyser les données de télémétrie d’appareil et détecter les anomalies.

## <a name="prerequisites"></a>Prérequis

Pour réaliser cette procédure, vous avez besoin des éléments suivants :

* Tutoriel [Déployer la solution préconfigurée de surveillance à distance](iot-accelerators-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Créer un groupe de consommateurs

Vous devez créer un groupe de consommateurs dédié dans votre hub IoT à utiliser pour le streaming des données dans Time Series Insights.

> [!NOTE]
> Les groupes de consommateurs sont utilisés par les applications pour extraire des données de l’instance Azure IoT Hub. Chaque groupe de consommateurs peut contenir jusqu’à cinq consommateurs de sortie. Vous devez créer un autre groupe de consommateurs tous les cinq récepteurs de sortie, et vous pouvez créer jusqu’à 32 groupes de consommateurs.

1. Dans le portail Azure, cliquez sur le bouton Cloud Shell.

1. Exécutez la commande suivante pour créer un groupe de consommateurs :

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Créer un environnement Time Series Insights

Azure Time Series Insights est un service entièrement managé d’analytique, de stockage et de visualisation pour la gestion des données chronologiques IoT dans le cloud. Le service fournit un stockage des données chronologiques massivement scalable. Il vous permet d’explorer et d’analyser des milliards de streamings d’événements provenant du monde entier en quelques secondes. Time Series Insights permet de stocker et de gérer plusieurs téraoctets de données chronologiques, d’explorer et de visualiser des milliards d’événements de façon simultanée, de procéder à une analyse de la cause racine et de comparer plusieurs sites et ressources.

1. Connectez-vous au [Portail Azure](http://portal.azure.com/).

1. Sélectionnez **Créer une ressource** > **Internet des objets** > **Time Series Insights**.

    ![Nouvel environnement Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Pour créer votre environnement Time Series Insights, utilisez les valeurs dans le tableau suivant :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de l’environnement | La capture d’écran suivante utilise le nom **contorosrmtsi**. Choisissez votre propre nom unique quand vous effectuez cette étape. |
    | Abonnement | Sélectionnez votre abonnement Azure dans la liste déroulante. |
    | Groupe de ressources | **Créer un nouveau**. Nous utilisons le nom **ContosoRM**. |
    | Lieu | Nous utilisons **Est des États-Unis**. Créez votre environnement dans la même région que votre solution de surveillance à distance. |
    | Sku |**S1** |
    | Capacité | **1** |
    | Épingler au tableau de bord | **Oui** |

    ![Créer un environnement Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Cliquez sur **Créer**. La création de l’environnement peut prendre quelques instants.

## <a name="create-event-source"></a>Créer une source d’événement

Créez une source d’événement à connecter à votre hub IoT. Vérifiez que vous utilisez le groupe de consommateurs créé dans les étapes précédentes. Time Series Insights exige que chaque service dispose d’un groupe de consommateurs dédié qui ne soit pas utilisé par un autre service.

1. Accédez à votre nouvel environnement Time Series.

1. Sur la gauche, sélectionnez **Sources d’événements**.

    ![Afficher les sources d’événements](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Cliquez sur **Add**.

    ![Ajouter une source d’événement](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Pour configurer votre hub IoT comme nouvelle source d’événement, utilisez les valeurs dans le tableau suivant :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de la source d’événement | La capture d’écran suivante utilise le nom **contosorm-iot-hub**. Utilisez votre propre nom unique quand vous effectuez cette étape. |
    | Source | **IoT Hub** |
    | Option d’importation | **Utiliser Iot Hub à partir des abonnements disponibles** |
    | ID d’abonnement | Sélectionnez votre abonnement Azure dans la liste déroulante. |
    | Nom du hub IoT | **contosorma57a6**. Utilisez le nom du hub IoT associé à votre solution de surveillance à distance. |
    | Nom de la stratégie du hub IoT | **iothubowner** Vérifiez que la stratégie utilisée est une stratégie propriétaire. |
    | Clé de stratégie du hub IoT | Ce champ est rempli automatiquement. |
    | Groupe de consommateurs du hub IoT | **timeseriesinsights** |
    | Format de sérialisation de l’événement | **JSON**     | Nom de la propriété d’horodatage | Laisser vide |

    ![Créer une source d’événement](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Cliquez sur **Créer**.

> [!NOTE]
> Si vous devez accorder à des utilisateurs supplémentaires l’accès à l’Explorateur Time Series Insights, vous pouvez suivre ces étapes pour [accorder l’accès aux données](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Explorateur Time Series Insights

L’Explorateur Time Series Insights est une application web qui vous permet de créer des visualisations de vos données.

1. Sélectionnez l’onglet **Vue d’ensemble**.

1. Cliquez sur **Accéder à l’environnement** pour ouvrir l’application web de l’Explorateur Time Series Insights.

    ![Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. Dans le panneau de sélection des durées, sélectionnez **12 dernières heures** dans le menu des durées rapides et cliquez sur **Rechercher**.

    ![Recherche dans l’Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. Dans le panneau des conditions sur la gauche, sélectionnez la valeur de mesure **température** et la valeur de fractionnement **iothub-connection-device-id**.

    ![Requête de l’Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Cliquez avec le bouton droit sur le graphique, puis sélectionnez **Explorer les événements**.

    ![Événements de l’Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Les événements sont affichés dans la grille sous forme de tableau.

    ![Tableau de l’Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Cliquez sur le bouton Vue en perspective.

    ![Perspective de l’Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Cliquez sur **Ajouter** pour créer une requête dans la perspective.

    ![Ajouter une requête dans l’Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Sélectionnez la durée rapide **12 dernières heures**, la mesure **Humidité** et la valeur de fractionnement **iothub-connection-device-id**.

    ![Requête de l’Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Cliquez sur le bouton Vue en perspective pour afficher le tableau de bord des métriques de l’appareil.

    ![Tableau de bord de l’Explorateur Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon d’explorer et d’interroger des données dans l’Explorateur Time Series Insights, consultez [Explorateur Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
