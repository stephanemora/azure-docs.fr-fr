---
title: Gérer les points de terminaison et les itinéraires
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer et gérer les points de terminaison et les itinéraires d’événements pour les données Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 7/30/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4e9bfa2dc340f567a6c2b7c4ab5d45cfeaa41e6c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661010"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Gérer les points de terminaison et les itinéraires dans Azure Digital Twins

Dans Azure Digital Twins, vous pouvez acheminer les [notifications d’événements](concepts-event-notifications.md) vers des services en aval ou des ressources de calcul connectées. Pour ce faire, vous devez d’abord configurer des **points de terminaison** qui peuvent recevoir les événements. Vous pouvez ensuite créer des [routes d’événements](concepts-route-events.md) qui spécifient quels événements générés par Azure Digital Twins doivent être envoyés aux points de terminaison.

Cet article vous guide tout au long du processus de création de points de terminaison et de routes à l’aide du [portail Azure](https://portal.azure.com), des[API REST](/rest/api/azure-digitaltwins/), du [kit SDK .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) et de l’instance [CLI Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

## <a name="prerequisites"></a>Prérequis

* Vous aurez besoin d’un **compte Azure**, qui [peut être configuré gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Vous aurez besoin d’une **instance Azure Digital Twins** dans votre abonnement Azure. Si vous n’avez pas encore d’instance, vous pouvez en créer une en suivant les étapes décrites dans [Configurer une instance et l’authentification](how-to-set-up-instance-portal.md). Utilisez les valeurs suivantes du programme d’installation pour les utiliser plus loin dans cet article :
    - Nom de l’instance
    - Resource group

Vous trouverez ces informations dans le [portail Azure](https://portal.azure.com) après avoir configuré votre instance. Connectez-vous au portail et recherchez le nom de votre instance dans la barre de recherche du portail.
 
:::image type="content" source="media/how-to-manage-routes/search-field-portal.png" alt-text="Capture d’écran de la barre de recherche du portail Azure." lightbox="media/how-to-manage-routes/search-field-portal.png":::

Sélectionnez votre instance parmi les résultats pour afficher ces détails dans la page Vue d’ensemble de votre instance :

:::image type="content" source="media/how-to-manage-routes/instance-details.png" alt-text="Capture d’écran montrant la page de présentation d’une instance Azure Digital Twins dans le portail Azure. Le nom et le groupe de ressources sont mis en surbrillance.":::

Suivez les instructions ci-dessous si vous avez l’intention d’utiliser l’interface CLI Azure tout en suivant ce guide.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Créer un point de terminaison pour Azure Digital Twins

Voici les types de points de terminaison pris en charge que vous pouvez créer pour votre instance :
* [Event Grid](../event-grid/overview.md) 
* [Hubs d'événements](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Pour plus d’informations sur les différents points de terminaison, consultez [Choisir entre les différents services de messagerie Azure](../event-grid/compare-messaging-services.md).

Cette section explique comment créer un point de terminaison à l’aide du[portail Azure](https://portal.azure.com) ou de l’interface [CLI Azure](/cli/azure/dt/endpoint?view=azure-cli-latest&preserve-view=true). Vous pouvez également gérer les points de terminaison avec les [API de plan de contrôle DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints).

### <a name="prerequisite-create-endpoint-resources"></a>Condition préalable : Créer des ressources de point de terminaison

Pour lier un point de terminaison à Azure Digital Twins, la rubrique Event Hub, Event Grid ou Service Bus que vous utilisez pour le point de terminaison doit pré-exister.

Utilisez le graphique suivant pour identifier les ressources qui doivent être configurées avant de créer votre point de terminaison.

| Type de point de terminaison | Ressources requises (liées aux instructions de création) |
| --- | --- |
| Point de terminaison Event Grid | [Rubrique Event Grid](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Point de terminaison Event Hubs | [Espace de noms&nbsp;Event&nbsp;Hubs](../event-hubs/event-hubs-create.md)<br/><br/>[hub d’événements](../event-hubs/event-hubs-create.md)<br/><br/>(Facultatif) [Règle d’autorisation](../event-hubs/authorize-access-shared-access-signature.md) pour l’authentification basée sur une clé | 
| Point de terminaison de Service Bus | [Espace de noms Service Bus](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Rubrique Service Bus](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (Facultatif) [Règle d’autorisation](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) pour l’authentification basée sur une clé|

### <a name="create-the-endpoint"></a>Créer le point de terminaison 

Une fois que vous avez créé les ressources du point de terminaison, vous pouvez les utiliser pour un point de terminaison Azure Digital Twins. 

# <a name="portal"></a>[Portail](#tab/portal) 

Pour créer un point de terminaison, accédez à la page de votre instance dans le [portail Azure](https://portal.azure.com) (vous pouvez rechercher l’instance en entrant son nom dans la barre de recherche du portail).

1. Dans le menu de l’instance, sélectionnez _Points de terminaison_. Ensuite, à partir de la page *Points de terminaison* qui suit, sélectionnez *+ Créer un point de terminaison*. Cette opération ouvre la page *Créer un point de terminaison*, dans laquelle vous allez remplir les champs dans les étapes suivantes.

    :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-grid.png" alt-text="Capture d’écran de la création d’un point de terminaison de type Event Grid dans le portail Azure" lightbox="media/how-to-manage-routes/create-endpoint-event-grid.png":::

1. Entrez le **nom** de votre point de terminaison, puis choisissez le **type de point de terminaison**.

1. Spécifiez les autres informations requises pour votre type de point de terminaison, y compris votre abonnement et les ressources du point de terminaison décrites [ci-dessus](#prerequisite-create-endpoint-resources).
    1. Pour les points de terminaison Event Hub et Service Bus uniquement, vous devez sélectionner un **type d’authentification**. Vous pouvez utiliser l’authentification basée sur une clé avec une règle d’autorisation pré-créée ou une authentification basée sur l’identité si vous utilisez le point de terminaison avec une [identité managée](concepts-security.md#managed-identity-for-accessing-other-resources) pour votre instance Azure Digital Twins. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Capture d’écran de la création d’un point de terminaison de type Event Hub dans le portail Azure" lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Terminez la création de votre point de terminaison en sélectionnant _Enregistrer_.

>[!IMPORTANT]
> Pour pouvoir utiliser correctement l’authentification basée sur l’identité pour votre point de terminaison, vous devez créer une identité managée pour votre instance en suivant les étapes décrites dans [Acheminer les événements avec une identité managée](how-to-route-with-managed-identity.md).

Après avoir créé votre point de terminaison, vous pouvez le vérifier en sélectionnant l’icône de notification dans la barre supérieure du portail Azure : 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-notifications.png" alt-text="Capture d’écran de la notification pour vérifier la création d’un point de terminaison dans le portail Azure":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Si la création du point de terminaison échoue, observez le message d’erreur et réessayez après quelques minutes.

Vous pouvez également afficher le point de terminaison qui a été créé dans la page *Points de terminaison* de votre instance Azure Digital Twins.

La rubrique Event Grid, Event Hub ou Service Bus est maintenant disponible en tant que point de terminaison dans Azure Digital Twins, sous le nom que vous avez choisi pour le point de terminaison. Vous utiliserez généralement ce nom en tant que cible d’un **itinéraire d’événement** que vous allez créer [plus loin dans cet article](#create-an-event-route).

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli) 

Les exemples suivants montrent comment créer des points de terminaison à l’aide de la commande [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) pour l’[interface CLI Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true). Remplacez les espaces réservés dans les commandes par les informations de vos propres ressources.

Pour créer un point de terminaison Event Grid :

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Pour créer un point de terminaison Event Hubs (authentification basée sur une clé) :
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Pour créer un point de terminaison de rubrique Service Bus (authentification basée sur une clé) :
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Une fois ces commandes exécutées avec succès, la rubrique Event Grid, Event Hub ou Service Bus est disponible en tant que point de terminaison dans Azure Digital Twins, sous le nom que vous avez fourni avec l’argument `--endpoint-name`. Vous utiliserez généralement ce nom en tant que cible d’un **itinéraire d’événement** que vous allez créer [plus loin dans cet article](#create-an-event-route).

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Créer un point de terminaison avec une authentification basée sur l’identité

Vous pouvez également créer un point de terminaison avec une authentification basée sur l’identité pour utiliser le point de terminaison avec une [identité managée](concepts-security.md#managed-identity-for-accessing-other-resources). Cette option est uniquement disponible pour les points de terminaison de type Event Hub et Service Bus (elle n’est pas prise en charge pour Event Grid).

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

---

### <a name="create-an-endpoint-with-dead-lettering"></a>Créer un point de terminaison avec mise en file d’attente de lettres mortes

Lorsqu’un point de terminaison ne peut pas remettre un événement dans un laps de temps donné ou après avoir essayé de remettre l’événement un certain nombre de fois, il peut envoyer l’événement non remis à un compte de stockage. Ce processus est appelé **mise en file d’attente de lettres mortes**.

Vous pouvez configurer les ressources de stockage nécessaires à l’aide du [portail Azure](https://ms.portal.azure.com/#home) ou de l’interface [Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true). Cependant, pour créer un point de terminaison avec mise en file d’attente de lettres mortes, vous devrez utiliser l’[interface CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) ou les [API de plan de contrôle](concepts-apis-sdks.md#overview-control-plane-apis) Azure Digital Twins.

Pour en savoir plus sur la mise en file d’attente de lettres mortes, consultez [Itinéraires d’évènements](concepts-route-events.md#dead-letter-events). Pour obtenir des instructions sur la configuration d’un point de terminaison avec une mise en file d’attente de lettres mortes, suivez le reste de cette section.

#### <a name="set-up-storage-resources"></a>Configurer des ressources de stockage

Avant de définir l’emplacement des messages non distribués, vous devez disposer d’un [compte de stockage](../storage/common/storage-account-create.md?tabs=azure-portal) avec un [conteneur](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) configurés dans votre compte Azure. 

Vous devrez fournir l’URI de ce conteneur au moment de créer le point de terminaison ultérieurement. L’emplacement de la file d’attente de lettres mortes est fourni au point de terminaison en tant qu’URI de conteneur avec un [jeton SAS](../storage/common/storage-sas-overview.md). Ce jeton a besoin de l’autorisation `write` pour le conteneur de destination dans le compte de stockage. L’**URI SAS de lettre morte** complète s’affichera au format : `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`.

Suivez les étapes ci-dessous pour configurer ces ressources de stockage dans votre compte Azure, afin de préparer la configuration de la connexion du point de terminaison dans la section suivante.

1. Suivez les étapes décrites dans [Créer un compte de stockage](../storage/common/storage-account-create.md?tabs=azure-portal) pour créer un **compte de stockage** dans votre abonnement Azure. Prenez note du nom du compte de stockage ; vous en aurez besoin plus tard.
1. Suivez les étapes décrites dans [Créer un conteneur](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) pour créer un **conteneur** dans le nouveau compte de stockage. Notez le nom de conteneur ; vous en aurez besoin plus tard.

##### <a name="create-a-sas-token"></a>Créez un jeton SAP

Ensuite, créez un **jeton SAS** pour votre compte de stockage que le point de terminaison peut utiliser pour y accéder.

# <a name="portal"></a>[Portail](#tab/portal)

1. Commencez par accéder à votre compte de stockage dans le [portail Azure](https://ms.portal.azure.com/#home) (vous pouvez le rechercher par son nom dans la barre de recherche du portail).
1. Dans la page du compte de stockage, choisissez le lien _Signature d’accès partagé_ dans la barre de navigation de gauche afin de lancer la configuration du jeton SAP.

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-1.png" alt-text="Capture d’écran de la page du compte de stockage dans le portail Azure" lightbox="./media/how-to-manage-routes/generate-sas-token-1.png":::

1. Dans la page *Signature d’accès partagé*, sous *Services autorisés* et *Types de ressources autorisés*, sélectionnez les paramètres de votre choix. Vous devez sélectionner au moins une case dans chaque catégorie. Sous *Autorisations acceptées*, choisissez **Écriture** (vous pouvez également sélectionner d’autres autorisations si vous le souhaitez).
1. Définissez les valeurs de votre choix pour les autres paramètres.
1. Lorsque vous avez terminé, sélectionnez le bouton _Générer la chaîne de connexion et SAP_ pour générer le jeton SAP. 

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-2.png" alt-text="Capture d’écran de la page du compte de stockage dans le portail Azure, montrant toutes les sélections de paramètres pour générer un jeton SAS" lightbox="./media/how-to-manage-routes/generate-sas-token-2.png"::: 

1. Cela a pour effet de générer plusieurs valeurs de chaîne de connexion et SAP au bas de la même page, sous les sélections de paramètres. Faites défiler l’écran pour afficher les valeurs et utilisez l’icône de *copie dans le presse-papiers* pour copier la valeur du **jeton SAP**. Enregistrez-la en vue d’une utilisation ultérieure.

    :::image type="content" source="./media/how-to-manage-routes/copy-sas-token.png" alt-text="Capture d’écran de la page du compte de stockage dans le portail Azure mettant en évidence comment copier le jeton SAS à utiliser dans le secret des lettres mortes" lightbox="./media/how-to-manage-routes/copy-sas-token.png":::

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

1. Récupérez les clés de votre compte de stockage à l’aide de la commande suivante et copiez la valeur de l’une de vos clés :

    ```azurecli
    az storage account keys list --account-name <storage-account-name>
    ```

1. Sélectionnez une date d’expiration et générez le jeton SAP pour votre compte de stockage à l’aide de la commande suivante :

    ```azurecli
    az storage account generate-sas --account-name <storage-account-name> --account-key <storage-account-key> --expiry <expiration-date> --services bfqt --resource-types o --permissions w
    ```

    La sortie de cette commande est le jeton SAP. Copiez la valeur du jeton SAP à utiliser ultérieurement.

    > [!NOTE]
    > Cette commande inclut les services "**b** lob", "**f** ile", "**q** ueue", et "**t** able" ; un type de ressource "**o** bject"; et attribue les permissions "**w** rite".
    >
    > Pour plus d’informations sur la commande `az storage account generate-sas` et ses paramètres, consultez la [documentation CLI Azure](/cli/azure/storage/account?view=azure-cli-latest&preserve-view=true#az_storage_account_generate_sas).

---

#### <a name="create-the-dead-letter-endpoint"></a>Créer le point de terminaison de lettres mortes

# <a name="portal"></a>[Portail](#tab/portal)

Pour créer un point de terminaison avec mise en file d’attente de lettres mortes, il est préférable d’utiliser les [commandes CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) ou les [API de plan de contrôle](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) pour créer votre point de terminaison, plutôt que le portail Azure.

Pour obtenir des instructions sur la façon de procéder avec l’interface CLI Azure, basculez vers l’onglet CLI pour cette section.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Pour créer un point de terminaison avec mise en file d’attente de lettres mortes, ajoutez le paramètre de lettre morte suivant à la commande [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) pour l’[interface CLI Azure Digital Twins](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

La valeur du paramètre représente l’**URI SAS de lettre morte** constitué du nom du compte de stockage, du nom du conteneur et du jeton SAP que vous avez collectés à la [section précédente](#set-up-storage-resources). Ce paramètre crée le point de terminaison avec l’authentification basée sur une clé.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Ajoutez ce paramètre à la fin des commandes de création de point de terminaison à partir de la section [Créer le point de terminaison](#create-the-endpoint) précédente pour créer un point de terminaison du type souhaité, avec mise en file d’attente de lettres mortes.

Vous pouvez également créer des points de terminaison de lettres mortes à l’aide des [API de plan de contrôle Azure Digital Twins](concepts-apis-sdks.md#overview-control-plane-apis) au lieu de l’interface CLI. Pour cela, consultez la [documentation DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) pour savoir comment structurer la demande et ajouter les paramètres de lettres mortes.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Créer un point de terminaison de lettres mortes avec une authentification basée sur l’identité

Vous pouvez également créer un point de terminaison de lettres mortes avec une authentification basée sur l’identité pour utiliser le point de terminaison avec une [identité managée](concepts-security.md#managed-identity-for-accessing-other-resources). Cette option est uniquement disponible pour les points de terminaison de type Event Hub et Service Bus (elle n’est pas prise en charge pour Event Grid).

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

---

#### <a name="message-storage-schema"></a>Schéma de stockage des messages

Une fois que le point de terminaison avec la file d’attente de lettres mortes est configuré, les messages associés sont stockés au format suivant dans votre compte de stockage :

`<container>/<endpoint-name>/<year>/<month>/<day>/<hour>/<event-ID>.json`

Les messages en file d’attente de lettres mortes correspondent au schéma de l’événement d’origine qui a été conçu pour être remis à votre point de terminaison d’origine.

Voici un exemple de message mis en file d’attente de lettres mortes pour une [notification de création de jumeau](concepts-event-notifications.md#digital-twin-lifecycle-notifications) :

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Création d’un itinéraire d’événements

Pour envoyer concrètement des données d’Azure Digital Twins à un point de terminaison, vous devez définir un **itinéraire d’événement**. Ces itinéraires permettent aux développeurs d’associer le flux d’événements au sein du système et aux services en aval. Un seul itinéraire peut permettre la sélection de plusieurs notifications et types d’événements. Pour en savoir plus sur les itinéraires d’événements, consultez [Routage des événements Azure Digital Twins](concepts-route-events.md).

**Condition préalable** : Vous devez créer des points de terminaison comme décrit précédemment dans cet article avant de pouvoir passer à la création d’un itinéraire. Une fois que vos points de terminaison sont configurés, vous pouvez passer à la création d’un itinéraire d’événements.

>[!NOTE]
>Si vous avez récemment déployé vos points de terminaison, vérifiez que le déploiement est terminé **avant** de tenter de les utiliser pour un nouvel itinéraire d’événements. Si le déploiement de l’itinéraire échoue parce que les points de terminaison ne sont pas prêts, patientez quelques minutes, puis réessayez.
>
> Si vous créez un script pour ce flux, vous pouvez prendre cela en compte en intégrant un temps d’attente de 2-3 minutes pour permettre au service de point de terminaison d’achever le déploiement avant de passer à la configuration de l’itinéraire.

Une définition d’itinéraire peut contenir les éléments suivants :
* nom de l’itinéraire que vous souhaitez utiliser ;
* nom du point de terminaison que vous souhaitez utiliser ;
* filtre définissant les événements envoyés au point de terminaison.
    - Pour désactiver l’itinéraire afin qu’aucun événement ne soit envoyé, utilisez une valeur de filtre `false`.
    - Pour activer un itinéraire qui n’a pas de filtrage spécifique, utilisez une valeur de filtre `true`.
    - Pour plus d’informations sur les autres types de filtre, consultez la section [Filtrer les événements](#filter-events) ci-dessous

À défaut de nom d’itinéraire, aucun message n’est acheminé en dehors d’Azure Digital Twins. S’il existe un nom d’itinéraire et que le filtre est `true`, tous les messages sont acheminés vers le point de terminaison. Si un nom d’itinéraire et un filtre différent sont ajoutés, les messages sont filtrés en fonction du filtre.

Les itinéraires d'événements peuvent être créés à l’aide du[portail Azure](https://portal.azure.com), des [API de plan de données EventRoutes](/rest/api/digital-twins/dataplane/eventroutes) ou des [commandes CLI az dt route](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true). Le reste de cette section décrit le processus de création.

# <a name="portal"></a>[Portail](#tab/portal2)

Pour créer un itinéraire d’événement, accédez à la page de détails de votre instance Azure Digital Twins dans le [portail Azure](https://portal.azure.com) (vous pouvez rechercher l’instance en entrant son nom dans la barre de recherche du portail).

Dans le menu de l’instance, sélectionnez _Itinéraires d’événements_. Ensuite, à partir de la page *Itinéraires d’événements* qui suit, sélectionnez *+ Créer un itinéraire d’événements*. 

Dans la page *Créer un itinéraire d’événement* qui s’ouvre, choisissez au minimum :
* Un nom pour votre itinéraire dans le champ _Nom_
* Le _point de terminaison_ que vous souhaitez utiliser pour créer la route 

Pour que l’itinéraire soit activé, vous devez également **ajouter un filtre d’itinéraires d’événements** `true` au minimum. (Laisser la valeur `false` par défaut créera l’itinéraire, mais aucun événement ne lui sera envoyé.) Pour ce faire, appuyez sur le bouton bascule de l’_éditeur avancé_ pour l’activer, puis écrivez `true` dans la zone *Filtre*.

:::image type="content" source="media/how-to-manage-routes/create-event-route-no-filter.png" alt-text="Capture d’écran de la création d’une route d’événement pour votre instance dans le portail Azure" lightbox="media/how-to-manage-routes/create-event-route-no-filter.png":::

Lorsque vous avez terminé, sélectionnez le bouton _Enregistrer_ pour créer votre itinéraire d’événement.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli2)

Vous pouvez également gérer les itinéraires à l’aide des commandes [az dt route](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true) pour l’interface CLI Azure Digital Twins. 

Pour plus d’informations sur l’utilisation de l’interface CLI et sur les commandes disponibles, consultez [Ensemble de commandes de l’interface CLI Azure Digital Twins](concepts-cli.md).

# <a name="net-sdk"></a>[Kit de développement logiciel (SDK) .NET](#tab/sdk2)

Cette section montre comment créer un itinéraire d’événement à l’aide du [Kit de développement logiciel (SDK) .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

`CreateOrReplaceEventRouteAsync` est l’appel du kit de développement logiciel (SDK) utilisé pour ajouter un itinéraire d’événement. Voici un exemple de son utilisation :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Toutes les fonctions du Kit de développement logiciel (SDK) sont disponibles en versions synchrone et asynchrone.

#### <a name="event-route-sample-sdk-code"></a>Exemple de code SDK d’itinéraire d’événement

L’exemple de méthode suivant montre comment créer, lister et supprimer une route d’événement avec le SDK C# :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

---

## <a name="filter-events"></a>Filtrer les événements

Comme décrit ci-dessus, les itinéraires ont un champ **Filtre**. Si la valeur de filtre sur votre itinéraire est `false`, aucun événement n’est envoyé à votre point de terminaison. 

Après avoir activé le filtre minimal `true`, les points de terminaison recevront une variété d’événements d’Azure Digital Twins :
* télémétrie déclenchée par des [jumeaux numériques](concepts-twins-graph.md) à l’aide de l’API de service d’Azure Digital Twins ;
* notifications de changement de propriété de jumeau, déclenchées par des modifications des propriétés d’un jumeau dans l’instance Azure Digital Twins ;
* événements de cycle de vie déclenchés lors de la création ou de la suppression de jumeaux ou de relations ;

Vous pouvez restreindre les types d’événements envoyés en définissant un filtre plus spécifique.

>[!NOTE]
> Les filtres **respectent la casse** et doivent correspondre à la casse de la charge utile. 
>
> Pour les filtres de télémétrie, cela signifie que la casse doit correspondre à celle de la télémétrie envoyée par l’appareil, pas nécessairement à celle définie dans le modèle du jumeau.

# <a name="portal"></a>[Portail](#tab/portal3)

Pour ajouter un filtre d’événements lors de la création d’un itinéraire d’événement, utilisez la section Ajouter un filtre d’itinéraires d’événements de la page *Créer une route d’événement*. 

Vous pouvez sélectionner l’une des options de filtre courantes de base ou utiliser les options de filtre avancées pour écrire vos propres filtres personnalisés.

### <a name="use-the-basic-filters"></a>Utiliser les filtres de base

Pour utiliser les filtres de base, développez l’option _Types d’événements_, puis cochez les cases correspondant aux événements que vous souhaitez envoyer à votre point de terminaison. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-1.png" alt-text="Capture d’écran de la création d’une route d’événement avec un filtre de base dans le portail Azure, mettant en évidence les cases à cocher des événements":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Cette opération remplit automatiquement la zone de texte de filtre avec le texte du filtre que vous avez sélectionné :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-2.png" alt-text="Capture d’écran de la création d’une route d’événement avec un filtre de base dans le portail Azure, mettant en évidence le texte de filtre rempli automatiquement après la sélection des événements":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="use-the-advanced-filters"></a>Utiliser les filtres avancés

Vous pouvez également utiliser l’option de filtre avancée pour écrire vos propres filtres personnalisés.

Pour créer un itinéraire d’événement avec des options de filtre avancées, appuyez sur le bouton bascule de l’_éditeur avancé_ pour l’activer. Vous pouvez ensuite écrire vos propres filtres d’événement dans la zone *Filtre* :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-advanced.png" alt-text="Capture d’écran de la création d’une route d’événement avec un filtre avancé dans le portail Azure":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

# <a name="api"></a>[API](#tab/api)

Vous pouvez utiliser les API pour écrire des filtres personnalisés. Pour ajouter un filtre, vous pouvez envoyer une demande PUT à `https://<Your-Azure-Digital-Twins-host-name>/eventRoutes/<event-route-name>?api-version=2020-10-31` avec le corps suivant :

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

---

### <a name="supported-route-filters"></a>Filtres de routage pris en charge

Voici les filtres d’itinéraire pris en charge.

| Nom du filtre | Description | Schéma du texte du filtre | Valeurs prises en charge | 
| --- | --- | --- | --- |
| True / False | Autorise la création d'un itinéraire sans filtrage ou la désactivation d'un itinéraire afin qu'aucun événement ne soit envoyé | `<true/false>` | `true` = l’itinéraire est activé sans filtrage. <br> `false` = l’itinéraire est désactivé |
| Type | [Type d’événement](concepts-route-events.md#types-of-event-messages) circulant dans votre instance de jumeau numérique. | `type = '<event-type>'` | Les valeurs de type d'événement possibles sont les suivantes : <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Source | Nom de l’instance Azure Digital Twins. | `source = '<host-name>'`| Les valeurs de nom d’hôte possibles sont les suivantes : <br> **Pour les notifications** : `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net` <br> **Pour la télémétrie** : `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net/<twin-ID>`|
| Objet | Description de l’événement dans le contexte de la source de l’événement ci-dessus | `subject = '<subject>'` | Les valeurs d'objet sont les suivantes : <br>**Pour les notifications** : l’objet est `<twin-ID>` <br> ou un format d’URI pour les objets identifiés de façon unique par plusieurs éléments ou ID :<br>`<twin-ID>/relationships/<relationship-ID>`<br> **Pour la télémétrie** : l’objet est le chemin d’accès du composant (si la télémétrie est émise à partir d’un composant jumeau), par exemple `comp1.comp2`. Si la télémétrie n’est pas émise à partir d’un composant, le champ d’objet est vide. |
| Schéma de données | ID de modèle DTDL | `dataschema = '<model-dtmi-ID>'` | **Pour la télémétrie** : le schéma de données est l'ID de modèle du jumeau ou du composant qui émet les données de télémétrie. Par exemple : `dtmi:example:com:floor4;2` <br>**Pour les notifications (création/suppression)**  : Le schéma de données est accessible dans le corps de la notification à `$body.$metadata.$model`. <br>**Pour les notifications (mise à jour)**  : Le schéma de données est accessible dans le corps de la notification à `$body.modelId`|
| Type de contenu | Type de contenu de la valeur de données | `datacontenttype = '<content-type>'` | Le type de contenu est `application/json` |
| Version de spécification | Version du schéma d’événement que vous utilisez | `specversion = '<version>'` | La version doit être `1.0`. Cela indique le schéma CloudEvents version 1.0. |
| Corps de la notification | Référencer n’importe quelle propriété dans le champ `data` d’une notification | `$body.<property>` | Consultez [Notifications d'événements](concepts-event-notifications.md) pour accéder à des exemples de notifications. Toute propriété du champ `data` peut être référencée à l’aide de `$body`

>[!NOTE]
> Azure Digital Twins ne prend actuellement pas en charge le filtrage d’événements basés sur les champs d’un tableau. Cela comprend le filtrage des propriétés au sein d’une section `patch` d’une [notification de modification de jumeau numérique](concepts-event-notifications.md#digital-twin-change-notifications).

Les types de données suivants sont pris en charge en tant que valeurs retournées par les références aux données ci-dessus :

| Type de données | Exemple |
|-|-|-|
|**Chaîne**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twin-ID>')`|
|**Integer**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Les opérateurs suivants sont pris en charge lors de la définition de filtres de route :

|Famille|Opérateurs|Exemple|
|-|-|-|
|Logical|AND, OR, ( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Comparaison|<, <=, >, >=, =, !=|`$body.temperature <= 5.5`

Les fonctions suivantes sont prises en charge lors de la définition de filtres de route :

|Fonction|Description|Exemple|
|--|--|--|
|STARTS_WITH(x,y)|Retourne la valeur true si la valeur `x` commence par la chaîne `y`.|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | Retourne la valeur true si la valeur `x` se termine par la chaîne `y`.|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| Retourne la valeur true si la valeur `x` contient la chaîne `y`.|`CONTAINS(subject, '<twin-ID>')`|

Lorsque vous implémentez ou mettez à jour un filtre, quelques minutes peuvent s’écouler avant que la modification apparaisse dans le pipeline de données.

## <a name="monitor-event-routes"></a>Surveiller les itinéraires d’événements

Vous pouvez consulter les métriques de routage telles que le nombre, la latence et le taux d’échec dans le [portail Azure](https://portal.azure.com/). 

Dans la page d’accueil du portail, recherchez votre instance Azure Digital Twins pour en extraire les détails. Sélectionnez l’option **Métriques** dans le menu de navigation de l’instance Azure Digital Twins à gauche pour afficher la page *Métriques*.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Capture d’écran montrant la page des métriques pour Azure Digital Twins.":::

À partir de là, vous pouvez afficher les métriques de votre instance et créer des vues personnalisées.

Pour plus d’informations sur la visualisation des métriques Azure Digital Twins, consultez [Visualiser les métriques avec Azure Monitor](troubleshoot-metrics.md).

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les différents types de messages d’événements que vous pouvez recevoir :
* [Notifications d’événement](concepts-event-notifications.md)