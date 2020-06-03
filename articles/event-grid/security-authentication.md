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
ms.openlocfilehash: bca450022322db7a7569fa1dc7ce80ec75a9ce69
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774308"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Authentification de l’accès aux ressources Azure Event Grid
Cet article fournit des informations sur les scénarios suivants :  

- Authentifier les clients qui publient des événements dans des rubriques Azure Event Grid à l’aide d’une signature d’accès partagé (SAS) ou d’une clé. 
- Sécurisez le point de terminaison webhook utilisé pour recevoir des événements d’Event Grid à l’aide d’Azure Active Directory (Azure AD) ou d’un secret partagé.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Authentifier les clients de publication à l’aide d’une SAS ou d’une clé
Les rubriques personnalisées utilisent une Signature d’accès partagé (SAP) ou une authentification par clé. Nous vous recommandons la SAP, mais l’authentification par clé propose une programmation simple et est compatible avec de nombreux éditeurs de webhook existants.

Vous incluez la valeur d’authentification dans l’en-tête HTTP. Pour une SAP, utilisez **aeg-sas-token** en valeur de l’en-tête. Pour une authentification par clé, utilisez **aeg-sas-key** en valeur de l’en-tête.

### <a name="key-authentication"></a>Authentification par clé

L’authentification par clé est la plus simple. Utilisez le format : `aeg-sas-key: <your key>` dans l’en-tête du message.

Par exemple, vous transmettez une clé avec :

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Vous pouvez également spécifier `aeg-sas-key` en tant que paramètre de requête. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
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

L’ensemble des événements ou données écrits sur le disque par le service Event Grid sont chiffrés à l’aide d’une clé gérée par Microsoft, ce qui garantit leur chiffrement au repos. En outre, la durée maximale de conservation des événements ou données est de 24 heures, conformément à la [stratégie de nouvelles tentatives Event Grid](delivery-and-retry.md). Event Grid supprime automatiquement tous les événements ou données après 24 heures, ou la durée de vie de l’événement, selon la valeur la plus faible.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Authentifier la remise des événements aux points de terminaison webhook
Les sections suivantes décrivent comment authentifier la remise d’événements aux points de terminaison webhook. L’emploi d’un mécanisme de négociation de validation est obligatoire, quelle que soit la méthode que vous utilisez. Pour plus d’informations, consultez [Remise d’événements webhook](webhook-event-delivery.md). 

### <a name="using-azure-active-directory-azure-ad"></a>Utilisation d’Azure Active Directory (Azure AD)
Vous pouvez sécuriser le point de terminaison webhook utilisé pour recevoir des événements d’Event Grid à l’aide d’Azure AD. Vous devez créer une application Azure AD, créer un rôle et un principal de service dans votre application qui autorisent Event Grid, et configurer l’abonnement à un événement pour utiliser l’application Azure AD. Découvrez comment [Configurer Azure Active Directory avec Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Utilisation d’un secret client comme paramètre de requête
Vous pouvez également sécuriser votre point de terminaison webhook en ajoutant des paramètres de requête à l’URL de destination webhook spécifiée lors de la création d’un abonnement à un événement. Définissez l’un des paramètres de requête en tant que clé secrète client, par exemple un [jeton d’accès](https://en.wikipedia.org/wiki/Access_token) ou un secret partagé. Le service Event Grid inclut tous les paramètres de requête dans chaque demande de remise d’événement au webhook. Le service webhook peut récupérer et valider le secret. Si le secret client est mis à jour, l’abonnement aux événements doit également être mis à jour. Pour éviter les échecs de remise pendant cette rotation de secret, faites en sorte que le webhook accepte l’ancien et le nouveau secrets pendant une durée limitée avant de mettre à jour l’abonnement à l’événement avec le nouveau secret. 

Comme les paramètres de requête peuvent contenir des secrets clients, ils doivent être utilisés avec une prudence particulière. Ils sont stockés sous forme chiffrée hors de portée des opérateurs de service. Ils ne sont pas enregistrés dans les journaux/traces de service. Lors de la récupération des propriétés de l’abonnement à l’événement, par défaut, les paramètres de requête de destination ne sont pas retournés. Par exemple, le paramètre [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) doit être utilisé dans [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Pour plus d’informations sur la remise d’événements à des webhooks, consultez [Remise d’événements webhook](webhook-event-delivery.md)

> [!IMPORTANT]
Azure Event Grid ne prend en charge que les points de terminaison webhook **HTTPS**. 

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Event Grid, consultez [Event Grid](overview.md)
