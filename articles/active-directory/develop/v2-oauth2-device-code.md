---
title: Utiliser Azure AD v2.0 pour connecter des utilisateurs sur des appareils sans navigateur | Microsoft Docs
description: Générez des flux d’authentification intégrés et sans navigateur à l’aide de l’octroi de code d’appareil.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e05bf723507ba12b577605d44ddb3aaef3b19efe
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079619"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-device-code-flow"></a>Azure Active Directory v2.0 et le flux de code d’appareil OAuth 2.0

Azure AD prend en charge l’[octroi de code d’appareil](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), ce qui permet aux utilisateurs de se connecter à des appareils à entrée limitée comme une télévision connectée, un appareil IoT ou une imprimante.  Pour activer ce flux, l’appareil exige que l’utilisateur consulte une page web dans son navigateur sur un autre appareil pour se connecter.  Lorsque l’utilisateur est connecté, l’appareil peut obtenir des jetons d’accès et actualiser les jetons si nécessaire.  

> [!Important] 
> À ce stade, le point de terminaison v2.0 prend uniquement en charge le flux d’appareil pour les locataires Azure AD, mais pas les comptes personnels.  Cela signifie que vous devez utiliser un point de terminaison avec locataire ou le point de terminaison d’organisations.  
>
> Les comptes personnels invités sur un locataire Azure AD peuvent utiliser l’octroi de flux d’appareil, mais uniquement dans le contexte du locataire.

> [!NOTE]
> Le point de terminaison v2.0 ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure Active Directory. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).
>

## <a name="protocol-diagram"></a>Schéma de protocole

Le flux de code d’appareil complet est similaire à l’illustration suivante. Nous décrirons en détail chacune des étapes plus loin dans cet article.

![Flux de code d’appareil](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>Requête d’autorisation d’appareil

Le client doit d’abord rechercher sur le serveur d’authentification un appareil et un code utilisateur, utilisés pour lancer l’authentification.  Le client collecte cette requête à partir du point de terminaison `/devicecode`. Dans cette requête, le client doit également inclure les autorisations dont il a besoin d’obtenir auprès de l’utilisateur.  À partir du moment où cette requête est envoyée, l’utilisateur ne dispose que de 15 minutes pour se connecter (la valeur habituelle de `expires_in`). N’effectuez cette requête que lorsque l’utilisateur a indiqué qu’il est prêt à se connecter.

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Paramètre | Condition | Description |
| --- | --- | --- |
| locataire |Obligatoire |Le client d’annuaire auquel vous souhaitez demander l’autorisation. Peut être au format GUID ou sous forme de nom convivial.  |
| client_id |Obligatoire |ID d’application que le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a affecté à votre application. |
| scope | Recommandé | Liste séparée par des espaces d’ [étendues](v2-permissions-and-consent.md) pour lesquelles vous souhaitez que l’utilisateur donne son consentement.  |

### <a name="device-authorization-response"></a>Réponse d’autorisation d’appareil

Une réponse réussie est un objet JSON contenant les informations requises pour autoriser l’utilisateur à se connecter.  

| Paramètre | Format | Description |
| ---              | --- | --- |
|`device_code`     |Chaîne| Chaîne longue utilisée pour vérifier la session entre le client et le serveur d’autorisation.  Elle est utilisée par le client pour demander le jeton d’accès au serveur d’autorisation. |
|`user_code`       |Chaîne| Chaîne courte présentée à l’utilisateur, utilisée pour identifier la session sur un appareil secondaire.|
|`verification_uri`|URI| URI auquel l’utilisateur doit accéder avec le `user_code` pour pouvoir se connecter. |
|`verification_uri_complete`|URI| URI combinant le `user_code` et le `verification_uri`, utilisé pour la transmission non textuelle à l’utilisateur (par exemple, via Bluetooth sur un appareil ou via un code QR).  |
|`expires_in`      |int| Nombre de secondes avant l’expiration de `device_code` et `user_code`. |
|`interval`        |int| Nombre de secondes d’attente du client entre chaque requête d’interrogation. |
| `message`        |Chaîne| Chaîne lisible par l’homme contenant des instructions pour l’utilisateur.  Elle peut être localisée en incluant un **paramètre de requête** dans la requête sous la forme `?mkt=xx-XX`, en remplissant le code de langue approprié. |

## <a name="authenticating-the-user"></a>Authentification de l’utilisateur

Après avoir reçu `user_code` et `verification_uri`, le client les présente aux utilisateurs, en les invitant à se connecter à l’aide de leur téléphone mobile ou navigateur PC.  En outre, le client peut utiliser un code QR ou un mécanisme similaire pour afficher le `verfication_uri_complete`. L’étape de saisie du `user_code` est alors présentée à l’utilisateur.

Tandis que l’utilisateur s’authentifie auprès de `verification_uri`, le client doit interroger le point de terminaison `/token` pour le jeton demandé à l’aide du `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

|Paramètre | Obligatoire | Description|
| -------- | -------- | ---------- |
|`grant_type` | Obligatoire| Doit être `urn:ietf:params:oauth:grant-type:device_code`|
|`client_id`  | Obligatoire| Doit correspondre au `client_id` utilisé dans la requête initiale. |
|`device_code`| Obligatoire| `device_code` retourné dans la requête d’autorisation d’appareil.  |

### <a name="expected-errors"></a>Erreurs attendues

Étant donné que le flux de code d’appareil est un protocole d’interrogation, votre client doit s’attendre à recevoir des erreurs avant que l’utilisateur ait terminé l’authentification.  

| Error | Description | Action du client |
|------ | ----------- | -------------|
| `authorization_pending` |  L’utilisateur n’a pas encore terminé l’authentification, mais n’a pas annulé le flux. | Répétez la requête après `interval` secondes minimum. |
| `authorization_declined`|  L’utilisateur final a refusé la requête d’autorisation.| Arrêtez l’interrogation et revenez à un état non authentifié.  |
| `bad_verification_code`|Le `device_code` envoyé au point de terminaison `/token` n’a pas été reconnu. | Vérifiez que le client envoie le `device_code` approprié dans la requête. |
| `expired_token`|  `expires_in` secondes minimum se sont écoulées, et l’authentification n’est plus possible avec ce `device_code`. | Arrêtez l’interrogation et revenez à un état non authentifié. |


### <a name="succesful-authentication-response"></a>Réponse d’authentification réussie

Une réponse de jeton réussie se présente ainsi :

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Paramètre | Format | Description |
| --------- | ------ | ----------- |
|`token_type` | Chaîne| Toujours Porteur. |
|`scope` | Chaînes séparées par un espace | Si un jeton d’accès est retourné, répertorie les étendues pour lesquelles le jeton d’accès est valide. |
|`expires_in`| int | Nombre de secondes avant lequel le jeton d’accès fourni est valide. |
|`access_token`| Chaîne opaque | Émise pour les [étendues](v2-permissions-and-consent.md) qui ont été demandées.  |
|`id_token`   | JWT | Émis si le paramètre `scope` d’origine inclut l’étendue `openid`.  |
|`refresh_token` | Chaîne opaque | Émise si le paramètre `scope` d’origine inclut `offline_access`.  |

Le jeton d’actualisation peut être utilisé pour acquérir de nouveaux jetons d’accès et actualiser des jetons à l’aide du même flux détaillé dans la [documentation du flux de code OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  