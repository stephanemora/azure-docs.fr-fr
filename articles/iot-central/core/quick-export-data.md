---
title: Démarrage rapide - Exporter des données à partir d’Azure IoT Central
description: 'Démarrage rapide : Découvrez comment utiliser la fonctionnalité d’exportation de données dans IoT Central à des fins d’intégration avec d’autres services cloud.'
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 06e387e4742d86f4176d262ae2e2e9db863386ca
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300893"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>Démarrage rapide : Exporter des données à partir d’une application IoT Central

Ce démarrage rapide vous montre comment exporter en continu des données à partir de votre application Azure IoT Central vers un autre service cloud. Pour vous faciliter la tâche, ce guide de démarrage rapide utilise [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) pour vous permettre de stocker, d’interroger et de traiter les données de télémétrie à partir de l’application de smartphone **IoT Plug-and-Play**.

Dans ce guide de démarrage rapide, vous :

- Utilisez la fonctionnalité d’exportation de données de IoT Central pour exporter les données de télémétrie envoyées par l’application de smartphone vers une file d’attente Azure Event Hubs.
- Ingérez les données de télémétrie de la file d’attente Event Hubs dans une base de données Azure Data Explorer.
- Utilisez Azure Data Explorer pour exécuter des requêtes sur les données de télémétrie.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez effectuer le premier démarrage rapide [Créer une application Azure IoT Central](./quick-deploy-iot-central.md). Le scond démarrage rapide, [Configurer des règles et des actions pour votre appareil](quick-configure-rules.md), est facultatif.

## <a name="install-azure-services"></a>Installer des services Azure

Avant de pouvoir exporter des données à partir de votre application IoT Central, vous devez créer une file d’attente Event Hubs et une base de données Azure Data Explorer.

Sélectionnez le bouton suivant pour déployer les services :

