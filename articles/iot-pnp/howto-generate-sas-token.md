---
title: Générer un jeton de sécurité pour accéder au référentiel IoT Plug-and-Play (préversion) | Microsoft Docs
description: Générez un jeton de signature d’accès partagé à utiliser quand vous accédez à un référentiel de modèles IoT Plug-and-Play (préversion) par programme.
author: Philmea
ms.author: philmea
ms.date: 08/06/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f6c4f5b9784eeff9d03b6e93953674736fb78c6c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976024"
---
# <a name="generate-sas-token"></a>Générer un jeton SAS

Ce guide pratique vous montre comment générer, par programme, un jeton de signature d’accès partagé (SAS, shared access signature) à utiliser avec les API de référentiel de modèles IoT Plug-and-Play (préversion).

## <a name="python"></a>Python

L’extrait de code suivant vous montre comment générer un jeton SAS à l’aide de Python :

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

L’extrait de code suivant vous montre comment générer un jeton SAS à l’aide de C\# :

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Utiliser le jeton SAS

Après avoir généré un jeton SAS, vous pouvez l’utiliser pour envoyer une requête HTTP POST. Par exemple :

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Si vous donnez un jeton SAS à un client, celui-ci ne dispose pas de la clé primaire de la ressource et ne peut pas inverser le code de hachage pour l’obtenir. Un jeton SAS vous permet de contrôler ce à quoi le client peut accéder et pour combien de temps. Quand vous modifiez la clé primaire dans la stratégie, tous les jetons SAS créés à partir de cette clé deviennent non valides.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à générer des jetons de sécurité à utiliser pour accéder aux référentiels de modèles IoT Plug-and-Play (préversion), nous vous recommandons d’en apprendre davantage en consultant le [Guide du développeur sur la modélisation avec IoT Plug-and-Play (préversion)](concepts-developer-guide.md).
