---
title: Sécurité et authentification Azure Event Grid
description: Cet article décrit les différentes façons d’authentifier l’accès à vos ressources Event Grid (webhook, abonnements, rubriques personnalisées)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532391"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Authentification de l’accès aux ressources Event Grid

Azure Event Grid dispose de trois types d’authentification :

- Remise d’événement WebHook
- Abonnements à des événements
- Publication d’une rubrique personnalisée

## <a name="webhook-event-delivery"></a>Remise d’événement WebHook

Un Webhook constitue l’un des nombreux moyens de recevoir des événements provenant d’Azure Event Grid. Lorsqu'un nouvel événement est prêt, le service Event Grid envoie une requête HTTP (POST) au point de terminaison configuré avec l'événement dans le corps de la requête.

Comme de nombreux autres services qui prennent en charge les Webhooks, Event Grid vous demande de prouver que vous êtes propriétaire de votre point de terminaison Webhook avant de démarrer la diffusion d'événements vers ce point de terminaison. Cette condition empêche tout utilisateur malveillant d'inonder votre point de terminaison d'événements. Lorsque vous utilisez un des trois services Azure répertoriés ci-dessous, l'infrastructure Azure gère automatiquement cette validation :

- Azure Logic Apps avec [connecteur Event Grid](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions avec [déclencheur Event Grid](../azure-functions/functions-bindings-event-grid.md)

Si vous utilisez un autre type de point de terminaison, comme une fonction Azure basée sur un déclencheur HTTP, le code de votre point de terminaison doit participer à l'établissement d'une liaison de validation avec EventGrid. Event Grid prend en charge deux méthodes de validation de l'abonnement.

1. **Établissement d'une liaison synchrone** : Au moment de la création de l'abonnement aux événements, Event Grid envoie un événement de validation d'abonnement à votre point de terminaison. Le schéma de cet événement est semblable à celui de n'importe quel autre événement Event Grid. La partie données de cet événement inclut une propriété `validationCode`. Votre application vérifie que la requête de validation concerne un abonnement à un événement attendu, et renvoie le code de validation dans la réponse de manière synchrone. Ce mécanisme d'établissement de liaison est pris en charge dans toutes les versions d'Event Grid.

2. **Établissement d'une liaison asynchrone** : Dans certains cas, il est impossible de renvoyer le code de validation de manière synchrone. Par exemple, si vous utilisez un service tiers (comme [`Zapier`](https://zapier.com) ou [IFTTT](https://ifttt.com/)), vous ne pouvez pas envoyer le code de validation par programmation.

   À partir de la version 2018-05-01-preview, Event Grid prend en charge l'établissement d'une liaison de validation manuel. Si vous créez un abonnement aux événements à l'aide d'un kit de développement logiciel (SDK) ou d'un outil qui utilise l'API 2018-05-01-preview ou version ultérieure, Event Grid envoie une propriété `validationUrl` dans la partie données de l'événement de validation de l'abonnement. Pour terminer l'établissement de la liaison, recherchez cette URL dans les données d'événement et envoyez-lui une requête GET. Vous pouvez utiliser un client REST ou votre navigateur web.

   L'URL fournie est valable pendant **5 minutes**. Pendant ce temps, l’état d’approvisionnement de l’abonnement aux événements est `AwaitingManualAction`. Si vous n’effectuez pas la validation manuelle dans les 5 minutes, l’état d’approvisionnement est défini sur `Failed`. Vous devez recréer l’abonnement aux événements avant de commencer la validation manuelle.

   Ce mécanisme d’authentification requiert également que le point de terminaison webhook retourne un code d’état HTTP 200 afin qu’il sache que le POST pour l’événement de validation a été accepté avant qu’il ne puisse être placé dans le mode de validation manuelle. En d'autres termes, si le point de terminaison renvoie un code 200 mais ne renvoie pas de réponse de validation de manière synchrone, le mode est transmis au mode de validation manuelle. S’il y a une opération GET sur l’URL de validation dans les 5 minutes, l’établissement de liaison de validation est considéré comme réussi.

> [!NOTE]
> L’utilisation de certificats auto-signés pour la validation n’est pas prise en charge. Utilisez plutôt un certificat signé auprès d’une autorité de certification (AC).

### <a name="validation-details"></a>Détails de validation

- Au moment de la création/mise à jour de l'abonnement aux événements, Event Grid publie un événement de validation d'abonnement sur le point de terminaison cible.
- L'événement contient une valeur d'en-tête « aeg-event-type: SubscriptionValidation ».
- Le corps de l’événement dispose du même schéma que les autres événements Event Grid.
- La propriété eventType de l’événement correspond à `Microsoft.EventGrid.SubscriptionValidationEvent`.
- La propriété de données de l’événement inclut une propriété `validationCode` avec une chaîne générée de façon aléatoire, par exemple « validationCode: acb13… ».
- Les données d’événement incluent une propriété `validationUrl` avec une URL pour la validation manuelle de l’abonnement.
- Le tableau contient uniquement l’événement de validation. Les autres événements sont envoyés dans une requête distincte, une fois que vous avez renvoyé le code de validation.
- Les kits de développement logiciel DataPlane EventGrid possèdent des classes correspondant aux données d’événement de validation d’abonnement et à la réponse de validation d’abonnement.

Un exemple de SubscriptionValidationEvent est illustré ci-dessous :

```json
[
  {
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
  }
]
```

Pour prouver que vous êtes propriétaire du point de terminaison, renvoyez le code de validation dans la propriété validationResponse, comme indiqué dans l’exemple suivant :

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Vous devez renvoyer un code d'état de réponse HTTP 200 OK. HTTP 202 Accepté n'est pas considéré comme une réponse de validation d'abonnement Event Grid valide. La requête HTTP doit s'achever dans un délai de 30 secondes. Si l’opération ne se termine pas dans les 30 secondes, elle est annulée et pourra faire l’objet d’une nouvelle tentative au bout de 5 secondes. Si toutes les tentatives échouent, elle est traitée comme une erreur d’établissement de liaison de validation.

Sinon, vous pouvez valider manuellement l’abonnement en envoyant une demande GET à l’URL de validation. L’abonnement aux événements reste dans un état d’attente jusqu’à ce qu’il soit validé. L’URL de validation utilise le port 553. Si les règles de pare-feu bloquent le port 553, il faudra peut-être les mettre à jour pour que l’établissement manuel d’une liaison réussisse.

Pour accéder à un exemple de gestion d'établissement de liaison de validation d'abonnement, consultez un [exemple C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Résolution des problèmes de validation EventSubsciption

Lors de la création de l’abonnement aux événements, si vous voyez un message d’erreur indiquant que la tentative de validation a échoué pour le point de terminaison https:\//your-endpoint-here failed. et vous invitant à rechercher plus d’informations dans https:\//aka.ms/esvalidation, cela signifie qu’il existe une défaillance dans l’établissement de liaison de validation. Pour résoudre cette erreur, vérifiez les points suivants :

- Envoyez une requête HTTP POST à l'URL de votre webhook avec un échantillon de [SubscriptionValidationEvent](#validation-details) dans le corps de la requête à l'aide de Postman, de Curl ou d'un outil similaire.
- Si votre webhook implémente un mécanisme d'établissement de liaison de validation synchrone, vérifiez que le code de validation est renvoyé dans le cadre de la réponse.
- Si votre webhook implémente un mécanisme d'établissement de liaison de validation asynchrone, assurez-vous que la requête HTTP POST renvoie 200 OK.
- Si votre webhook renvoie 403 (interdit) dans la réponse, vérifiez qu'il se trouve derrière une instance d'Azure Application Gateway ou un pare-feu d'applications web. Si tel est le cas, vous devez désactiver ces règles de pare-feu et renvoyer une requête HTTP POST :

  920300 (En-tête Accept manquant dans la requête, nous pouvons y remédier)

  942430 (Détection restreinte des anomalies de caractères SQL (args) : nombre de caractères spéciaux dépassé (12))

  920230 (Détection d'encodage de plusieurs URL)

  942130 (Attaque par injection de code SQL : Tautologie SQL détectée.)

  931130 (Attaque possible par inclusion de fichier distant = Référence/Lien hors domaine)

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

La ressource représente le chemin de la rubrique Event Grid à laquelle vous envoyez des événements. Exemple de chemin d'accès de ressource valide : `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Pour accéder à la liste des versions d'API prises en charge, consultez [Types de ressources Microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

### <a name="encryption-at-rest"></a>Chiffrement au repos

Tous les événements ou données écrits sur le disque par le service Event Grid sont chiffrés à l’aide d’une clé managée par Microsoft, ce qui garantit un chiffrement au repos. En outre, la durée maximale de conservation des événements ou données est de 24 heures, conformément à la [stratégie de nouvelles tentatives Event Grid](delivery-and-retry.md). Event Grid supprime automatiquement tous les événements ou données après 24 heures, ou la durée de vie de l’événement, selon la valeur la plus faible.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validation de point de terminaison avec CloudEvents v1.0
Si vous êtes déjà familiarisé avec Event Grid, vous pouvez être conscient de la négociation de validation de point de terminaison de Event Grid pour empêcher les abus. CloudEvents v1.0 implémente sa propre [sémantique de protection contre les abus](security-authentication.md#webhook-event-delivery) à l’aide de la méthode HTTP OPTIONS. Pour plus d'informations à ce sujet, cliquez [ici](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Lors de l’utilisation du schéma CloudEvents pour la sortie, Event Grid l’utilise avec la protection contre les abus CloudEvents v1.0 à la place du mécanisme d’événement de validation Event Grid.

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Event Grid, consultez [Event Grid](overview.md)
