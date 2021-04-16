---
title: Exemples de communication du service émetteur (préversion) – Justificatifs vérifiables Azure Active Directory
description: Détails de la communication entre le fournisseur d’identité et le service émetteur
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 8771c61f96b244e0cc0bca1c61ceb8042b4a5b4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220196"
---
# <a name="issuer-service-communication-examples-preview"></a>Exemples de communication du service émetteur (Préversion)

Le service émetteur de justificatifs vérifiables peut émettre des justificatifs vérifiables en extrayant les revendications d’un jeton d’ID généré par le fournisseur d’identité conforme à OpenID de votre organisation. Cet article vous indique comment configurer votre fournisseur d’identité pour que l’application Authenticator puisse communiquer avec lui et récupérer le jeton d’ID correct à transmettre au service émetteur. 

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Pour émettre des justificatifs vérifiables, Authenticator reçoit des instructions lors du téléchargement du contrat, pour collecter l’entrée de l’utilisateur et envoyer ces informations au service émetteur. Si vous devez utiliser un jeton d’ID, vous devez configurer votre fournisseur d’identité pour permettre à Authenticator de se connecter à un utilisateur à l’aide du protocole OpenID Connect. Les revendications du jeton d’ID résultant sont utilisées pour remplir le contenu de vos justificatifs vérifiables. L’application Authenticator authentifie l’utilisateur à l’aide du flux de code d’autorisation OpenID Connect. Votre fournisseur OpenID doit prendre en charge les fonctionnalités OpenID Connect suivantes : 

| Fonctionnalité | Description |
| ------- | ----------- |
| Type d’autorisation | Doit prendre en charge le type d’octroi de code d’autorisation. |
| Format de jeton | Doit produire des jetons JWT (JSON Web Token) compacts non chiffrés. |
| Algorithme de signature | Doit produire des jetons JWT signés selon la norme RSA 256. |
| Document de configuration | Doit prendre en charge le document de configuration OpenID Connect et `jwks_uri`. | 
| Inscription du client | Doit prendre en charge l’inscription du client public en utilisant une valeur `redirect_uri` de `vclient://openid/` . | 
| PKCE | Recommandé pour des raisons de sécurité, mais pas obligatoire. |

Vous trouverez ci-dessous des exemples de requêtes HTTP envoyées à votre fournisseur d’identité. Votre fournisseur d’identité doit accepter et répondre à ces demandes conformément à la norme d’authentification OpenID Connect.

## <a name="client-registration"></a>Inscription du client

Pour recevoir des justificatifs vérifiables, vos utilisateurs doivent se connecter à votre fournisseur d’identité (ou « IDP », pour « Identity Provider ») en utilisant l’application Microsoft Authenticator. 

Pour activer cet échange, inscrivez une application auprès de votre fournisseur d’identité. Si vous utilisez Azure AD, vous trouverez les instructions [ici](../develop/quickstart-register-app.md). Utilisez les valeurs suivantes lors de l’inscription.

| Paramètre | Valeur |
| ------- | ----- |
| Nom de l'application | `<Issuer Name> Verifiable Credential Service` |
| URI de redirection | `vcclient://openid/ ` |


Une fois que vous avez inscrit une application auprès de votre fournisseur d’identité, enregistrez son ID client. Vous en aurez besoin dans la prochaine section. Vous devez également écrire l’URL pointant vers le point de terminaison connu pour le fournisseur d’identité compatible OIDC. Le service émetteur utilise ce point de terminaison pour télécharger les clés publiques nécessaires à la validation du jeton d’ID une fois qu’il est envoyé par Authenticator.

L’URI de redirection configurée est utilisé par Authenticator afin qu’il sache quand la connexion est terminée et qu’il peut récupérer le jeton d’ID. 

## <a name="authorization-request"></a>Demande d’autorisation.

La demande d’autorisation envoyée à votre fournisseur d’identité utilise le format suivant.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Paramètre | Valeur |
| ------- | ----------- |
| `client_id` | ID client obtenu lors du processus d’inscription de l’application. |
| `redirect_uri` | Doit utiliser `vcclient://openid/`. |
| `response_mode` | Doit prendre en charge `query`. |
| `response_type` | Doit prendre en charge `code`. |
| `scope` | Doit prendre en charge `openid`. |
| `state` | Doit être renvoyé au client selon la norme OpenID Connect. |
| `nonce` | Doit être retourné en tant que revendication dans le jeton d’ID selon la norme OpenID Connect. |

Lorsqu’il reçoit une demande d’autorisation, votre fournisseur d’identité doit authentifier l’utilisateur et prendre les mesures nécessaires pour mener à bien la connexion, par exemple l’authentification multifacteur.

Vous pouvez personnaliser le processus de connexion selon vos besoins. Vous pouvez demander aux utilisateurs de fournir des informations supplémentaires, d’accepter des conditions de service, de payer leurs informations d’identification, et bien plus encore. Une fois toutes les étapes terminées, répondez à la demande d’autorisation en redirigeant vers l’URI de redirection, comme indiqué ci-dessous. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Paramètre | Valeur |
| ------- | ----------- |
| `code` |  Code d’autorisation retourné par votre fournisseur d’identité. |
| `state` | Doit être renvoyé au client selon la norme OpenID Connect. |

## <a name="token-request"></a>Requête de jeton

La requête de jeton envoyée à votre fournisseur d’identité aura la forme suivante.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Paramètre | Valeur |
| ------- | ----------- |
| `client_id` | ID client obtenu lors du processus d’inscription de l’application. |
| `redirect_uri` | Doit utiliser `vcclient://openid/`. |
| `scope` | Doit prendre en charge `openid`. |
| `grant_type` | Doit prendre en charge `authorization_code`. |
| `code` | Code d’autorisation retourné par votre fournisseur d’identité. |

Lors de la réception de la requête de jeton, votre fournisseur d’identité doit répondre avec un jeton d’ID.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

Le jeton d’ID doit utiliser le format de sérialisation compact JWT et ne doit pas être chiffré. Le jeton d’ID doit contenir les revendications suivantes.

| Revendication | Valeur |
| ------- | ----------- |
| `kid` | Identificateur de la clé utilisée pour signer le jeton d’ID, correspondant à une entrée dans le `jwks_uri` du fournisseur OpenID. |
| `aud` | ID client obtenu lors du processus d’inscription de l’application. |
| `iss` | Doit avoir la valeur `issuer` dans votre document de configuration OpenID Connect. |
| `exp` | Doit contenir l’heure d’expiration du jeton d’ID. |
| `iat` | Doit contenir l’heure à laquelle le jeton d’ID a été émis. |
| `nonce` | Valeur incluse dans la requête d’autorisation. |
| Revendications supplémentaires | Le jeton d’ID doit contenir toutes les revendications supplémentaires dont les valeurs seront incluses dans les justificatifs vérifiables qui seront émis. Dans cette section, vous devez inclure tous les attributs de l’utilisateur, tels que leur nom. |

## <a name="next-steps"></a>Étapes suivantes

- [Comment personnaliser vos justificatifs vérifiables Azure Active Directory](credential-design.md)