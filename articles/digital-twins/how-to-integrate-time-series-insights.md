---
title: Intégrer à Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer des itinéraires d’événements d’Azure Digital Twins à Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6aeb7489b455840eeca0a8e1967c7e6e2ed50b7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199898"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Intégrer Azure Digital Twins avec Azure Time Series Insights

Dans cet article, vous allez apprendre à intégrer Azure Digital Twins avec [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

La solution décrite dans cet article vous permettra de collecter et d’analyser les données d’historique relatives à votre solution IoT. Azure Digital Twins est idéal pour alimenter des données dans Time Series Insights, car il vous permet de mettre en corrélation plusieurs flux de données et de normaliser vos informations avant de les envoyer à Time Series Insights. 

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer une relation avec Time Series Insights, vous devez disposer d’une **instance Azure Digital Twins**. Cette instance doit être configurée avec la possibilité de mettre à jour les informations relatives aux représentations numériques en fonction des données, car vous devrez mettre à jour les informations sur les représentations plusieurs fois pour voir ces données suivies dans Time Series Insights. 

Si vous ne l’avez pas déjà configurée, vous pouvez la créer en suivant le [*tutoriel Azure Digital Twins : Connecter une solution de bout en bout*](./tutorial-end-to-end.md). Ce tutoriel vous guide dans la configuration d’une instance Azure Digital Twins qui fonctionne avec un appareil IoT virtuel pour déclencher des mises à jour de jumeaux numériques.

## <a name="solution-architecture"></a>Architecture de solution

Vous allez attacher Time Series Insights à Azure Digital Twins via le chemin d’accès ci-dessous.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Vue des services Azure dans un scénario de bout en bout, mise en surbrillance de Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Créer un itinéraire et un filtre pour les notifications de mise à jour des jumeaux

Les instances Azure Digital Twins peuvent émettre des [événements de mise à jour des jumeaux](how-to-interpret-event-data.md) chaque fois que l’état d’un jumeau est mis à jour. Dans cette section, vous allez créer un [**itinéraire d’événement**](concepts-route-events.md) Azure Digital Twins qui dirigera ces événements de mise à jour vers Azure [Event Hubs](../event-hubs/event-hubs-about.md) en vue d’un traitement ultérieur.

Le [*Tutoriel Azure Digital Twins : Connecter une solution de bout en bout*](./tutorial-end-to-end.md) référencé ci-dessus présente un scénario dans lequel un thermomètre est utilisé pour mettre à jour un attribut sur un jumeau numérique représentant une pièce. Ce modèle repose sur les mises à jour de jumeaux, plutôt que transférer la télémétrie à partir d’un appareil IoT, ce qui vous donne la possibilité de modifier la source de données sous-jacente sans avoir à mettre à jour votre logique Time Series Insights.

1. Créez d’abord un espace de noms Event Hub, qui recevra les événements de notre instance Azure Digital Twins. Vous pouvez utiliser les instructions Azure CLI ci-dessous ou utiliser le portail Azure : [*Démarrage rapide : Créer un hub d’événements avec le portail Azure*](../event-hubs/event-hubs-create.md). Pour connaître les régions qui prennent en charge Event Hubs, consultez [*Produits Azure disponibles par région*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. Créez un hub d’événements dans l’espace de noms pour recevoir les événements de changement de jumeau. Spécifiez un nom pour le hub d’événements.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Créez une [règle d’autorisation](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) avec les autorisations d’envoi et de réception. Spécifiez un nom pour la règle.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Créez un [point de terminaison](concepts-route-events.md#create-an-endpoint) Azure Digital Twins qui lie votre hub d’événements à votre instance Azure Digital Twins.

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. Créez un [itinéraire](concepts-route-events.md#create-an-event-route) dans Azure Digital Twins pour envoyer des événements de mise à jour de jumeaux à votre point de terminaison. Le filtre de cet itinéraire permet uniquement aux messages de mise à jour de jumeaux d’être transmis à votre point de terminaison.

    >[!NOTE]
    >Il existe actuellement un **problème connu** dans Cloud Shell affectant les groupes de commandes suivants : `az dt route`, `az dt model`, `az dt twin`.
    >
    >Pour le résoudre, exécutez `az login` dans Cloud Shell avant d’exécuter la commande, ou utilisez la [CLI locale](/cli/azure/install-azure-cli) au lieu de Cloud Shell. Pour plus d’informations, consultez [*résolution des problèmes : Problèmes connus dans Azure Digital Twins*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Avant de poursuivre, prenez note de votre *espace de noms Event Hubs* et de votre *groupe de ressources*, car vous les utiliserez à nouveau pour créer un autre Event Hub plus loin dans cet article.

## <a name="create-a-function-in-azure"></a>Créer une fonction dans Azure

Ensuite, vous utiliserez Azure Functions pour créer une **fonction déclenchée par Event Hubs** à l’intérieur d’une application de fonction. Vous pouvez utiliser l’application de fonction créée dans le tutoriel de bout en bout ([*Tutoriel : Connecter une solution de bout en bout*](./tutorial-end-to-end.md)), ou votre propre fonction. 

Cette fonction convertit ces événements de mise à jour de jumeaux de leur forme d’origine en tant que documents de correctifs JSON en objets JSON, contenant uniquement des valeurs mises à jour et ajoutées à partir de vos jumeaux.

Pour plus d’informations sur l’utilisation d’Event Hubs avec Azure Functions, consultez [*Déclencheur Azure Event Hubs pour Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Dans votre application de fonction publiée, ajoutez une nouvelle fonction appelée **ProcessDTUpdatetoTSI** avec le code suivant.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>Vous devrez peut-être ajouter les packages à votre projet à l’aide de la commande `dotnet add package` ou du gestionnaire de package NuGet de Visual Studio.

Ensuite, **publiez** la nouvelle fonction Azure. Pour obtenir des instructions sur la façon de procéder, consultez [*Procédure : Configurer une fonction Azure pour le traitement des données*](how-to-create-azure-function.md#publish-the-function-app-to-azure).

Par la suite, cette fonction enverra les objets JSON qu’elle crée à un deuxième hub d’événements que vous connecterez à Time Series Insights. Vous créerez ce hub d’événements dans la section suivante.

Plus tard, vous définirez également certaines variables d’environnement qui seront utilisées par cette fonction pour se connecter à vos propres instances Event Hub.

## <a name="send-telemetry-to-an-event-hub"></a>Envoyer des données de télémétrie à un Event Hub

Vous allez maintenant créer un deuxième Event Hub et configurer votre fonction pour diffuser sa sortie vers cet Event Hub. Cet Event Hub est ensuite connecté à Time Series Insights.

### <a name="create-an-event-hub"></a>Créer un hub d’événements

Pour créer le second Event Hub, vous pouvez utiliser les instructions Azure CLI ci-dessous ou utiliser le portail Azure : [*Démarrage rapide : Créer un hub d’événements avec le portail Azure*](../event-hubs/event-hubs-create.md).

1. Préparez votre *espace de noms Event Hubs* et votre nom de *groupe de ressources*, comme plus haut dans cet article

2. Créez un hub d’événements. Spécifiez un nom pour le hub d’événements.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Créez une [règle d’autorisation](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) avec les autorisations d’envoi et de réception. Spécifiez un nom pour la règle.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Configurer votre fonction

Ensuite, vous devez définir des variables d’environnement dans votre application de fonction à partir d’une version antérieure, contenant les chaînes de connexion pour les instances Event Hub que vous avez créées.

### <a name="set-the-twins-event-hub-connection-string"></a>Définir la chaîne de connexion Event Hub de Twins

1. Récupérez la [chaîne de connexion Event Hub](../event-hubs/event-hubs-get-connection-string.md) de Twins à l’aide des règles d’autorisation que vous avez créées ci-dessus pour le Hub de Twins.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Utilisez la valeur *primaryConnectionString* du résultat pour créer un paramètre d’application dans votre application de fonction qui contient votre chaîne de connexion :

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Définir la chaîne de connexion à Event Hub de Time Series Insights

1. Récupérez la [chaîne de connexion Event Hub](../event-hubs/event-hubs-get-connection-string.md) de TSI à l’aide des règles d’autorisation que vous avez créées ci-dessus pour le Hub de Time Series Insights :

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Utilisez la valeur *primaryConnectionString* du résultat pour créer un paramètre d’application dans votre application de fonction qui contient votre chaîne de connexion :

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Créer et connecter une instance Time Series Insights

Ensuite, vous allez configurer une instance Time Series Insights pour recevoir les données de votre second hub d’événements (TSI). Suivez les étapes ci-dessous, et pour plus d’informations sur ce processus, consultez le [*Tutoriel : Configurer un environnement Azure Time Series Insights Gen2 avec paiement à l’utilisation*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. Dans le portail Azure, commencez à créer un environnement Time Series Insights. 
    1. Sélectionnez le niveau tarifaire **Gen2(L1)** .
    2. Vous devez choisir un **ID de série chronologique** pour cet environnement. Votre ID de série chronologique peut comporter jusqu’à trois valeurs que vous allez utiliser pour rechercher vos données dans Time Series Insights. Pour ce tutoriel, vous pouvez sélectionner **$dtId**. Pour plus d’informations sur la sélection d’une valeur d’ID, consultez [*Meilleures pratiques pour choisir un ID de série chronologique*](../time-series-insights/how-to-select-tsid.md).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="L’expérience utilisateur du portail de création pour un environnement de Time Series Insights. Sélectionnez vos abonnement, groupe de ressources et emplacement dans les listes déroulantes respectives, puis nommez votre environnement." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="L’expérience utilisateur du portail de création pour un environnement de Time Series Insights. Le niveau tarifaire Gen2(L1) est sélectionné et le nom de la propriété de l’ID de la série chronologique est $dtId" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Sélectionnez **Suivant : Source de l’événement** et sélectionnez les informations du hub d’événements TSI mentionné précédemment. Vous devrez également créer un groupe de consommateurs Event Hubs.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="L’expérience utilisateur du portail de création pour une source d’événements de l’environnement Time Series Insights. Vous créez une source d’événement avec les informations Event Hub ci-dessus. Vous créez également un groupe de consommateurs." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Commencer à envoyer des données IoT à Azure Digital Twins

Pour commencer à envoyer des données à Time Series Insights, vous devez commencer à mettre à jour les propriétés dans Azure Digital Twins avec des valeurs de données variables. Utilisez la commande [az dt twin update](/cli/azure/ext/azure-iot/dt/twin#ext-azure-iot-az-dt-twin-update).

Si vous utilisez le Tutoriel de bout en bout ([*Tutoriel : Connecter une solution de bout en bout*](tutorial-end-to-end.md)) pour faciliter la configuration de l’environnement, vous pouvez commencer à envoyer des données IoT simulées en exécutant le projet *DeviceSimulator* de l’exemple. Les instructions correspondantes se trouvent dans la section [*Configurer et exécuter la simulation*](tutorial-end-to-end.md#configure-and-run-the-simulation) du tutoriel.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualiser vos données dans Time Series Insights

À présent, les données devraient être transmises à votre instance Time Series Insights, prêtes à être analysées. Suivez les étapes ci-dessous pour explorer les données entrantes.

1. Ouvrez votre environnement Time Series Insights dans le [portail Azure](https://portal.azure.com) (vous pouvez rechercher le nom de votre environnement dans la barre de recherche du portail). Accédez à l’*URL de l’Explorateur Time Series Insights* affichée dans la vue d’ensemble de l’instance.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Sélectionnez l’URL de l’explorateur de Time Series Insights sous l’onglet Vue d’ensemble de votre environnement Time Series Insights":::

2. Dans l’Explorateur, vous verrez vos trois jumeaux d’Azure Digital Twins sur la gauche. Sélectionnez _**thermostat67**_, puis **temperature** et appuyez sur **add**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Sélectionnez **thermostat67**, puis **temperature** et appuyez sur **add**":::

3. Vous devriez maintenant voir les valeurs de température initiales de votre thermostat, comme indiqué ci-dessous. Cette même lecture de température est mise à jour pour *room21* et *floor1*, et vous pouvez visualiser ces flux de données en tandem.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Les données de température initiales sont représentées sous forme de graphique dans l’explorateur TSI. Il s’agit d’une ligne de valeurs aléatoires comprises entre 68 et 85":::

4. Si vous autorisez l’exécution de la simulation pendant une durée plus longue, votre visualisation ressemble à ce qui suit :
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Les données de température pour chaque représentation sont représentées sous forme de graphique en trois lignes parallèles de couleurs différentes.":::

## <a name="next-steps"></a>Étapes suivantes

Les jumeaux numériques sont stockés par défaut sous la forme d’une hiérarchie plate dans Time Series Insights, mais ils peuvent être enrichis avec des informations de modèle et une hiérarchie à plusieurs niveaux pour l’organisation. Pour en savoir plus sur ce processus, lisez : 

* [*Tutoriel : Définir et appliquer un modèle*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Vous pouvez écrire une logique personnalisée pour fournir automatiquement ces informations à l’aide des données de modèle et de graphique déjà stockées dans Azure Digital Twins. Pour en savoir plus sur la gestion, la mise à niveau et la récupération d’informations à partir du graphique de jumeaux, consultez les références suivantes :

* [*Guide pratique : Gérer un jumeau numérique*](./how-to-manage-twin.md)
* [*Guide pratique : Interroger le graphe de jumeaux*](./how-to-query-graph.md)