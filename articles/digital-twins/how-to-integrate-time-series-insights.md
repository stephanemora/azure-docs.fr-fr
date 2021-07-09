---
title: Intégrer à Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer des itinéraires d’événements d’Azure Digital Twins à Azure Time Series Insights.
author: baanders
ms.author: baanders
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b4dad7e5de44701b946c3d7b9412d5d3095c5736
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110615795"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Intégrer Azure Digital Twins avec Azure Time Series Insights

Dans cet article, vous allez apprendre à intégrer Azure Digital Twins avec [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

La solution décrite dans cet article vous permettra de collecter et d’analyser les données d’historique relatives à votre solution IoT. Azure Digital Twins est idéal pour alimenter des données dans Time Series Insights, car il vous permet de mettre en corrélation plusieurs flux de données et de normaliser vos informations avant de les envoyer à Time Series Insights.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer une relation avec Time Series Insights, vous devez configurer les ressources suivantes :
* Un **IoT Hub**. Pour obtenir des instructions, consultez la section [Créer un hub IoT](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) du démarrage rapide *Envoyer la télémétrie d’IoT Hub*.
* Une **instance d’Azure Digital Twins**. Pour obtenir des instructions, consultez [Procédure : Configurer une instance Azure Digital Twins et l’authentification](./how-to-set-up-instance-portal.md).
* Un **modèle et un jumeau dans l’instance Azure Digital Twins**. Vous devez mettre à jour les informations du jumeau plusieurs fois pour voir ces données suivies dans Time Series Insights. Pour obtenir des instructions, consultez la section [Ajouter un modèle et un jumeau](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) de l’article *Guide pratique : Ingérer IoT Hub*.

> [!TIP]
> Dans cet article, les valeurs de jumeau changeantes qui sont affichées dans Time Series Insights sont mises à jour manuellement par souci de simplicité. Toutefois, si vous souhaitez suivre cet article avec des données simulées en temps réel, vous pouvez configurer une fonction Azure qui met à jour les jumeaux en fonction des événements de télémétrie IoT à partir d’un appareil simulé. Pour obtenir des instructions, consultez [Guide pratique : Ingérer les données d’IoT Hub](how-to-ingest-iot-hub-data.md), notamment les dernières étapes pour exécuter le simulateur d’appareil et valider le fonctionnement du workflow.
>
> Plus tard, recherchez un autre conseil pour vous montrer où démarrer l’exécution du simulateur d’appareil et faire en sorte que vos fonctions Azure mettent à jour automatiquement les jumeaux, au lieu d’envoyer des commandes de mise à jour de jumeau manuellement.


## <a name="solution-architecture"></a>Architecture de solution

Vous allez attacher Time Series Insights à Azure Digital Twins via le chemin d’accès ci-dessous.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagramme services Azure dans un scénario de bout en bout, mise en surbrillance de Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Créer un espace de noms du hub d’événements

Avant de créer les hubs d’événements, créez d’abord un espace de noms Event Hub, qui recevra les événements de notre instance Azure Digital Twins. Vous pouvez utiliser les instructions Azure CLI ci-dessous ou utiliser le portail Azure : [Démarrage rapide : Créer un hub d’événements avec le portail Azure](../event-hubs/event-hubs-create.md). Pour connaître les régions qui prennent en charge Event Hubs, consultez [Produits Azure disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-Event-Hubs-namespace> --resource-group <your-resource-group> --location <region>
```

> [!TIP]
> Si vous recevez une erreur indiquant `BadRequest: The specified service namespace is invalid.`, vérifiez que le nom que vous avez choisi pour votre espace de noms répond aux conditions d’attribution de noms décrites dans ce document de référence : [Créer un espace de noms](/rest/api/servicebus/create-namespace).

Vous utiliserez cet espace de noms Event hubs pour conserver les deux hubs d’événements qui sont nécessaires pour cet article :

  1. **Hub Twins** - Instance Event Hub pour recevoir les événements de changement de jumeau
  2. **Hub Time Series** - Instance Event Hub pour diffuser des événements à Time Series Insights

Les sections suivantes vous guideront dans la création et la configuration de ces hubs dans votre espace de noms Event Hub.

## <a name="create-twins-hub"></a>Créer un hub Twins

La première instance Event Hub que vous allez créer dans cet article est le **Hub Twins**. Cet Event Hub recevra les événements de changement de jumeau d’Azure Digital Twins.
Pour configurer le hub Twins, procédez comme suit dans cette section :

1. Créer le hub Twins
2. Créer une règle d’autorisation pour contrôler les autorisations sur le hub
3. Créer un point de terminaison dans Azure Digital Twins qui utilise la règle d’autorisation pour accéder au hub
4. Créer un itinéraire dans Azure Digital Twins qui envoie un événement de mise à jour de jumeau au point de terminaison et au hub Twins connecté
5. Définir la chaîne de connexion du hub Twins

Créez le **hub Twins** avec la commande CLI suivante. Spécifiez un nom pour votre hub Twins.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Créer une règle d’autorisation pour le hub Twins

Créez une [règle d’autorisation](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az_eventhubs_eventhub_authorization_rule_create) avec les autorisations d’envoi et de réception. Spécifiez un nom pour la règle.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Créer un point de terminaison pour le hub Twins

Créez un [point de terminaison](concepts-route-events.md#create-an-endpoint) Azure Digital Twins qui lie votre hub d’événements à votre instance Azure Digital Twins. Spécifiez un nom pour le point de terminaison de votre hub Twins.

```azurecli-interactive
az dt endpoint create eventhub --dt-name <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-Event-Hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Créer un itinéraire d’événement hub Twins

Les instances Azure Digital Twins peuvent émettre des [événements de mise à jour des jumeaux](./concepts-event-notifications.md) chaque fois que l’état d’un jumeau est mis à jour. Dans cette section, vous allez créer un **itinéraire d’événement** Azure Digital Twins qui dirigera ces événements de mise à jour vers le hub Twins en vue d’un traitement ultérieur.

Créez un [itinéraire](concepts-route-events.md#create-an-event-route) dans Azure Digital Twins pour envoyer des événements de mise à jour de jumeaux à votre point de terminaison à partir d’en haut. Le filtre de cet itinéraire permet uniquement aux messages de mise à jour de jumeaux d’être transmis à votre point de terminaison. Spécifiez un nom pour l’itinéraire d’événement du hub Twins.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Obtient la chaîne de connexion du hub Twins

Récupérez la [chaîne de connexion Event Hub](../event-hubs/event-hubs-get-connection-string.md) de Twins à l’aide des règles d’autorisation que vous avez créées ci-dessus pour le Hub de Twins.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Prenez note de la valeur **primaryConnectionString** du résultat pour configurer le paramètre d’application du hub Twins plus loin dans cet article.

## <a name="create-time-series-hub"></a>Créer un hub Time Series

Le deuxième Event Hub que vous allez créer dans cet article est le **hub Time Series**. Il s’agit d’un Event Hub qui diffusera les événements Azure Digital Twins sur Time Series Insights.
Pour configurer le hub Time Series, effectuez les étapes suivantes :

1. Créer le hub Time Series
2. Créer une règle d’autorisation pour contrôler les autorisations sur le hub
3. Obtient la chaîne de connexion du hub Time Series

Plus tard, lorsque vous créerez l’instance Time Series Insights, vous connecterez ce hub Time Series en tant que source d’événements pour l’instance Time Series Insights.

Créez le **hub Time Series** à l’aide de la commande suivante. Spécifiez un nom pour le hub Time Series.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Créer une règle d’autorisation de hub Time Series

Créez une [règle d’autorisation](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az_eventhubs_eventhub_authorization_rule_create) avec les autorisations d’envoi et de réception. Spécifiez un nom pour la règle d’authentification du hub Time Series.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Obtient la chaîne de connexion du hub Time Series

Récupérez la [chaîne de connexion du hub Time Series](../event-hubs/event-hubs-get-connection-string.md) à l’aide des règles d’autorisation que vous avez créées ci-dessus pour le hub Time Series Insights :

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Prenez note de la valeur **primaryConnectionString** du résultat pour configurer le paramètre d’application du hub Time Series plus loin dans cet article.

Notez également les valeurs suivantes pour les utiliser ultérieurement pour créer une instance Time Series Insights.
* Espace de noms du hub d’événements
* Nom du hub Time Series
* Règle d’authentification du hub Time Series

## <a name="create-a-function"></a>Créer une fonction

Dans cette section, vous créez une fonction Azure qui convertit les événements de mise à jour de jumeaux de leur forme d’origine en tant que documents de correctifs JSON en objets JSON, contenant uniquement des valeurs mises à jour et ajoutées à partir de vos jumeaux.

### <a name="step-1-create-function-app"></a>Étape 1 : Créer une application de fonction

Tout d’abord, créez un projet d’application de fonction dans Visual Studio. Pour obtenir des instructions sur la façon de procéder, consultez la section [Créer une application de fonction dans Visual Studio](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) de l’article *Guide pratique : Configurer une fonction pour le traitement des données*.

### <a name="step-2-add-a-new-function"></a>Étape 2 : Ajouter une nouvelle fonction

Créez une nouvelle fonction Azure appelée *ProcessDTUpdatetoTSI.cs* pour mettre à jour les événements de télémétrie de l’appareil vers Time Series Insights. Le type de fonction sera **Déclencheur Event Hub**.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Capture d’écran de Visual Studio pour créer une nouvelle fonction Azure de type déclencheur Event Hub.":::

### <a name="step-3-fill-in-function-code"></a>Étape 3 : Renseigner le code de la fonction

Ajoutez les packages suivants à votre projet :
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Remplacez le code du fichier *ProcessDTUpdatetoTSI.cs* par le code suivant :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Enregistrez le code de votre fonction.

### <a name="step-4-publish-the-function-app-to-azure"></a>Étape 4 : Publier l’application de fonction dans Azure

Publiez le projet avec la fonction *ProcessDTUpdatetoTSI.cs* dans une application de fonction dans Azure.

Pour obtenir des instructions sur la façon de procéder, consultez la section [Publier l’application de fonction dans Azure](how-to-create-azure-function.md#publish-the-function-app-to-azure) de l’article *Guide pratique : configurer une fonction pour le traitement des données*.

Enregistrez le nom de l’application de fonction pour l’utiliser ultérieurement pour configurer les paramètres de l’application pour les deux hubs d’événements.

### <a name="step-5-security-access-for-the-function-app"></a>Étape 5 : Accès de sécurité pour l’application de fonction

Ensuite, **attribuez un rôle d’accès** à la fonction et **configurez les paramètres de l’application** afin qu’elle puisse accéder à votre instance d’Azure Digital Twins. Pour obtenir des instructions sur la façon de procéder, consultez la section [Configurer l’accès de sécurité pour l’application de fonction](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) de l’article *Guide pratique : Configurer une fonction pour le traitement des données*.

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Étape 6 : Configurer les paramètres d’application pour les deux hubs d’événements

Ensuite, vous allez ajouter des variables d’environnement dans les paramètres de l’application de fonction qui lui permettent d’accéder aux hubs Twins et Time Series.

Utilisez la valeur du hub Twins **primaryConnectionString** que vous avez enregistrée précédemment pour créer un paramètre d’application dans votre application de fonction qui contient la chaîne de connexion du hub Twins :

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" --resource-group <your-resource-group> --name <your-App-Service-function-app-name>
```

Utilisez la valeur du hub Time Series **primaryConnectionString** que vous avez enregistrée précédemment pour créer un paramètre d’application dans votre application de fonction qui contient la chaîne de connexion du hub Time Series :

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" --resource-group <your-resource-group> --name <your-App-Service-function-app-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Créer et connecter une instance Time Series Insights

Dans cette section, vous allez configurer une instance Time Series Insights pour recevoir des données de votre hub Time Series. Pour plus d’informations sur ce processus, consultez le [Tutoriel : Configurer un environnement Azure Time Series Insights Gen2 avec paiement à l’utilisation](../time-series-insights/tutorial-set-up-environment.md). Suivez les étapes ci-dessous pour créer un environnement Time Series Insights.

1. Dans le [portail Azure](https://portal.azure.com), recherchez *Environnements Time Series Insights*, puis cliquez sur le bouton **Ajouter**. Choisissez les options suivantes pour créer l’environnement Time Series.

    * **Abonnement** : Choisissez votre abonnement.
        - **Groupe de ressources** : Choisissez votre groupe de ressources.
    * **Nom de l’environnement** : Spécifiez un nom pour votre environnement Time Series.
    * **Emplacement** - Choisissez un emplacement.
    * **Niveau** : choisissez le niveau tarifaire **Gen2 (L1)** .
    * **Nom de propriété** - Entrez **$dtId** (Pour plus d’informations sur la sélection d’une valeur d’ID, consultez [Meilleures pratiques pour choisir un ID de série chronologique](../time-series-insights/how-to-select-tsid.md)).
    * **Nom du compte de stockage** : Spécifiez un nom de compte de stockage.
    * **Activer le magasin chaud** : laissez ce champ défini sur *Oui*.

    Vous pouvez laisser les valeurs par défaut pour les autres propriétés de cette page. Sélectionnez le bouton **Suivant : Source de l’événement >** .

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Capture d’écran du portail Azure montrant comment créer un environnement Time Series Insights (partie 1/3)" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Capture d’écran du portail Azure montrant comment créer un environnement Time Series Insights (partie 2/3)" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Dans l’onglet *Source de l’événement*, choisissez les champs suivants :

   * **Créer une source d’événement ?** - Choisissez *Oui*.
   * **Type de source** : Choisissez *Event Hub*.
   * **Nom** : Spécifiez un nom pour votre source d’événement.
   * **Abonnement** : Choisissez votre abonnement Azure.
   * **Espace de noms Event Hub** : Choisissez l’espace de noms que vous avez créé précédemment dans cet article.
   * **Nom Event Hub** : Choisissez le nom du **hub Time Series** que vous avez créé précédemment dans cet article.
   * **Nom de la stratégie d’accès Event Hub** : Choisissez la *règle d’authentification hub Time Series* que vous avez créée précédemment dans cet article.
   * **Groupe de consommateurs Event Hub** : Sélectionnez *Nouveau* et spécifiez un nom pour votre groupe de consommateurs Event Hub. Sélectionnez ensuite *Ajouter*.
   * **Nom de propriété** : Laissez ce champ vide.
    
    Choisissez le bouton **Vérifier + créer** pour passer en revue tous les détails. Ensuite, sélectionnez à nouveau le bouton **Vérifier + créer** pour créer l’environnement Time Series.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Capture d’écran du portail Azure montrant comment créer un environnement Time Series Insights (partie 3/3)" lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Envoyer des données IoT à Azure Digital Twins

Pour commencer à envoyer des données à Time Series Insights, vous devez commencer à mettre à jour les propriétés dans Azure Digital Twins avec des valeurs de données variables.

Utilisez la commande CLI suivante pour mettre à jour la propriété *Temperature* sur le jumeau thermostat67 que vous avez ajouté à votre instance dans la [section Prérequis](#prerequisites).

```azurecli-interactive
az dt twin update --dt-name <your-Azure-Digital-Twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Répétez la commande au moins 4 fois avec différentes valeurs de température** pour créer plusieurs points de données qui peuvent être observés plus tard dans l’environnement Time Series Insights.

> [!TIP]
> Si vous souhaitez terminer cet article avec des données simulées en temps réel au lieu de mettre à jour manuellement les valeurs numériques, commencez par vous assurer que vous avez appliqué le Conseil de la section [Conditions préalables](#prerequisites) pour configurer une fonction Azure qui met à jour les jumeaux à partir d’un appareil simulé.
Après cela, vous pouvez exécuter l’appareil pour commencer à envoyer des données simulées et mettre à jour votre jumeau par le biais de ce flux de données.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualiser vos données dans Time Series Insights

À présent, les données devraient être transmises à votre instance Time Series Insights, prêtes à être analysées. Suivez les étapes ci-dessous pour explorer les données entrantes.

1. Dans le [portail Azure](https://portal.azure.com), recherchez le nom de l’environnement Time Series que vous avez créé précédemment. Dans les options de menu sur la gauche, sélectionnez *Vue d’ensemble* pour afficher l’URL de l’*Explorateur Time Series Insights*. Sélectionnez l’URL pour afficher les modifications de température reflétées dans l’environnement Time Series Insights.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Capture d’écran du portail Azure montrant l’URL de l’explorateur Time Series Insights sous l’onglet de présentation de l’environnement Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. Dans l’Explorateur, vous verrez vos trois instances d’Azure Digital Twins sur la gauche. Sélectionnez le jumeau thermostat67, choisissez la propriété *Température*, puis sélectionnez **Ajouter**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Capture d’écran de l’explorateur Time Series Insights avec mise en évidence des étapes de sélection de thermostat67, de la propriété Temperature et du bouton Ajouter" lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Vous devriez maintenant voir les valeurs de température initiales de votre thermostat, comme indiqué ci-dessous. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Capture d’écran de l’explorateur Time Series Insights avec les données de température initiales, montrant une ligne de valeurs aléatoires comprises entre 68 et 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Si vous autorisez l’exécution d’une simulation pendant une durée plus longue, votre visualisation ressemble à ce qui suit :

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Capture d’écran de l’explorateur Time Series Insights où les données de température pour chaque jumeau sont représentées sous forme de trois lignes parallèles de couleurs différentes" lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Étapes suivantes

Les jumeaux numériques sont stockés par défaut sous la forme d’une hiérarchie plate dans Time Series Insights, mais ils peuvent être enrichis avec des informations de modèle et une hiérarchie à plusieurs niveaux pour l’organisation. Pour en savoir plus sur ce processus, lisez : 

* [Tutoriel : Définir et appliquer un modèle](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Vous pouvez écrire une logique personnalisée pour fournir automatiquement ces informations à l’aide des données de modèle et de graphique déjà stockées dans Azure Digital Twins. Pour en savoir plus sur la gestion, la mise à niveau et la récupération d’informations à partir du graphique de jumeaux, consultez les références suivantes :

* [Guide pratique : Gérer un jumeau numérique](./how-to-manage-twin.md)
* [Guide pratique pour interroger le graphique de jumeaux](./how-to-query-graph.md)