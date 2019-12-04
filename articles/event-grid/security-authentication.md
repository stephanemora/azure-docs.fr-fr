---
title: Sécurité et authentification Azure Event Grid
description: Détaille le service Azure Event Grid et ses concepts.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: dfa53acaf392e225873a40b05b8517de2f9780dc
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169579"
---
# <a name="event-grid-security-and-authentication"></a>Sécurité et authentification Azure Event Grid 

Azure Event Grid dispose de trois types d’authentification :

* Remise d’événement WebHook
* Abonnements à des événements
* Publication d’une rubrique personnalisée

## <a name="webhook-event-delivery"></a>Remise d’événement WebHook

Un Webhook constitue l’un des nombreux moyens de recevoir des événements provenant d’Azure Event Grid. Lorsqu'un nouvel événement est prêt, le service Event Grid envoie une requête HTTP (POST) au point de terminaison configuré avec l'événement dans le corps de la requête.

Comme de nombreux autres services qui prennent en charge les Webhooks, Event Grid vous demande de prouver que vous êtes propriétaire de votre point de terminaison Webhook avant de démarrer la diffusion d'événements vers ce point de terminaison. Cette condition empêche tout utilisateur malveillant d'inonder votre point de terminaison d'événements. Lorsque vous utilisez un des trois services Azure répertoriés ci-dessous, l'infrastructure Azure gère automatiquement cette validation :

