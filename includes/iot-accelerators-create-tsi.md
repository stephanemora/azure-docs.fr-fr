---
title: Fichier Include
description: Fichier Include
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8c114ed137089e70899e601ebdc1d4d39f562601
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382859"
---
## <a name="create-a-consumer-group"></a>Créer un groupe de consommateurs

Vous devez créer un groupe de consommateurs dédié dans votre hub IoT pour permettre la transmission des données de télémétrie à Time Series Insights. Une source d’événement dans Time Series Insights doit avoir l’usage exclusif d’un groupe de consommateurs IoT Hub.

Les étapes suivantes utilisent l’interface Azure CLI dans Azure Cloud Shell pour créer le groupe de consommateurs :

1. Le hub IoT est l’une des ressources qui ont été créées quand vous avez déployé l’accélérateur de solution Simulation d’appareil. Exécutez la commande suivante pour obtenir le nom de votre hub IoT. N’oubliez pas de spécifier le nom de votre propre accélérateur de solution :

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Le hub IoT est la ressource de type **Microsoft.Devices/IotHubs**.

1. Ajoutez au hub un groupe de consommateurs appelé **devicesimulationtsi**. Dans la commande suivante, utilisez le nom de votre hub et de votre accélérateur de solution :

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Vous pouvez maintenant fermer Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Créer un environnement Time Series Insights

[Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md) est un service entièrement managé d’analytique, de stockage et de visualisation pour la gestion des données chronologiques IoT dans le cloud. Pour créer un environnement Time Series Insights :

1. Connectez-vous au [Portail Azure](http://portal.azure.com/).

1. Sélectionnez **Créer une ressource** > **Internet des objets** > **Time Series Insights** :

    ![Nouvel environnement Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. Pour créer votre environnement Time Series Insights dans le même groupe de ressources que votre accélérateur de solution, utilisez les valeurs du tableau ci-dessous :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de l’environnement | La capture d’écran suivante utilise le nom **Contoso-TSI**. Choisissez votre propre nom unique quand vous effectuez cette étape. |
    | Abonnement | Sélectionnez votre abonnement Azure dans la liste déroulante. |
    | Groupe de ressources | **contoso-simulation**. Utilisez le nom de votre accélérateur de solution. |
    | Lieu | Cet exemple utilise la région **East US**. Créez votre environnement dans la même région que votre accélérateur de solution Simulation d’appareil. |
    | Sku |**S1** |
    | Capacité | **1** |

    ![Créer un environnement Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > Si vous ajoutez l’environnement Time Series Insights au même groupe de ressources que l’accélérateur de solution, l’environnement est automatiquement supprimé en même temps que l’accélérateur de solution.

1. Cliquez sur **Créer**. La création de l’environnement peut prendre plusieurs minutes.

## <a name="create-event-source"></a>Créez la source d’événement

Créez une source d’événement à connecter à votre hub IoT. Utilisez le groupe de consommateurs que vous avez créé aux étapes précédentes. Une source d’événement Time Series Insights nécessite un groupe de consommateurs dédié qui ne soit pas déjà utilisé par un autre service.

1. Dans le portail Azure, accédez à votre nouvel environnement Time Series.

1. Sur la gauche, cliquez sur **Sources d’événements** :

    ![Afficher les sources d’événements](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. Cliquez sur **Ajouter** :

    ![Ajouter une source d’événement](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. Pour configurer votre hub IoT comme nouvelle source d’événement, utilisez les valeurs dans le tableau suivant :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de la source d’événement | La capture d’écran suivante utilise le nom **contoso-iot-hub**. Utilisez votre propre nom unique quand vous effectuez cette étape. |
    | Source | **IoT Hub** |
    | Option d’importation | **Utiliser Iot Hub à partir des abonnements disponibles** |
    | ID d’abonnement | Sélectionnez votre abonnement Azure dans la liste déroulante. |
    | Nom du hub IoT | **contoso-simulation7d894**. Utilisez le nom de votre hub IoT spécifié dans votre accélérateur de solution Simulation d’appareil. |
    | Nom de la stratégie du hub IoT | **iothubowner** |
    | Clé de stratégie du hub IoT | Ce champ est rempli automatiquement. |
    | Groupe de consommateurs du hub IoT | **devicesimulationtsi** |
    | Format de sérialisation de l’événement | **JSON** |
    | Nom de la propriété d’horodatage | Laisser vide |

    ![Créer une source d’événement](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. Cliquez sur **Créer**.

> [!NOTE]
> Vous pouvez [accorder l’accès à des utilisateurs supplémentaires](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) à l’Explorateur Time Series Insights.