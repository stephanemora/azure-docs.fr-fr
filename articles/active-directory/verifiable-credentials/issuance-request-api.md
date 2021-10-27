---
title: Spécifier la demande d’émission de l’API REST du service de demande (préversion)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Découvrez comment émettre des justificatifs vérifiables que vous avez émis
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 837b42b5cb46743b7d486def32d01ab2333db54a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006676"
---
# <a name="request-service-rest-api-issuance-specification-preview"></a>Spécification d’émission de l’API REST du service de demande (préversion)

L’API REST du service de demande e justificatifs vérifiables Azure Active Directory (Azure AD) vous permet d’émettre et de vérifier des justificatifs vérifiables. Cet article spécifie l’API REST du service de demande pour la demande d’émission.


## <a name="http-request"></a>Demande HTTP

La demande d’émission de l’API REST du service de demande prend en charge la méthode HTTP suivante :

| Méthode |Notes  |
|---------|---------|
|POST | Avec la charge utile JSON spécifiée dans cet article. |

La demande d’émission de l’API REST du service de demande nécessite les en-têtes HTTP suivants :

| Méthode |Valeur  |
|---------|---------|
|`Authorization`| Attachez le jeton d’accès comme jeton du porteur à l’en-tête d’autorisation dans une requête HTTP. Par exemple : `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Construisez une requête HTTP POST adressée à l’API REST du service de demande. Remplacez `{tenantID}` par votre [ID de locataire](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application) ou votre nom de locataire.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

La requête HTTP suivante illustre une requête HTTP adressée à l’API REST du service de requête :

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true, 
        "callback": {  
            "url": "https://wwww.contoso.com/vc/callback",  
            "state": "Aaaabbbb11112222", 
            "headers": { 
                "api-key": "an-api-key-can-go-here" 
              }  
        }, 
    ... 
}
```  