* Azure Logic Apps avec [connecteur Event Grid](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions avec [déclencheur Event Grid](../azure-functions/functions-bindings-event-grid.md)

Si vous utilisez un autre type de point de terminaison, comme une fonction Azure basée sur un déclencheur HTTP, le code de votre point de terminaison doit participer à l'établissement d'une liaison de validation avec EventGrid. Event Grid prend en charge deux méthodes de validation de l'abonnement.

1. **Établissement d'une liaison ValidationCode (par programmation)**  : Cette méthode est recommandée si vous contrôlez le code source de votre point de terminaison. Au moment de la création de l'abonnement aux événements, Event Grid envoie un événement de validation d'abonnement à votre point de terminaison. Le schéma de cet événement est semblable à celui de n'importe quel autre événement Event Grid. La partie données de cet événement inclut une propriété `validationCode`. Votre application vérifie que la requête de validation concerne un abonnement aux événements attendu et renvoie le code de validation à Event Grid. Ce mécanisme d'établissement de liaison est pris en charge dans toutes les versions d'Event Grid.

2. **Établissement d'une liaison ValidationURL (manuel)**  : Dans certains cas, il est impossible d'accéder au code source du point de terminaison pour implémenter l'établissement de la liaison ValidationCode. Par exemple, si vous utilisez un service tiers (comme [Zapier](https://zapier.com) ou [IFTTT](https://ifttt.com/)), vous ne pouvez pas envoyer le code de validation par programmation.

   À partir de la version 2018-05-01-preview, Event Grid prend en charge l'établissement d'une liaison de validation manuel. Si vous créez un abonnement aux événements à l'aide d'un kit de développement logiciel (SDK) ou d'un outil qui utilise l'API 2018-05-01-preview ou version ultérieure, Event Grid envoie une propriété `validationUrl` dans la partie données de l'événement de validation de l'abonnement. Pour terminer l'établissement de la liaison, recherchez cette URL dans les données d'événement et envoyez-lui manuellement une requête GET. Vous pouvez utiliser un client REST ou votre navigateur web.

   L’URL fournie est valable pendant 5 minutes. Pendant ce temps, l’état d’approvisionnement de l’abonnement aux événements est `AwaitingManualAction`. Si vous n’effectuez pas la validation manuelle dans les 5 minutes, l’état d’approvisionnement est défini sur `Failed`. Vous devez recréer l’abonnement aux événements avant de commencer la validation manuelle.

    Ce mécanisme d’authentification requiert également que le point de terminaison webhook retourne un code d’état HTTP 200 afin qu’il sache que le POST pour l’événement de validation a été accepté avant qu’il ne puisse être placé dans le mode de validation manuelle. En d’autres termes, si le point de terminaison renvoie un code 200 mais ne renvoie pas une réponse de validation par programmation, le mode est transmis au mode de validation manuelle. S’il y a une opération GET sur l’URL de validation dans les 5 minutes, l’établissement de liaison de validation est considéré comme réussi.

> [!NOTE]
> L’utilisation de certificats auto-signés pour la validation n’est pas prise en charge. Utilisez plutôt un certificat signé auprès d’une autorité de certification (AC).

### <a name="validation-details"></a>Détails de validation

* Au moment de la création/mise à jour de l'abonnement aux événements, Event Grid publie un événement de validation d'abonnement sur le point de terminaison cible. 
* L'événement contient une valeur d'en-tête « aeg-event-type: SubscriptionValidation ».
* Le corps de l’événement dispose du même schéma que les autres événements Event Grid.
* La propriété eventType de l’événement correspond à `Microsoft.EventGrid.SubscriptionValidationEvent`.
* La propriété de données de l’événement inclut une propriété `validationCode` avec une chaîne générée de façon aléatoire, par exemple « validationCode: acb13… ».
* Les données d’événement incluent une propriété `validationUrl` avec une URL pour la validation manuelle de l’abonnement.
* Le tableau contient uniquement l’événement de validation. Les autres événements sont envoyés dans une requête distincte, une fois que vous avez renvoyé le code de validation.
* Les kits de développement logiciel DataPlane EventGrid possèdent des classes correspondant aux données d’événement de validation d’abonnement et à la réponse de validation d’abonnement.

Un exemple de SubscriptionValidationEvent est illustré ci-dessous :

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Pour prouver que vous êtes propriétaire du point de terminaison, renvoyez le code de validation dans la propriété validationResponse, comme indiqué dans l’exemple suivant :

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Vous devez renvoyer un code d'état de réponse HTTP 200 OK. HTTP 202 Accepté n’est pas considéré comme une réponse de validation d’abonnement Event Grid valide. La requête HTTP doit s’achever dans un délai de 30 secondes. Si l’opération ne se termine pas dans les 30 secondes, elle est annulée et pourra faire l’objet d’une nouvelle tentative au bout de 5 secondes. Si toutes les tentatives échouent, elle est traitée comme une erreur d’établissement d’une liaison de validation.

Sinon, vous pouvez valider manuellement l’abonnement en envoyant une demande GET à l’URL de validation. L’abonnement aux événements reste dans un état d’attente tant qu’il n’est pas validé. L’URL de validation utilise le port 553. Si les règles de pare-feu bloquent le port 553, il faudra peut-être les mettre à jour pour que l’établissement manuel d’une liaison réussisse.

Pour accéder à un exemple de gestion d'établissement de liaison de validation d'abonnement, consultez un [exemple C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Liste de contrôle

Lors de la création de l’abonnement aux événements, si vous voyez un message d’erreur indiquant que la tentative de validation a échoué pour le point de terminaison https:\//your-endpoint-here failed. et vous invitant à rechercher plus d’informations dans https:\//aka.ms/esvalidation, cela signifie qu’il existe une défaillance dans l’établissement de liaison de validation. Pour résoudre cette erreur, vérifiez les points suivants :

* Contrôlez-vous le code d’application dans le point de terminaison cible ? Par exemple, si vous écrivez un déclencheur HTTP basé sur Azure Function, avez-vous accès au code d’application pour apporter des modifications ?
* Si vous avez accès au code d’application, implémentez le mécanisme d’établissement de liaison ValidationCode comme dans l’exemple ci-dessus.

* Si vous n’avez pas accès au code d’application (par exemple, si vous utilisez un service tiers qui prend en charge les Webhooks), vous pouvez utiliser le mécanisme d’établissement de liaison manuel. Vérifiez que vous utilisez la version d’API 2018-05-01-preview ou ultérieure (installation de l’extension Azure CLI Event Grid) pour recevoir validationUrl dans l’événement de validation. Pour terminer l’établissement de liaison de validation manuel, obtenez la valeur de la propriété `validationUrl` et accédez à cette URL dans votre navigateur web. Si la validation a réussi, vous devez voir un message dans votre navigateur web indiquant que la validation a réussi. La propriété provisioningState de l’abonnement aux événements doit être définie sur « Réussi ». 

### <a name="event-delivery-security"></a>Sécurité de la remise des événements

#### <a name="azure-ad"></a>Azure AD

Vous pouvez sécuriser votre point de terminaison webhook à l’aide d’Azure Active Directory pour authentifier et autoriser Event Grid à publier des événements sur vos points de terminaison. Vous devez créer une application Azure Active Directory, créer un rôle et un principal de service dans votre application qui autorise Event Grid et configurer l’abonnement aux événements pour utiliser l’application Azure AD. [Découvrez comment configurer AAD avec Event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Paramètres de requête
Vous pouvez sécuriser votre point de terminaison Webhook en ajoutant des paramètres de requête à l’URL Webhook lorsque vous créez un abonnement à un événement. Définissez un de ces paramètres de requête en tant que secret, par exemple un [jeton d’accès](https://en.wikipedia.org/wiki/Access_token). Le webhook peut utiliser le secret pour établir que l'événement provient d'Event Grid avec des autorisations valides. Event Grid va inclure ces paramètres de requête dans chaque remise d’événement au Webhook.

Lorsque vous modifiez l’abonnement aux événements, les paramètres de requête ne sont pas affichés ni retournés, sauf si le paramètre [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) est utilisé dans [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Enfin, il est important de noter qu’Azure Event Grid ne prend en charge que les points de terminaison de Webhook HTTPS.

## <a name="event-subscription"></a>Abonnement à un événement

Pour vous abonner à un événement, vous devez prouver que vous avez accès à la source d’événement et au gestionnaire. La section précédente a expliqué comment prouver que vous possédez votre propre webhook. Si vous utilisez un gestionnaire d’événements qui n’est pas un WebHook (par exemple, un Event Hub ou un stockage File d’attente), vous avez besoin d’un accès en écriture à cette ressource. Cette vérification des autorisations empêche qu’un utilisateur non autorisé envoie des événements à votre ressource.

Vous devez disposer de l’autorisation **Microsoft.EventGrid/EventSubscriptions/Write** sur la ressource correspondant à la source de l’événement. Vous avez besoin de cette autorisation, car vous rédigez un nouvel abonnement dans la portée de la ressource. La ressource nécessaire diffère si vous vous abonnez à une rubrique du système ou à une rubrique personnalisée. Les deux types sont décrits dans cette section.

### <a name="system-topics-azure-service-publishers"></a>Rubriques du système (éditeurs du service Azure)

Dans les rubriques du système, il vous faut l’autorisation de rédiger un nouvel abonnement à un événement dans la portée de la ressource qui publie l’événement. La ressource est au format suivant : `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Par exemple, pour vous abonner à un événement sur un compte de stockage nommé **myacct**, il vous faut l’autorisation Microsoft.EventGrid/EventSubscriptions/Write sur :`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Rubriques personnalisées

Dans les rubriques personnalisées, vous avez besoin de l’autorisation de rédiger un nouvel abonnement à un événement dans la portée de la rubrique Event Grid. La ressource est au format suivant : `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Par exemple, pour vous abonner à une rubrique personnalisée nommée **mytopic**, il vous faut l’autorisation Microsoft.EventGrid/EventSubscriptions/Write sur :`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publication d’une rubrique personnalisée

Les rubriques personnalisées utilisent une Signature d’accès partagé (SAP) ou une authentification par clé. Nous vous recommandons la SAP, mais l’authentification par clé propose une programmation simple et est compatible avec de nombreux éditeurs de webhook existants. 

Vous incluez la valeur d’authentification dans l’en-tête HTTP. Pour une SAP, utilisez **aeg-sas-token** en valeur de l’en-tête. Pour une authentification par clé, utilisez **aeg-sas-key** en valeur de l’en-tête.

### <a name="key-authentication"></a>Authentification par clé

L’authentification par clé est la plus simple. Utilisez le format : `aeg-sas-key: <your key>`

Par exemple, vous transmettez une clé avec :

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Jetons SAS

Les jetons SAP pour Event Grid incluent la ressource, un délai d’expiration et une signature. Le jeton SAP est au format suivant : `r={resource}&e={expiration}&s={signature}`.

La ressource représente le chemin de la rubrique Event Grid à laquelle vous envoyez des événements. Par exemple, un chemin d’accès de ressource valide est :`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Vous générez la signature à partir d’une clé.

Par exemple, une valeur **aeg-sas-token** valide est :

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

L’exemple suivant crée un jeton SAP utilisable avec Event Grid :

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Contrôle d’accès à la gestion

Azure Event Grid vous permet de contrôler le niveau d’accès offert aux utilisateurs, leur permettant d’effectuer différentes opérations de gestion telles que répertorier et créer des abonnements aux événements et générer des clés. Event Grid utilise le contrôle d’accès en fonction du rôle (RBAC) d’Azure.

### <a name="operation-types"></a>Types d’opération

Event Grid prend en charge les actions suivantes :

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Les trois dernières opérations retournent des informations potentiellement confidentielles qui sont filtrées lors d’opérations de lecture normales. Il est recommandé de restreindre l’accès à ces opérations. 

### <a name="built-in-roles"></a>Rôles intégrés

Event Grid fournit deux rôles intégrés pour gérer les abonnements aux événements. Ceux-ci sont importants lors de l'implémentation de [domaines d'événements](event-domains.md) car ils fournissent aux utilisateurs les autorisations nécessaires pour s'abonner aux rubriques de votre domaine d'événements. Ces rôles sont centrés sur les abonnements aux événements et ne donnent pas accès aux actions telles que la création de rubriques.

Vous pouvez [affecter ces rôles à un utilisateur ou groupe](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Contributeur EventGrid EventSubscription** : gère les opérations relatives aux abonnements Event Grid.

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**Lecteur EventGrid EventSubscription** : lit les abonnements Event Grid.

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Rôles personnalisés

Si vous avez besoin de spécifier des autorisations autres que les rôles intégrés, vous pouvez créer des rôles personnalisés.

Voici des exemples de définitions de rôle dans Event Grid permettant aux utilisateurs d’effectuer différentes actions. Ces rôles personnalisés sont différents des rôles intégrés car ils confèrent un accès qui va au-delà des abonnements aux événements.

**EventGridReadOnlyRole.json** : autorise uniquement les opérations en lecture seule.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json** : autorise des actions de publication limitées, et interdit les actions de suppression.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json** : autorise toutes les actions Event Grid.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Vous pouvez créer des rôles personnalisés avec [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md) et [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez [Event Grid](overview.md)
