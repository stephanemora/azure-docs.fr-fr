---
title: Authentifier les événements de publication des clients dans des rubriques ou des domaines personnalisés Event Grid
description: Cet article décrit les différentes façons d’authentifier les événements de publication des clients dans des rubriques personnalisées Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1e147830a4b37a8603df8e4ce29953acab2345bd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115874"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Authentifier les clients de publication (Azure Event Grid)
Cet article fournit des informations sur l’authentification des clients publiant des événements dans des rubriques ou des domaines Azure Event Grid à l’aide d'une **clé d’accès** ou d’un jeton **signature d’accès partagé (SAS)** . Nous vous recommandons l’utilisation d’un jeton SAS, mais l’authentification par clé propose une programmation simple et est compatible avec de nombreux éditeurs de webhook existants.  

## <a name="authenticate-using-an-access-key"></a>S’authentifier à l’aide d’une clé d’accès
L’authentification par clé d’accès est la plus simple. Vous pouvez transmettre la clé d’accès en tant qu’en-tête HTTP ou paramètre de requête d’URL. 

### <a name="access-key-in-a-http-header"></a>Clé d’accès dans un en-tête HTTP
Transmettez la clé d’accès en tant que valeur pour l’en-tête HTTP : `aeg-sas-key`.

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Clé d’accès en tant que paramètre de requête
Vous pouvez également spécifier `aeg-sas-key` en tant que paramètre de requête. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Pour obtenir des instructions sur la façon d’obtenir des clés d’accès pour une rubrique ou un domaine, consultez [Obtenir les clés d’accès](get-access-keys.md).

## <a name="authenticate-using-a-sas-token"></a>S’authentifier au moyen d’un jeton SAS
Les jetons SAP pour une ressource Event Grid incluent la ressource, un délai d’expiration et une signature. Le jeton SAP est au format suivant : `r={resource}&e={expiration}&s={signature}`.

La ressource représente le chemin de la rubrique Event Grid à laquelle vous envoyez des événements. Exemple de chemin d'accès de ressource valide : `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Pour accéder à la liste des versions d'API prises en charge, consultez [Types de ressources Microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Tout d’abord, générez par programmation un jeton SAP, puis utilisez l’en-tête `aeg-sas-token` ou `Authorization SharedAccessSignature` pour l’authentification avec Event Grid. 

### <a name="generate-sas-token-programmatically"></a>Générer des jetons SAS par programmation
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

### <a name="using-aeg-sas-token-header"></a>Utilisation de l’en-tête eg-sas-token
Voici un exemple de transmission du jeton SAP en tant que valeur pour l’en-tête `aeg-sas-toke`. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Utilisation de l’en-tête Authorization
Voici un exemple de transmission du jeton SAP en tant que valeur pour l’en-tête `Authorization`. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’authentification avec les gestionnaires d’événements pour remettre des événements, consultez [Authentification de remise d’événements](security-authentication.md). 