L’autorisation suivante est requise pour appeler l’API REST du service de demande. Pour plus d’informations, consultez [Accorder des autorisations pour obtenir des jetons d’accès](verifiable-credentials-configure-tenant.md#31-grant-permissions-to-get-access-tokens).

| Type d'autorisation | Autorisation  |
|---------|---------|
| Application | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="issuance-request-payload"></a>Charge utile de la demande d’émission

La charge utile de la demande d’émission contient des informations sur votre demande d’émission de justificatifs vérifiables. L’exemple suivant illustre une demande d’émission utilisant un flux de code confidentiel avec des revendications d’utilisateur, telles que le prénom et le nom. Le résultat de cette requête renvoie un code QR avec un lien pour démarrer le processus d’émission.

```json
{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```

La charge utile contient les propriétés suivantes.  


|Paramètre |Type  | Description |
|---------|---------|---------|
| `includeQRCode` |  boolean |   Détermine si un code QR est inclus dans la réponse de cette demande. Présentez le code QR et demandez à l’utilisateur de le scanner. Le scan du code QR lance l’application d’authentification avec cette demande d’émission. Valeurs possibles : `true` (par défaut) ou `false`. Lorsque la valeur est `false`, utilisez la propriété `url` de retour pour afficher le lien profond.  |
| `authority` | string|  Identificateur décentralisé de l’émetteur. Pour plus d’informations, consultez [Collecter les informations d’identification et les détails de l’environnement pour configurer votre exemple d’application](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Fournit des informations sur l’émetteur qui peuvent être affichées dans l’application d’authentification. |
| `issuance` | [RequestIssuance](#requestissuance-type)| Fournit des informations sur la demande d’émission.  |
|`callback`|  [Callback](#callback-type)| Permet au développeur d’obtenir de façon asynchrone des informations sur le flux pendant le processus d’émission de justificatifs vérifiables. Par exemple, un appel lorsque l’utilisateur a scanné le code QR.|

### <a name="requestregistration-type"></a>Type RequestRegistration

Le type RequestRegistration fournit l’inscription des informations pour l’émetteur. Le type RequestRegistration contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `clientName` | string|  Nom complet de l’émetteur des justificatifs vérifiables.  |
| `logoUrl` |  string |  [Facultatif] URL du logo de l’émetteur.  |
| `termsOfServiceUrl` |  string | [Facultatif] URL des Conditions d’utilisation des justificatifs vérifiables que vous émettez.  |

> [!NOTE]
> Pour l’instant, les informations de RequestRegistration ne sont pas présentées lors de l’émission de l’application Microsoft Authenticator, mais elles peuvent être utilisées dans la charge utile.

### <a name="requestissuance-type"></a>Type RequestIssuance

Le type RequestIssuance fournit les informations nécessaires à l’émission de justificatifs vérifiables. Il existe actuellement trois types d’entrée que vous pouvez envoyer dans le type RequestIssuance. Ces types sont utilisés par le service d’émission de justificatifs vérifiables pour insérer des revendications dans les justificatifs vérifiables et pour attester de ces informations avec le DID de l’émetteur. Les trois types sont les suivants :

- Jeton d’ID
- Justificatifs vérifiables via une présentation vérifiable.
- Revendications auto-attestées

Vous trouverez des informations détaillées sur les types d’entrée dans l’article [Personnalisation de vos justificatifs vérifiables](credential-design.md). 

Le type RequestIssuance contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| type |  string |  Type de justificatifs vérifiables. Doit correspondre au type défini dans le manifeste des justificatifs vérifiables. Par exemple : `VerifiedCredentialExpert`. Pour plus d’informations, consultez [Créer la carte d’expert en justificatifs vérifiables dans Azure](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application). |
| manifest | string| URL du document du manifeste des justificatifs vérifiables. Pour plus d’informations, consultez [Collecter les informations d’identification et les détails de l’environnement pour configurer votre exemple d’application](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application).|
| réclamations | string| [Facultatif] Incluez une collection d’assertions effectuées sur le sujet dans les justificatifs vérifiables. Pour le flux de code confidentiel, il est important de fournir le prénom et le nom de l’utilisateur. Pour plus d’informations, consultez [Noms des justificatifs vérifiables](verifiable-credentials-configure-issuer.md#verifiable-credential-names). |
| pin | [PIN](#pin-type)| [Facultatif] Numéro de code confidentiel pour fournir une sécurité supplémentaire lors de l’émission. Pour le flux de code confidentiel, cette propriété est obligatoire. Vous générez un code confidentiel et le présentez à l’utilisateur dans votre application. L’utilisateur devra fournir le code confidentiel que vous avez généré. |

### <a name="pin-type"></a>Type de code PIN

Le type PIN définit un code confidentiel qui peut être affiché dans le cadre de l’émission. Le code confidentiel est facultatif et, s’il est utilisé, doit toujours être envoyé hors bande. Lorsque vous utilisez un code confidentiel de code de hachage, vous devez définir les propriétés salt, alg et iterations. Le code confidentiel contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `value` | string| Contient la valeur de code confidentiel en texte brut. Lors de l’utilisation d’un code confidentiel haché, la propriété value contient le code de hachage salé, codé en base64.|
| `type` | string|  Type de code confidentiel. Valeur possible : `numeric` (par défaut). |
| `length` | entier|  Longueur du code confidentiel.  Longueur par défaut : 6. Longueur minimale : 4. Longueur maximale : 16.|
| `salt` | string|  Sel du code confidentiel haché. Le sel est ajouté au début du calcul du code de hachage. Encodage : UTF-8. |
| `alg` | string|  Algorithme de hachage pour le code confidentiel haché. Algorithme pris en charge : `sha256`. |
| `iterations` | entier| Nombre d’itérations de hachage. Valeurs possibles : `1`.|


### <a name="callback-type"></a>Type de rappel

L’API REST du service de demande génère plusieurs événements pour le point de terminaison de rappel. Ces événements vous permettent de mettre à jour l’interface utilisateur et de poursuivre le processus une fois que les résultats sont renvoyés à l’application. Le type Callback contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `url` | string| URI du point de terminaison de rappel de votre application. |
| `state` | string| S’associe à l’état transmis dans la charge utile d’origine. |
| `headers` | string| [Facultatif] Vous pouvez inclure une collection d’en-têtes HTTP requis par le destinataire du message POST. Les en-têtes doivent inclure uniquement la clé API ou tout en-tête requis pour l’autorisation.|

## <a name="successful-response"></a>Réponse correcte

En cas de réussite, cette méthode renvoie un code de réponse HTTP 201 Created et une collection d’objets d’événement dans le corps de la réponse. Le code JSON suivant illustre une réponse réussie :

```json
{  
    "requestId": :"799f23ea-5241-45af-99ad-cf8e5018814e",  
    "url": "openid://vc?request_uri=https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiablecredentials/request/178319f7-20be-4945-80fb-7d52d47ae82e",  
    "expiry": 1622227690,  
    "qrCode": "data:image/png;base64,iVBORw0KggoA<SNIP>"  
} 
```

La réponse contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `requestId`| string | ID de corrélation généré automatiquement. Le [rappel](#callback-events) utilise la même requête. Cela vous permet de garder une trace de la demande d’émission et de ses rappels. |
| `url`|  string| URL qui lance l’application d’authentification et démarre le processus d’émission. Vous pouvez présenter cette URL à l’utilisateur s’il ne parvient pas à scanner le code QR. |
| `expiry`| entier| Indique le moment où la réponse sera expirée. |
| `qrCode`| string | Code QR que l’utilisateur peut scanner pour démarrer le processus d’émission. |

Lorsque votre application reçoit la réponse, l’application doit présenter le code QR à l’utilisateur. L’utilisateur scanne le code QR, ce qui ouvre l’application d’authentification qui démarre le processus d’émission.

## <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être renvoyées afin que l’application puisse les traiter de façon appropriée. Le code JSON suivant montre un message d’erreur non autorisé.


```json
{
    "requestId": "d60b068e7fbd975896e179b99347866a",
    "date": "Wed, 29 Sep 2021 21:49:00 GMT",
    "error": {
        "code": "unauthorized",
        "message": "Failed to authenticate the request."
    }
}
```

La réponse contient les propriétés suivantes :

|Propriété |Type |Description |
|---------|---------|---------|
| `requestId`| string | ID de demande généré automatiquement.|
| `date`| Date| Heure de l’erreur. |
| `error.code` | string| Code d’erreur de retour. |
| `error.message`| string| Message d’erreur. |

## <a name="callback-events"></a>Événements de rappel

Le point de terminaison de rappel est appelé lorsqu’un utilisateur scanne le code QR, utilise le lien profond avec son application d’authentification ou termine le processus d’émission. 


|Propriété |Type |Description |
|---------|---------|---------|
| `requestId`| string | Mappé à la demande d’origine lorsque la charge utile a été publiée sur le service Justificatifs vérifiables.|
| `code` |string |Code renvoyé lorsque la demande a été récupérée par l’application d’authentification. Valeurs possibles : <ul><li>`request_retrieved` : l’utilisateur a scanné le code QR ou cliqué sur le lien qui démarre le processus d’émission.</li><li>`issuance_successful` : l’émission des justificatifs vérifiables a réussi.</li><li>`Issuance_error` : une erreur s’est produite pendant l’émission. Pour plus de détails, consultez la propriété `error`.</li></ul>    |
| `state` |string| L’état renvoie la valeur d’état que vous avez transmise dans la charge utile d’origine.   |
| `error`| error | Lorsque `code` est `Issuance_error`, cette propriété contient des informations sur l’erreur.| 
| `error.code` | string| Code d’erreur de retour. |
| `error.message`| string| Message d’erreur. |

L’exemple suivant illustre une charge utile de rappel lorsque la demande d’émission est démarrée par l’application d’authentification.

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

L’exemple suivant illustre une charge utile de rappel une fois que l’utilisateur a terminé le processus d’émission.

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",
    "code":"issuance_successful",
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
} 
```

### <a name="callback-errors"></a>Erreurs de rappel  

Le point de terminaison de rappel peut être appelé avec un message d’erreur.

Le tableau suivant répertorie les codes d’erreur. Ces détails spécifiques aux erreurs regroupent de manière générique la plupart des erreurs qui pourraient se produire lors de l’émission.

|Message  |Définition    |
|---------|---------|
| `fetch_contract_error*`| Impossible de récupérer le contrat de justificatifs vérifiables. Cette erreur se produit généralement lorsque l’API ne parvient pas à extraire le manifeste que vous spécifiez dans l’[objet RequestIssuance](#requestissuance-type) de la charge utile de la demande.|
| `issuance_service_error*` | Le service Justificatifs vérifiables n’a pas pu valider les exigences, ou une erreur s’est produite du côté du service Justificatifs vérifiables.|
| `unspecified_error`| Une erreur qui n’entre pas dans cette catégorie s’est produite. Cette erreur ne devrait pas être courante, mais elle vaut toujours la peine d’être étudiée. |

L’exemple suivant illustre une charge utile de rappel lorsqu’une erreur s’est produite.

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",  
    "code": "issuance_error",  
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",  
    "error": { 
      "code":"IssuanceFlowFailed", 
      "message":"issuance_service_error”, 
    } 
} 
``` 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’appel de l’API REST du service de demande](get-started-request-api.md)
