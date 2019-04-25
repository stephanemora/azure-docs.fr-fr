---
title: Plateforme d’identité Microsoft utilisé pour connecter les utilisateurs sur les appareils sans navigateur | Azure
description: Générez des flux d’authentification intégrés et sans navigateur à l’aide de l’octroi de code d’appareil.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 703416788d123798774802613d71b30e8fbdaa9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299408"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Plateforme d’identité Microsoft et le flux de code d’appareil OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

L’identité plateforme pour prendre en charge la [octroi de code d’appareil](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), ce qui permet aux utilisateurs de se connecter à des appareils avec contraintes d’entrée comme une télévision intelligente, appareils IoT ou imprimante.  Pour activer ce flux, l’appareil exige que l’utilisateur consulte une page web dans son navigateur sur un autre appareil pour se connecter.  Lorsque l’utilisateur est connecté, l’appareil peut obtenir des jetons d’accès et actualiser les jetons si nécessaire.  

> [!IMPORTANT]
> À ce stade, le point de terminaison Microsoft identity platform prend uniquement en charge le flux de l’appareil pour les locataires Azure AD, mais les comptes non personnels.  Cela signifie que vous devez utiliser un point de terminaison configuré en tant que client, ou la `organizations` point de terminaison.  
>
> Les comptes personnels invités sur un locataire Azure AD peuvent utiliser l’octroi de flux d’appareil, mais uniquement dans le contexte du locataire.

> [!NOTE]
> Le point de terminaison Microsoft identity plateforme ne prend pas en charge tous les scénarios d’Azure Active Directory et les fonctionnalités. Pour déterminer si vous devez utiliser le point de terminaison Microsoft identity plateforme, consultez les [limitations de plateforme d’identité Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Schéma de protocole

Le flux de code d’appareil complet est similaire à l’illustration suivante. Nous décrirons en détail chacune des étapes plus loin dans cet article.

![Flux de code d’appareil](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Requête d’autorisation d’appareil

Le client doit d’abord vérifier avec le serveur d’authentification pour un appareil et le code utilisateur qui est utilisé pour lancer l’authentification. Le client collecte cette requête à partir du point de terminaison `/devicecode`. Dans cette requête, le client doit également inclure les autorisations dont il a besoin d’obtenir auprès de l’utilisateur. À partir du moment où cette requête est envoyée, l’utilisateur ne dispose que de 15 minutes pour se connecter (la valeur habituelle de `expires_in`). N’effectuez cette requête que lorsque l’utilisateur a indiqué qu’il est prêt à se connecter.

> [!TIP]
> Essayez d'exécuter cette requête dans Postman !
> [![Exécuter dans Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Paramètre | Condition | Description |
| --- | --- | --- |
| `tenant` | Obligatoire |Le client d’annuaire auquel vous souhaitez demander l’autorisation. Peut être au format GUID ou sous forme de nom convivial.  |
| `client_id` | Obligatoire | Le **ID d’Application (client)** qui le [portail Azure-inscriptions](https://go.microsoft.com/fwlink/?linkid=2083908) expérience affecté à votre application. |
| `scope` | Recommandé | Liste séparée par des espaces d’ [étendues](v2-permissions-and-consent.md) pour lesquelles vous souhaitez que l’utilisateur donne son consentement.  |

### <a name="device-authorization-response"></a>Réponse d’autorisation d’appareil

Une réponse réussie est un objet JSON contenant les informations requises pour autoriser l’utilisateur à se connecter.  

| Paramètre | Format | Description |
| ---              | --- | --- |
|`device_code`     | String | Chaîne longue utilisée pour vérifier la session entre le client et le serveur d’autorisation. Le client utilise ce paramètre pour demander le jeton d’accès à partir du serveur d’autorisation. |
|`user_code`       | String | Une chaîne courte indiquée à l’utilisateur qui est utilisé pour identifier la session sur un appareil secondaire.|
|`verification_uri`| URI | URI auquel l’utilisateur doit accéder avec le `user_code` pour pouvoir se connecter. |
|`verification_uri_complete`| URI | Un URI qui combine le `user_code` et `verification_uri`, utilisé pour la transmission non textuelles à l’utilisateur (par exemple, via Bluetooth sur un appareil, ou via un code QR).  |
|`expires_in`      | int | Nombre de secondes avant l’expiration de `device_code` et `user_code`. |
|`interval`        | int | Nombre de secondes d’attente du client entre chaque requête d’interrogation. |
| `message`        | String | Chaîne lisible par l’homme contenant des instructions pour l’utilisateur. Elle peut être localisée en incluant un **paramètre de requête** dans la requête sous la forme `?mkt=xx-XX`, en remplissant le code de langue approprié. |

## <a name="authenticating-the-user"></a>Authentification de l’utilisateur

Après avoir reçu le `user_code` et `verification_uri`, le client s’affiche aux utilisateurs, les invitant à vous connecter à l’aide de leur téléphone mobile ou le navigateur de PC.  En outre, le client peut utiliser un code QR ou un mécanisme similaire pour afficher le `verfication_uri_complete`. L’étape de saisie du `user_code` est alors présentée à l’utilisateur.

Tandis que l’utilisateur s’authentifie auprès de `verification_uri`, le client doit interroger le point de terminaison `/token` pour le jeton demandé à l’aide du `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Paramètre | Obligatoire | Description|
| -------- | -------- | ---------- |
| `grant_type` | Obligatoire | Doit être `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obligatoire | Doit correspondre au `client_id` utilisé dans la requête initiale. |
| `device_code`| Obligatoire | `device_code` retourné dans la requête d’autorisation d’appareil.  |

### <a name="expected-errors"></a>Erreurs attendues

Le flux de code d’appareil étant un protocole d’interrogation de votre client doit s’attendre à recevoir des erreurs avant que l’utilisateur ait terminé l’authentification.  

| Error | Description | Action du client |
| ------ | ----------- | -------------|
| `authorization_pending` | L’utilisateur n’a pas terminé l’authentification, mais n’a pas annulé le flux. | Répétez la requête après `interval` secondes minimum. |
| `authorization_declined` | L’utilisateur final a refusé la requête d’autorisation.| Arrêtez l’interrogation et revenez à un état non authentifié.  |
| `bad_verification_code`| Le `device_code` envoyé à la `/token` point de terminaison n’est pas reconnu. | Vérifiez que le client envoie le `device_code` approprié dans la requête. |
| `expired_token` | `expires_in` secondes minimum se sont écoulées, et l’authentification n’est plus possible avec ce `device_code`. | Arrêter d’interrogation et revenir à un état non authentifié. |

### <a name="successful-authentication-response"></a>Réponse d’authentification réussie

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
| `token_type` | String| Toujours Porteur. |
| `scope` | Chaînes séparées par un espace | Si un jeton d’accès est retourné, répertorie les étendues pour lesquelles le jeton d’accès est valide. |
| `expires_in`| int | Nombre de secondes avant lequel le jeton d’accès fourni est valide. |
| `access_token`| Chaîne opaque | Émise pour les [étendues](v2-permissions-and-consent.md) qui ont été demandées.  |
| `id_token`   | JWT | Émis si le paramètre `scope` d’origine inclut l’étendue `openid`.  |
| `refresh_token` | Chaîne opaque | Émise si le paramètre `scope` d’origine inclut `offline_access`.  |

Vous pouvez utiliser le jeton d’actualisation pour acquérir de nouveaux jetons d’accès et actualisation des jetons à l’aide du même flux documenté dans la [documentation de flux de OAuth Code](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