[![Bouton Déployer sur Azure pour l’exportation en continu des données](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Fquickstart-cde%2Fdeploy-azure-services.json)

Dans le formulaire **Déploiement personnalisé** :

- Créez un groupe de ressources nommé *central-quickstart*.
- Choisissez la région la plus proche de vous.
- Entrez un nom Azure Data Explorer unique en utilisant des lettres minuscules et des chiffres uniquement. Par exemple, *contosocentraladx*.
- Entrez un nom d’espace de noms Azure Event Hubs unique en utilisant des lettres minuscules et des chiffres uniquement. Par exemple, *contosocentraleh*.

Sélectionnez **Vérifier + créer**, puis sélectionnez **Créer**.

> [!TIP]
> Le déploiement prend environ 10 minutes.

Une fois le déploiement terminé, notez la chaîne de connexion qu’il renvoie pour Azure Event Hubs :

1. Attendez la fin du déploiement.
1. Sélectionnez **Sorties**.
1. Notez la valeur **eventHubConnectionString** que vous utiliserez plus tard :

    :::image type="content" source="media/quick-export-data/connection-string.png" alt-text="Chaîne de connexion Event Hubs.":::

## <a name="configure-data-export"></a>Configurer une exportation de données

Ce scénario utilise une file d’attente Azure Event Hubs pour remettre les données de télémétrie collectées par votre application IoT Central à Azure Data Explorer.

Pour configurer l’exportation de données à partir d’IoT Central :

1. Accédez à la page **Exportation de données** de votre application IoT Central.
1. Sélectionnez l’onglet **Destinations**, puis **Ajouter une destination**.
1. Entrez *Event Hub 1* en tant que nom de destination. Sélectionnez **Azure Event Hubs** en tant que type de destination.
1. Entrez la chaîne de connexion de file d’attente Event Hubs enregistrée dans la section précédente. **Event Hub** est automatiquement rempli avec *centraltelemetry*.
1. Sélectionnez **Enregistrer**.
1. Dans la page **Exportation de données**, sélectionnez l’onglet **Exportations**, puis **Ajouter une exportation**.
1. Entrez *Exportation de données de télémétrie* en tant que nom d’exportation.
1. Sélectionnez **Données de télémétrie** en tant que type de données à exporter.
1. Ajoutez **Event Hub 1** en tant que destination.
1. Sélectionnez **Enregistrer**.

Une fois l’exportation en cours d’exécution, l’état de la page **Exportation de données** indique **Sain**:

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="Capture d’écran montrant une exportation de données en cours d’exécution avec l’état sain.":::

## <a name="configure-data-ingestion"></a>Configurer l’ingestion des données

Assurez-vous que l’application de téléphone est connectée à votre application IoT Central et que vous envoyez des données avant de continuer.

Votre application IoT Central exporte en continu les données télémétrie vers la file d’attente Event Hubs. Dans cette section, vous configurez votre cluster Azure Data Explorer pour ingérer en continu les données de télémétrie dans une table où vous pouvez les interroger.

Pour configurer l’ingestion des données :

1. Dans le Portail Azure, accédez à votre cluster Azure Data Explorer dans le groupe de ressources **central-quickstart** :

    :::image type="content" source="media/quick-export-data/azure-data-explorer-portal.png" alt-text="Capture d’écran de la page Vue d’ensemble d’Azure Data Explorer.":::

1. Sélectionnez **Ingérer les nouvelles données**.
1. Dans la page **Ingérer les nouvelles données**, sélectionnez votre cluster et la base de données **iotcentraldata**.
1. Sélectionnez **Créer** pour créer une nouvelle table appelée *telemetry*.
1. Sélectionnez **Event Hubs** en tant que type de source.
1. Entrez *IOT-Central-Connection* en tant que nom de connexion de données.
1. Utilisez les informations du tableau suivant pour remplir le reste du formulaire :

    | Champ                   | Valeur                            |
    |-------------------------|----------------------------------|
    | Abonnement            | Sélectionner votre abonnement Azure   |
    | Espace de noms Event Hub     | Sélectionner votre espace de noms Event Hubs |
    | Event Hub               | `centraltelemetry`               |
    | Groupe de consommateurs          | `$Default`                       |
    | Format de données             | JSON                             |
    | Compression             | Aucun                             |
    | Propriétés du système d’événements | Laisser vide                      |

1. Sélectionnez **Modifier le schéma**.
1. La page **Schéma** affiche un aperçu des données des messages dans la file d’attente Event Hubs.
1. Modifiez la valeur des niveaux imbriqués sur `3` pour développer le JSON et afficher chaque valeur de télémétrie dans sa propre colonne.
1. Sélectionnez **Démarrer l’ingestion**. Attendez que l’ingestion des données se termine.

:::image type="content" source="media/quick-export-data/data-ingestion-complete.png" alt-text="Capture d’écran montrant l’ingestion de données terminée dans Azure Data Explorer.":::

Laissez cette page ouverte, vous allez l’utiliser dans la section suivante.

## <a name="query-exported-data"></a>Interroger les données exportées

Votre cluster Azure Data Explorer ingère à présent en continu les données de votre application IoT Central. Pour interroger les données :

1. Dans la page Azure Data Explorer de la section précédente, sélectionnez la requête rapide **Prendre 10**. Cette requête sélectionne dix enregistrements de la table de données de **télémétrie** .
1. Remplacez la requête par la requête suivante :

    ```kusto
    ['telemetry'] 
    | where isnotnull(telemetry_magnetometer_x)
    | project Time=todatetime(enqueuedTime), deviceId, telemetry_magnetometer_x, telemetry_magnetometer_y, telemetry_magnetometer_z
    | render timechart 
    ```

    Cette requête trace les valeurs de télémétrie « magnétomètre » à partir de l’application de téléphone sur une chronologie.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

Pour supprimer l’instance Azure Data Explorer et l’espace de noms Event Hubs de votre abonnement et éviter des frais inutiles, supprimez le groupe de ressources **central-quickstarts** du [portail Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups).

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à exporter en continu des données entre IoT Central et un autre service Azure.

Vous savez désormais exporter vos données et voici l’étape suivane que nous vous suggérons :

> [!div class="nextstepaction"]
> [Créer et gérer un modèle d'appareil](howto-set-up-template.md).
