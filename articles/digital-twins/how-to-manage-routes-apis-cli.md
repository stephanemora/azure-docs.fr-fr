---
title: Gérer les points de terminaison et les itinéraires (API et CLI)
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer et gérer les points de terminaison et les itinéraires d’événements pour les données d’Azure Digital Twins.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e6b35031d976a11bdac6f38d74f9e02a0fc83302
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936306"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Gérer les points de terminaison et les itinéraires dans Azure Digital Twins (API et CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Dans Azure Digital Twins, vous pouvez acheminer les [notifications d’événements](how-to-interpret-event-data.md) vers des services en aval ou des ressources de calcul connectées. Pour ce faire, vous devez d’abord configurer des **points de terminaison** qui peuvent recevoir les événements. Vous pouvez ensuite créer [**des itinéraires d’événements**](concepts-route-events.md) qui spécifient quels événements générés par Azure Digital Twins sont remis aux points de terminaison.

Cet article vous guide tout au long du processus de création de points de terminaison et de routes à l’aide des [API REST](/rest/api/azure-digitaltwins/), du [kit SDK .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client) et de l’[interface CLI Azure Digital Twins](how-to-use-cli.md).

Vous pouvez également gérer les points de terminaison et les routes à l’aide du [portail Azure](https://portal.azure.com). Pour obtenir une version de cet article qui utilise le portail à la place, consultez le [Tutoriel  *: Gérer les points de terminaison et les itinéraires (portail)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Prérequis

- Vous avez besoin d’un **compte Azure** (vous pouvez en définir un gratuitement [ici](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- Vous aurez besoin d’une **instance Azure Digital Twins** dans votre abonnement Azure. Si vous n’avez pas d’instance, vous pouvez en créer une en suivant les étapes décrites du [*Tutoriel : Configurer une instance et l’authentification*](how-to-set-up-instance-cli.md). Utilisez les valeurs suivantes du programme d’installation pour les utiliser plus loin dans cet article :
    - Nom de l’instance
    - Resource group

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Créer un point de terminaison pour Azure Digital Twins

Voici les types de points de terminaison pris en charge que vous pouvez créer pour votre instance :
* [Event Grid](../event-grid/overview.md)
* [Hubs d'événements](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Pour plus d’informations sur les différents points de terminaison, consultez [*Choisir entre les différents services de messagerie Azure*](../event-grid/compare-messaging-services.md).

Cette section explique comment créer ces points de terminaison à l’aide de l’interface de ligne de commande Azure (CLI). Vous pouvez également gérer les points de terminaison avec les [API de plan de contrôle DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Créer le point de terminaison

Une fois que vous avez créé les ressources du point de terminaison, vous pouvez les utiliser pour un point de terminaison Azure Digital Twins. Les exemples suivants montrent comment créer des points de terminaison à l’aide de la commande [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create) pour l’[interface CLI Azure Digital Twins](how-to-use-cli.md). Remplacez les espaces réservés dans les commandes par les informations de vos propres ressources.

Pour créer un point de terminaison Event Grid :

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Pour créer un point de terminaison Event Hubs (authentification basée sur une clé) :
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Pour créer un point de terminaison de rubrique Service Bus (authentification basée sur une clé) :
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Une fois ces commandes exécutées avec succès, la rubrique Event Grid, Event Hub ou Service Bus est disponible en tant que point de terminaison dans Azure Digital Twins, sous le nom que vous avez fourni avec l’argument `--endpoint-name`. Vous utiliserez généralement ce nom en tant que cible d’un **itinéraire d’événement** que vous allez créer [plus loin dans cet article](#create-an-event-route).

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Créer un point de terminaison avec une authentification basée sur l’identité

Vous pouvez également créer un point de terminaison avec une authentification basée sur l’identité pour utiliser le point de terminaison avec une [identité managée](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Cette option est uniquement disponible pour les points de terminaison de type Event Hub et Service Bus (elle n’est pas prise en charge pour Event Grid).

La commande CLI pour créer ce type de point de terminaison est indiquée ci-dessous. Vous aurez besoin des valeurs suivantes pour intégrer les espaces réservés dans la commande :
* ID de ressource Azure de votre instance Azure Digital Twins
* nom d’un point de terminaison
* type de point de terminaison
* espace de noms de la ressource de point de terminaison
* nom de la rubrique Event Hub ou Service Bus
* emplacement de votre instance Azure Digital Twins

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Créer un point de terminaison avec mise en file d’attente de lettres mortes

Lorsqu’un point de terminaison ne peut pas remettre un événement dans un laps de temps donné ou après avoir essayé de remettre l’événement un certain nombre de fois, il peut envoyer l’événement non remis à un compte de stockage. Ce processus est appelé **mise en file d’attente de lettres mortes**.

Les points de terminaison avec mise en file d’attente de lettres mortes peuvent être configurés à l’aide de l’[interface CLI](how-to-use-cli.md) ou des [API de plan de contrôle](how-to-use-apis-sdks.md#overview-control-plane-apis) Azure Digital Twins.

Pour en savoir plus sur la mise en file d’attente de lettres mortes, consultez [*Concepts : Routes d’événements*](concepts-route-events.md#dead-letter-events). Pour obtenir des instructions sur la configuration d’un point de terminaison avec une mise en file d’attente de lettres mortes, suivez le reste de cette section.

#### <a name="set-up-storage-resources"></a>Configurer des ressources de stockage

Avant de définir l’emplacement des messages non distribués, vous devez disposer d’un [compte de stockage](../storage/common/storage-account-create.md?tabs=azure-portal) avec un [conteneur](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) configurés dans votre compte Azure. 

Vous devrez fournir l’URI de ce conteneur au moment de créer le point de terminaison ultérieurement. L’emplacement de la file d’attente de lettres mortes est fourni au point de terminaison en tant qu’URI de conteneur avec un [jeton SAS](../storage/common/storage-sas-overview.md). Ce jeton a besoin de l’autorisation `write` pour le conteneur de destination dans le compte de stockage. L’**URI SAS de lettre morte** complète s’affichera au format : `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`.

Suivez les étapes ci-dessous pour configurer ces ressources de stockage dans votre compte Azure, afin de préparer la configuration de la connexion du point de terminaison dans la section suivante.

1. Suivez les étapes décrites dans [*Créer un compte de stockage*](../storage/common/storage-account-create.md?tabs=azure-portal) pour créer un **compte de stockage** dans votre abonnement Azure. Prenez note du nom du compte de stockage ; vous en aurez besoin plus tard.
2. Suivez les étapes décrites dans [*Créer un conteneur*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) pour créer un **conteneur** dans le nouveau compte de stockage. Notez le nom de conteneur ; vous en aurez besoin plus tard.
3. Ensuite, créez un **jeton SAS** pour votre compte de stockage que le point de terminaison peut utiliser pour y accéder. Commencez par accéder à votre compte de stockage dans le [portail Azure](https://ms.portal.azure.com/#home) (vous pouvez le rechercher par son nom dans la barre de recherche du portail).
4. Dans la page du compte de stockage, choisissez le lien _Signature d’accès partagé_ dans la barre de navigation de gauche afin de lancer la configuration du jeton SAP.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Page du compte de stockage dans le portail Azure" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Dans la page *Signature d’accès partagé*, sous *Services autorisés* et *Types de ressources autorisés*, sélectionnez les paramètres de votre choix. Vous devez sélectionner au moins une case dans chaque catégorie. Sous *Autorisations acceptées*, choisissez **Écriture** (vous pouvez également sélectionner d’autres autorisations si vous le souhaitez).
1. Définissez les valeurs de votre choix pour les autres paramètres.
1. Lorsque vous avez terminé, sélectionnez le bouton _Générer la chaîne de connexion et SAP_ pour générer le jeton SAP. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Page du compte de stockage dans le portail Azure, présentant toutes les sélections de paramètres pour générer un jeton SAP et mise en surbrillance du bouton Générer la chaîne de connexion et SAP" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Cela a pour effet de générer plusieurs valeurs de chaîne de connexion et SAP au bas de la même page, sous les sélections de paramètres. Faites défiler l’écran pour afficher les valeurs et utilisez l’icône de *copie dans le presse-papiers* pour copier la valeur du **jeton SAP**. Enregistrez-la en vue d’une utilisation ultérieure.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Copiez le jeton SAP à utiliser dans le secret des messages non distribués." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>Créer le point de terminaison de lettres mortes

Pour créer un point de terminaison avec mise en file d’attente de lettres mortes, ajoutez le paramètre de lettre morte suivant à la commande [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create) pour l’[interface CLI Azure Digital Twins](how-to-use-cli.md).

La valeur du paramètre représente l’**URI SAS de lettre morte** constitué du nom du compte de stockage, du nom du conteneur et du jeton SAP que vous avez collectés à la [section précédente](#set-up-storage-resources). Ce paramètre crée le point de terminaison avec l’authentification basée sur une clé.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Ajoutez ce paramètre à la fin des commandes de création de point de terminaison à partir de la section [*Créer le point de terminaison*](#create-the-endpoint) précédente pour créer un point de terminaison du type souhaité, avec mise en file d’attente de lettres mortes.

Vous pouvez également créer des points de terminaison de lettres mortes à l’aide des [API de plan de contrôle Azure Digital Twins](how-to-use-apis-sdks.md#overview-control-plane-apis) au lieu de l’interface CLI. Pour cela, consultez la [documentation DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) pour savoir comment structurer la demande et ajouter les paramètres de lettres mortes.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Créer un point de terminaison de lettres mortes avec une authentification basée sur l’identité

Vous pouvez également créer un point de terminaison de lettres mortes avec une authentification basée sur l’identité pour utiliser le point de terminaison avec une [identité managée](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Cette option est uniquement disponible pour les points de terminaison de type Event Hub et Service Bus (elle n’est pas prise en charge pour Event Grid).

Pour créer ce type de point de terminaison, utilisez la même commande CLI que précédemment pour [créer un point de terminaison avec une authentification basée sur l’identité](#create-an-endpoint-with-identity-based-authentication), avec un champ supplémentaire dans la charge utile JSON pour un élément `deadLetterUri`.

Voici les valeurs dont vous aurez besoin pour intégrer les espaces réservés dans la commande :
* ID de ressource Azure de votre instance Azure Digital Twins
* nom d’un point de terminaison
* type de point de terminaison
* espace de noms de la ressource de point de terminaison
* nom de la rubrique Event Hub ou Service Bus
* Détails de l’**URI SAS de lettre morte** : nom du compte de stockage, nom du conteneur
* emplacement de votre instance Azure Digital Twins

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>Schéma de stockage des messages

Une fois que le point de terminaison avec la file d’attente de lettres mortes est configuré, les messages associés sont stockés au format suivant dans votre compte de stockage :

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

Les messages en file d’attente de lettres mortes correspondent au schéma de l’événement d’origine qui a été conçu pour être remis à votre point de terminaison d’origine.

Voici un exemple de message mis en file d’attente de lettres mortes pour une [notification de création de jumeau](how-to-interpret-event-data.md#digital-twin-lifecycle-notifications) :

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Création d’un itinéraire d’événements

**Condition préalable** : Vous devez créer des points de terminaison comme décrit précédemment dans cet article avant de pouvoir passer à la création d’un itinéraire. Une fois que vos points de terminaison sont configurés, vous pouvez passer à la création d’un itinéraire d’événements.

> [!NOTE]
> Si vous avez récemment déployé vos points de terminaison, vérifiez que le déploiement est terminé **avant** de tenter de les utiliser pour un nouvel itinéraire d’événements. Si le déploiement de l’itinéraire échoue parce que les points de terminaison ne sont pas prêts, patientez quelques minutes, puis réessayez.
>
> Si vous créez un script pour ce flux, vous pouvez prendre cela en compte en intégrant un temps d’attente de 2-3 minutes pour permettre au service de point de terminaison d’achever le déploiement avant de passer à la configuration de l’itinéraire.

Pour envoyer concrètement des données d’Azure Digital Twins à un point de terminaison, vous devez définir un **itinéraire d’événement**. Les routes d'événements permettent de connecter le flux d’événements dans l’ensemble du système et aux services en aval.

Une définition d’itinéraire peut contenir les éléments suivants :
* nom de l’itinéraire que vous souhaitez utiliser ;
* nom du point de terminaison que vous souhaitez utiliser ;
* filtre définissant les événements envoyés au point de terminaison. 

À défaut de nom d’itinéraire, aucun message n’est acheminé en dehors d’Azure Digital Twins. S’il existe un nom d’itinéraire et que le filtre est `true`, tous les messages sont acheminés vers le point de terminaison. Si un nom d’itinéraire et un filtre différent sont ajoutés, les messages sont filtrés en fonction du filtre.

Un itinéraire doit permettre la sélection de plusieurs notifications et types d’événements. 

Les routes d'événements peuvent être créées à l’aide des API de plan de données [**EventRoutes**](/rest/api/digital-twins/dataplane/eventroutes) ou des commandes CLI [**az dt route**](/cli/azure/ext/azure-iot/dt/route) Azure Digital Twins. Le reste de cette section décrit le processus de création.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Créer des routes avec les API et le Kit de développement logiciel (SDK) C#

Une des méthodes permettant de définir des routes d’événements consiste à utiliser les [API de plan de données](how-to-use-apis-sdks.md#overview-data-plane-apis). Les exemples fournis dans cet article utilisent le [Kit de développement logiciel (SDK) C#](/dotnet/api/overview/azure/digitaltwins/client).

`CreateOrReplaceEventRouteAsync` est l’appel du kit de développement logiciel (SDK) utilisé pour ajouter un itinéraire d’événement. Voici un exemple de son utilisation :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Toutes les fonctions du Kit de développement logiciel (SDK) sont disponibles en versions synchrone et asynchrone.

#### <a name="event-route-sample-sdk-code"></a>Exemple de code SDK d’itinéraire d’événement

L’exemple de méthode suivant montre comment créer, lister et supprimer une route d’événement avec le SDK C# :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Créer des routes avec l’interface CLI

Vous pouvez également gérer les routes à l’aide des commandes [az dt route](/cli/azure/ext/azure-iot/dt/route) pour l’interface CLI Azure Digital Twins. 

Pour plus d’informations sur l’utilisation de l’interface CLI et sur les commandes disponibles, consultez le [*Tutoriel : utiliser l’interface CLI Azure Digital Twins*](how-to-use-cli.md).

## <a name="filter-events"></a>Filtrer les événements

Sans filtrage, les points de terminaison reçoivent un grand nombre d’événements d’Azure Digital Twins :
* télémétrie déclenchée par des [jumeaux numériques](concepts-twins-graph.md) à l’aide de l’API de service d’Azure Digital Twins ;
* notifications de changement de propriété de jumeau, déclenchées par des modifications des propriétés d’un jumeau dans l’instance Azure Digital Twins ;
* Événements de cycle de vie déclenchés lors de la création ou de la suppression de jumeaux ou de relations

Vous pouvez limiter les événements envoyés en ajoutant un **filtre** à un point de terminaison sur votre itinéraire d’événement.

>[!NOTE]
> Les filtres **respectent la casse** et doivent correspondre à la casse de la charge utile. 
>
> Pour les filtres de télémétrie, cela signifie que la casse doit correspondre à celle de la télémétrie envoyée par l’appareil, pas nécessairement à celle définie dans le modèle du jumeau. 

Pour ajouter un filtre, vous pouvez envoyer une demande PUT à *https://{Your-azure-digital-twins-hostname}/eventRoutes/{event-route-name}?api-version=2020-10-31* avec le corps suivant :

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Voici les filtres d’itinéraire pris en charge. Utilisez les détails de la colonne *Filtrer le schéma du texte* pour remplacer l’espace réservé `<filter-text>` dans le corps de la requête ci-dessus.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les différents types de messages d’événements que vous pouvez recevoir :
* [*Guide pratique pour interpréter des données d’événement*](how-to-interpret-event-data.md)
