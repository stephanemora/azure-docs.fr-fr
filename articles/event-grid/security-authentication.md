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
ms.openlocfilehash: 71d47c83586f7e5e31b148714e2804686422326a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588256"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Authentification de l’accès aux ressources Azure Event Grid
Cet article fournit des informations sur les scénarios suivants :  

- Authentifier les clients qui publient des événements dans des rubriques Azure Event Grid à l’aide d’une signature d’accès partagé (SAS) ou d’une clé. 
- Sécuriser votre point de terminaison webhook à l’aide d’Azure Active Directory (Azure AD) pour authentifier Event Grid avant la **remise** des événements au point de terminaison.

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

Tous les événements ou données écrits sur le disque par le service Event Grid sont chiffrés à l’aide d’une clé managée par Microsoft, ce qui garantit un chiffrement au repos. En outre, la durée maximale de conservation des événements ou données est de 24 heures, conformément à la [stratégie de nouvelles tentatives Event Grid](delivery-and-retry.md). Event Grid supprime automatiquement tous les événements ou données après 24 heures, ou la durée de vie de l’événement, selon la valeur la plus faible.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Authentifier la remise des événements aux points de terminaison webhook
Les sections suivantes décrivent comment authentifier la remise d’événements aux points de terminaison webhook. L’emploi d’un mécanisme de négociation de validation est obligatoire, quelle que soit la méthode que vous utilisez. Pour plus d’informations, consultez [Remise d’événements webhook](webhook-event-delivery.md). 

### <a name="using-azure-active-directory-azure-ad"></a>Utilisation d’Azure Active Directory (Azure AD)
Vous pouvez sécuriser votre point de terminaison webhook à l’aide d’Azure Active Directory (Azure AD) pour authentifier et autoriser Event Grid à remettre des événements à vos points de terminaison. Vous devez créer une application Azure AD, créer un rôle et un principal de service dans votre application qui autorise Event Grid, et configurer l’abonnement aux événements pour utiliser l’application Azure AD. [Découvrez comment configurer Azure Active Directory avec Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Utilisation d’un secret client comme paramètre de requête
Vous pouvez sécuriser votre point de terminaison Webhook en ajoutant des paramètres de requête à l’URL Webhook lorsque vous créez un abonnement à un événement. Définissez un de ces paramètres de requête en tant que secret client, par exemple un [jeton d’accès](https://en.wikipedia.org/wiki/Access_token) ou un secret partagé. Le webhook peut utiliser le secret pour établir que l'événement provient d'Event Grid avec des autorisations valides. Event Grid va inclure ces paramètres de requête dans chaque remise d’événement au Webhook. Si le secret client est mis à jour, l’abonnement aux événements doit également être mis à jour. Pour éviter les échecs de remise durant cette rotation de secrets, configurez le webhook pour qu’il accepte les secrets anciens et nouveaux pendant une durée limitée. 

Comme les paramètres de requête peuvent contenir des secrets clients, ils doivent être utilisés avec une prudence particulière. Ils sont stockés sous une forme chiffrée et ne sont pas accessibles aux opérateurs de service. Ils ne sont pas enregistrés dans les journaux/traces de service. Lorsque vous modifiez l’abonnement aux événements, les paramètres de requête ne sont pas affichés ni retournés, sauf si le paramètre [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) est utilisé dans [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Pour plus d’informations sur la remise d’événements à des webhooks, consultez [Remise d’événements webhook](webhook-event-delivery.md)

> [!IMPORTANT]
Azure Event Grid ne prend en charge que les points de terminaison webhook **HTTPS**. 

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Event Grid, consultez [Event Grid](overview.md)
