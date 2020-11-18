---
title: API REST Azure Active Directory - Test à l’aide de Fiddler
description: Utiliser Fiddler pour tester l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3766567fe58e8d2eb86556d3defa7a85efd9b2fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423649"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Tester l’API REST Azure App Configuration à l’aide de Fiddler

Pour tester l’API REST à l’aide de [Fiddler](https://www.telerik.com/fiddler), vous devez inclure les en-têtes HTTP requis pour l’[authentification](./rest-api-authentication-hmac.md) dans vos demandes. Voici la procédure à suivre pour configurer Fiddler de manière à tester l’API REST en générant automatiquement les en-têtes d’authentification :

1. Assurez-vous que TLS 1.2 est un protocole autorisé :
    1. Accédez à **Outils** > **Options** > **HTTPS**).
    1. Vérifiez que l’option **Déchiffrer le trafic HTTPS** est cochée.
    1. Dans la liste des protocoles, ajoutez **tls1.2** s’il n’est pas présent.
1. Ouvrez **Éditeur de script Fiddler** ou appuyez sur **Ctrl-R** dans Fiddler
1. Ajoutez le code suivant dans la classe `Handlers` avant la fonction `OnBeforeRequest`

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Ajoutez le code suivant à la fin de la fonction `OnBeforeRequest`. Mettez à jour la clé d’accès comme indiqué par le commentaire TODO.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. Utilisez l’éditeur [Composer de Fiddler](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) pour générer et envoyer une demande
