---
title: Authentifier les clients de publication Azure Event Grid avec des clés d’accès ou des signatures d’accès partagé
description: Cet article explique comment authentifier les clients de publication Azure Event Grid avec des clés d’accès et des signatures d’accès partagé.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5a271372afcc36df97d0a7afa37ee0b065a5a5e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531978"
---
# <a name="authenticate-azure-event-grid-publishing-clients-using-access-keys-or-shared-access-signatures"></a>Authentifier les clients de publication Azure Event Grid avec des clés d’accès ou des signatures d’accès partagé
Cet article fournit des informations sur l’authentification des clients qui publient des événements dans des rubriques, des domaines ou des espaces de noms de partenaires Azure Event Grid à l’aide d’une **clé d’accès** ou d’un jeton de **signature d’accès partagé (SAS)** . 

> [!IMPORTANT]
> Le fait d’authentifier et d’autoriser des utilisateurs ou des applications en utilisant des identités Azure AD offre davantage de sécurité et de facilité d’utilisation par rapport à l’authentification basée sur une clé et avec des signatures d’accès partagé (SAS). Azure AD vous évite d’avoir à stocker les secrets utilisés pour l’authentification dans votre code. Vous êtes ainsi moins exposé au risque de failles de sécurité. Nous vous recommandons vivement d’utiliser Azure AD avec vos applications de publication d’événements Azure Event Grid. Pour plus d’informations, consultez [Authentifier les clients de publication avec Azure Active Directory](authenticate-with-active-directory.md).

## <a name="authenticate-using-access-key"></a>S’authentifier à l’aide d’une clé d’accès
L’authentification par clé d’accès est la plus simple. Vous pouvez passer la clé d’accès sous la forme d’un en-tête HTTP ou d’un paramètre de requête d’URL. 

### <a name="access-key-in-an-http-header"></a>Clé d’accès dans un en-tête HTTP
Transmettez la clé d’accès en tant que valeur pour l’en-tête HTTP : `aeg-sas-key`.

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Clé d’accès en tant que paramètre de requête
Vous pouvez également spécifier `aeg-sas-key` en tant que paramètre de requête. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Pour obtenir des instructions sur la façon d’obtenir des clés d’accès pour une rubrique ou un domaine, consultez [Obtenir les clés d’accès](get-access-keys.md).

## <a name="authenticate-using-sas"></a>S’authentifier à l’aide d’une signature SAS 
Les jetons SAP pour une ressource Event Grid incluent la ressource, un délai d’expiration et une signature. Le jeton SAP est au format suivant : `r={resource}&e={expiration}&s={signature}`.

La ressource représente le chemin de la rubrique Event Grid à laquelle vous envoyez des événements. Exemple de chemin d'accès de ressource valide : `https://<yourtopic>.<region>.eventgrid.azure.net/api/events`. Pour accéder à la liste des versions d'API prises en charge, consultez [Types de ressources Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). 

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

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Utilisation de l’en-tête eg-sas-token
Voici un exemple de transmission du jeton SAP en tant que valeur pour l’en-tête `aeg-sas-token`. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Utilisation de l’en-tête Authorization
Voici un exemple de transmission du jeton SAP en tant que valeur pour l’en-tête `Authorization`. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’authentification avec les gestionnaires d’événements pour remettre des événements, consultez [Authentification de remise d’événements](security-authentication.md). 
