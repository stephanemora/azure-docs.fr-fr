---
title: Étendues de plateforme Microsoft identity, autorisations et consentement | Microsoft Docs
description: Une description de l’autorisation dans le Microsoft identity plateforme point de terminaison, notamment les étendues, autorisations et consentement.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 717607de6d9d193a7373637d0d0fcd879b54fed0
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544881"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Autorisations et consentement dans le point de terminaison Microsoft identity platform

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Les applications qui s’intègrent à la plateforme d’identité Microsoft suivent un modèle d’autorisation permettant aux utilisateurs et aux administrateurs de contrôler l’accès aux données. L’implémentation du modèle d’autorisation a été mis à jour sur le point de terminaison Microsoft identity plateforme, et elle modifie la façon dont une application doit interagir avec la plateforme Microsoft identity. Cet article aborde les concepts de base de ce modèle d’autorisation, notamment les étendues, les autorisations et le consentement.

> [!NOTE]
> Le point de terminaison Microsoft identity plateforme ne prend pas en charge tous les scénarios et fonctionnalités. Pour déterminer si vous devez utiliser le point de terminaison Microsoft identity plateforme, consultez les [limitations de plateforme d’identité Microsoft](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Étendues et autorisations

La plateforme d’identité Microsoft implémente le protocole d’autorisation [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 est une méthode par le biais de laquelle une application tierce peut accéder aux ressources hébergées sur le web au nom d’un utilisateur. Les ressources hébergées sur le web qui s’intègrent à la plateforme d’identité Microsoft présentent un identificateur de ressource, également appelé *URI d’ID d’application*. Voici, par exemple, quelques-unes des ressources hébergées sur le Web de Microsoft :

* Microsoft Graph : `https://graph.microsoft.com`
* API de messagerie Office 365 : `https://outlook.office.com`
* Azure AD Graph : `https://graph.windows.net`

> [!NOTE]
> Nous vous recommandons fortement d’utiliser Microsoft Graph au lieu d’Azure AD Graph, l’API de messagerie Office 365, etc.

Cela s’applique également aux ressources tierces intégrées à la plateforme d’identité Microsoft. Ces ressources peuvent également définir un ensemble d’autorisations à utiliser pour diviser la fonctionnalité de cette ressource en fragments plus réduits. Par exemple, [Microsoft Graph](https://graph.microsoft.com) a défini des autorisations pour effectuer les tâches suivantes, entre autres :

* Lire le calendrier d’un utilisateur
* Écrire dans le calendrier d’un utilisateur
* Envoyer un e-mail en tant qu'utilisateur

En définissant ces types d’autorisations, la ressource dispose d’un contrôle précis sur ses données et sur l’exposition de la fonctionnalité d’API. Une application tierce peut demander ces autorisations à des utilisateurs et des administrateurs. Ces derniers doivent approuver la requête avant que l’application puisse accéder aux données ou agir pour le compte d’un utilisateur. En fragmentant les fonctionnalités de la ressource en ensembles plus réduits d’autorisations, il est possible de créer des applications tierces pour qu’elles demandent uniquement les autorisations nécessaires à leur fonctionnement. Les utilisateurs et les administrateurs peuvent savoir exactement quelles données l’application a accès à, et ils peuvent être plus confiants qu’elle n’est pas comporte ayant des intentions malveillantes. Les développeurs doivent toujours respecter le concept de moindre privilège et demander uniquement les autorisations dont ils ont besoin pour faire fonctionner leurs applications.

Dans OAuth 2.0, ces types d’autorisations sont appelés des *étendues*. Elles aussi souvent appelées *autorisations*. Une autorisation est représentée dans la plateforme d’identité Microsoft sous forme de valeur de chaîne. Toujours dans l’exemple Microsoft Graph, la valeur de chaîne pour chaque autorisation est la suivante :

* Lire le calendrier d’un utilisateur en utilisant `Calendars.Read`
* Écrire dans le calendrier d’un utilisateur en utilisant `Calendars.ReadWrite`
* Envoi de messages en tant qu’utilisateur en utilisant `Mail.Send`

Une application demande généralement ces autorisations en spécifiant les étendues dans les demandes à la plateforme Microsoft identity autorisent le point de terminaison. Toutefois, certaines autorisations doté de privilèges élevés peuvent uniquement être accordées via le consentement de l’administrateur et demandé/accordées à l’aide du [point de terminaison de consentement administrateur](v2-permissions-and-consent.md#admin-restricted-permissions). Lisez la suite pour en savoir plus.

## <a name="permission-types"></a>Types d'autorisations

La plateforme d’identité Microsoft prend en charge deux types d’autorisations : les **autorisations déléguées** et les **autorisations d’application**.

* Les **autorisations déléguées** sont utilisées par les applications pour lesquelles un utilisateur est connecté et présent. Pour ces applications, l’utilisateur ou un administrateur donne son consentement pour les autorisations que les demandes d’application et que l’application est une autorisation déléguée à agir en tant que l’utilisateur connecté lors des appels à la ressource cible. Certaines autorisations déléguées peuvent être accordées par des utilisateurs non administrateurs, mais certaines autorisations à privilèges élevés requièrent le [consentement de l’administrateur](v2-permissions-and-consent.md#admin-restricted-permissions). Pour connaître les rôles administrateur habilités à donner leur consentement pour les autorisations déléguées, consultez [Autorisations du rôle administrateur dans Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* Les **autorisations d’application** sont utilisées par les applications qui s’exécutent sans qu’un utilisateur soit connecté et présent (les applications qui s’exécutent en tant que services ou démons en arrière-plan, par exemple).  Les autorisations de l’application peuvent uniquement être [accordées par un administrateur](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

Les _autorisations effectives_ correspondent aux autorisations accordées à votre application lorsqu’elle envoie des requêtes à une ressource cible. Il est important de comprendre la différence entre le délégué et les autorisations d’application est accordée à votre application et ses autorisations effectives lors des appels à la ressource cible.

- Pour les autorisations déléguées, les _autorisations effectives_ de votre application correspondent au niveau de privilège le moins élevé entre les autorisations déléguées que l’application a reçues (par le biais d’un consentement) et les privilèges de l’utilisateur actuellement connecté. Votre application ne peut jamais avoir plus de privilèges que l’utilisateur connecté. Au sein des organisations, les privilèges de l’utilisateur connecté peuvent être déterminés par la stratégie ou l’appartenance à un ou plusieurs rôles d’administrateur. Pour connaître les rôles administrateur habilités à donner leur consentement pour les autorisations déléguées, consultez [Autorisations du rôle administrateur dans Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Supposons que votre application ait reçu l’autorisation déléguée _User.ReadWrite.All_. Cette autorisation permet nominalement à votre application de lire et mettre à jour le profil de chaque utilisateur dans une organisation. Si l’utilisateur connecté est un administrateur général, votre application est en mesure de mettre à jour le profil de chaque utilisateur de l’organisation. Toutefois, si l’utilisateur connecté n’est pas dans un rôle d’administrateur, votre application sera en mesure de mettre à jour uniquement le profil de l’utilisateur connecté. Elle ne peut pas mettre à jour les profils des autres utilisateurs de l’organisation, car l’utilisateur pour lequel elle est autorisée à agir n’a pas ces privilèges.
  
- Pour les autorisations d’application, les _autorisations effectives_ de votre application correspondent au niveau complet des privilèges impliqués par l’autorisation. Par exemple, une application disposant de l’autorisation d’application _User.ReadWrite.All_ peut mettre à jour le profil de chaque utilisateur de l’organisation. 

## <a name="openid-connect-scopes"></a>Étendues OpenId Connect

L’implémentation de plateforme d’identité Microsoft d’OpenID Connect comprend quelques étendues bien définies qui ne s’appliquent pas à une ressource spécifique : `openid`, `email`, `profile`, et `offline_access`. Les étendues OpenID Connect `address` et `phone` ne sont pas prises en charge.

### <a name="openid"></a>openid

Si une application exécute la connexion à l’aide [d’OpenID Connect](active-directory-v2-protocols.md), elle doit solliciter l’étendue `openid`. L’étendue `openid` s’affiche sur la page de consentement du compte professionnel, en tant qu’autorisation de connexion, et sur la page de consentement du compte Microsoft personnel en tant qu’autorisation « Afficher votre profil et se connecter aux applications et aux services à l’aide de votre compte Microsoft ». Avec cette autorisation, une application peut recevoir un identifiant utilisateur unique sous la forme de la revendication `sub`. Elle permet également à l’application d’accéder au point de terminaison des informations utilisateur. Le `openid` étendue peut être utilisée sur le point de terminaison de Microsoft identity plateforme jeton pour acquérir des jetons d’ID, qui peuvent être utilisées par l’application pour l’authentification.

### <a name="email"></a>email

L’étendue `email` peut être utilisée avec l’étendue `openid` ainsi que d’autres. Elle permet à l’application d’accéder à l’adresse de messagerie principale de l’utilisateur sous la forme de la revendication `email`. Le `email` revendication est incluse dans un jeton uniquement si une adresse de messagerie est associée au compte d’utilisateur, ce qui n’est pas toujours le cas. Si elle utilise l’étendue `email`, votre application doit être préparée à faire face à l’éventualité où la revendication `email` n’existerait pas dans le jeton.

### <a name="profile"></a>profil

L’étendue `profile` peut être utilisée avec l’étendue `openid` ainsi que d’autres. Elle permet à l’application d’accéder à une quantité d’informations importante sur l’utilisateur, Il peut accéder aux informations inclut, mais n’est pas limitées à l’utilisateur prénom, nom de famille, nom d’utilisateur privilégié et ID d’objet. Pour obtenir la liste complète des revendications de profil disponibles dans le paramètre id_token pour un utilisateur donné, consultez la page [de référence `id_tokens`](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

[L’étendue `offline_access`](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permet à votre application d’accéder aux ressources pour le compte de l’utilisateur pendant une période prolongée. Dans la page de consentement, cette étendue apparaît comme l’autorisation « Conserver l’accès aux données auxquelles vous lui avez donné l’accès ». Lorsqu’un utilisateur approuve la `offline_access` étendue, votre application peut recevoir des jetons d’actualisation du point de terminaison de Microsoft identity plateforme jeton. Les jetons d’actualisation sont de longue durée. Votre application peut obtenir de nouveaux jetons d’accès lorsque les plus anciens arrivent à expiration.

Si votre application ne sollicite pas explicitement l’étendue `offline_access`, elle ne reçoit pas de jetons d’actualisation. Ainsi, lorsque vous échangez un code d’autorisation dans le [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md), vous recevez uniquement un jeton d’accès du point de terminaison `/token`. Le jeton d’accès est valide pendant une courte durée : il arrive généralement à expiration en une heure. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Pendant ce réacheminement, en fonction du type d’application, l’utilisateur peut devoir entrer à nouveau ses informations d’identification ou accepter une nouvelle fois les autorisations. Bien que le `offline_access` étendue est automatiquement demandé par le serveur, votre client doit toujours demander afin de recevoir les jetons d’actualisation.

Pour plus d’informations sur comment obtenir et utiliser des jetons d’actualisation, consultez le [référence de protocole plateforme Microsoft identity](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Demande de consentement d’utilisateur individuel

Dans une demande d’autorisation [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), une application peut demander les autorisations nécessaires à l’aide du paramètre de requête `scope`. Par exemple, lorsqu’un utilisateur se connecte à une application, cette dernière envoie une requête semblable à l’exemple ci-dessous (avec des sauts de ligne ajoutés pour une meilleure lisibilité) :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Le paramètre `scope` correspond à une liste d’autorisations déléguées séparées par des espaces, demandées par l’application. Chaque autorisation est indiquée par l’ajout de la valeur correspondante à l’identificateur de la ressource (URI d’ID d’application). Dans l’exemple de requête, l’application nécessite l’autorisation de lecture du calendrier de l’utilisateur et d’envoi de messages au nom de l’utilisateur.

Une fois que l’utilisateur entre ses informations d’identification, le point de terminaison Microsoft identity plateforme vérifie pour un enregistrement correspondant de *consentement de l’utilisateur*. Si l’utilisateur n’a pas donné son consentement pour les autorisations demandées dans le passé, ni a un administrateur a donné son consentement à ces autorisations pour le compte de toute l’organisation, le point de terminaison Microsoft identity plateforme invite l’utilisateur à accorder les autorisations demandées.

> [!NOTE]
> À ce stade, les autorisations `offline_access` (« Conserver l’accès aux données auxquelles vous lui avez donné l’accès ») et `user.read` (« Vous connecter et lire votre profil ») sont automatiquement incluses dans le consentement initial pour une application.  Ces autorisations sont généralement requises pour la fonctionnalité d’application appropriée : `offline_access` fournit à l’application l’accès aux jetons d’actualisation, critiques pour les applications natives et web, tandis que `user.read` donne accès à la revendication `sub`, ce qui permet au client ou à l’application d’identifier correctement l’utilisateur au fil du temps et d’accéder aux informations utilisateur de base.  

![Consentement dans le compte professionnel](./media/v2-permissions-and-consent/work_account_consent.png)

Lorsque l’utilisateur approuve la demande d’autorisation, le consentement est enregistré de manière à ce que l’utilisateur n’ait pas à le fournir à nouveau lors des connexions suivantes à l’application.

## <a name="requesting-consent-for-an-entire-tenant"></a>Demande de consentement d’un client entier

Souvent, lorsqu’une organisation achète une licence ou un abonnement à une application, elle souhaite configurer proactivement l’application afin que tous les membres de l’organisation puissent l’utiliser. Dans le cadre de cette procédure, un administrateur peut autoriser l’application à agir au nom de n’importe quel utilisateur au sein du locataire. Si l’administrateur donne son consentement pour l’intégralité du locataire, les utilisateurs de l’organisation ne voient pas de page de consentement pour l’application.

Pour demander le consentement pour les autorisations déléguées pour tous les utilisateurs d’un locataire, votre application peut utiliser le point de terminaison de consentement de l’administrateur.

En outre, les applications doivent utiliser le point de terminaison de consentement de l’administrateur pour demander les autorisations d’application.

## <a name="admin-restricted-permissions"></a>Autorisations restreintes aux administrateurs

Certaines autorisations à privilège élevé de l’écosystème Microsoft peuvent être définies sur *restreintes aux administrateurs*. Ces types d’autorisations peuvent être illustrés par ces exemples :

* Lire les profils complets de tous les utilisateurs à l’aide de `User.Read.All`
* Écrire des données dans le répertoire d’une organisation à l’aide de `Directory.ReadWrite.All`
* Lire tous les groupes dans le répertoire d’une organisation à l’aide de `Groups.Read.All`

Si un utilisateur consommateur peut accorder à une application l’accès à ce type de données, les utilisateurs d’organisation sont limités lorsqu’il s’agit d’octroyer l’accès au même jeu de données d’entreprise sensibles. Si votre application demande l’accès à un de ces autorisations à partir d’un utilisateur d’organisation, l’utilisateur reçoit un message d’erreur indiquant qu’ils ne sont pas autorisés à donner son consentement pour les autorisations de votre application.

Si votre application requiert l’accès aux étendues restreintes aux administrateurs pour les organisations, vous devez demander l’autorisation directement à un administrateur d’entreprise également à l’aide du point de terminaison de consentement de l’administrateur, décrit ci-dessous.

Si l’application demande des autorisations déléguées à privilèges élevés et qu’un administrateur accorde ces autorisations via le point de terminaison de consentement de l’administrateur, le consentement est accordé à tous les utilisateurs du locataire.

Si l’application demande des autorisations d’application et un administrateur accorde que ces autorisations via l’administrateur de point de terminaison de consentement, cette attribution n’est pas effectuée à la place de n’importe quel utilisateur spécifique. L’application cliente reçoit les autorisations *directement*. Ces types d’autorisations sont uniquement utilisés par les services de démon et d’autres applications non interactives qui s’exécutent en arrière-plan.

## <a name="using-the-admin-consent-endpoint"></a>Utilisation du point de terminaison de consentement administrateur

Lorsqu’un administrateur d’entreprise utilise votre application et qu’il est dirigé vers le point de terminaison d’autorisation, la plateforme d’identité Microsoft détecte le rôle de l’utilisateur et lui demande s’il souhaite donner son consentement pour le compte de l’intégralité du locataire pour les autorisations que vous avez demandées. Toutefois, il existe également un point de terminaison de consentement de l’administrateur dédié que vous pouvez utiliser si vous souhaitez demander proactivement qu’un administrateur accorde son autorisation pour le compte de l’intégralité du locataire. À l’aide de ce point de terminaison est également nécessaire pour demander des autorisations d’Application (qui ne peut pas être demandée à l’aide du point de terminaison authorize).

Si vous suivez ces étapes, votre application peut demander des autorisations pour tous les utilisateurs d’un locataire, notamment les étendues restreintes aux administrateurs. Il s’agit d’une opération à privilèges élevés qui doit être effectuée uniquement si elle est nécessaire dans votre scénario.

Pour voir un exemple de code qui implémente les étapes, consultez [l’exemple d’étendues restreintes aux administrateurs](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail d’inscription de l’application

Le consentement de l’administrateur n’accepte de paramètre d’étendue. Ainsi, toute autorisation demandée doit être définie de manière statique dans l’inscription de l’application. En règle générale, il est recommandé de vous assurer que les autorisations définies de manière statique pour une application donnée sont un sur-ensemble des autorisations demande dynamiquement/façon incrémentielle.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Pour configurer la liste des autorisations statiquement demandées pour une application

1. Accédez à votre application dans le [portail Azure-inscriptions](https://go.microsoft.com/fwlink/?linkid=2083908) rencontrer, ou [créer une application](quickstart-register-app.md) si vous n’avez pas déjà.
2. Recherchez la section **Autorisations pour Microsoft Graph**, puis ajoutez les autorisations nécessaires à votre application.
3. **Enregistrez** l’inscription de l’application.

### <a name="recommended-sign-the-user-into-your-app"></a>Recommandé : connecter l’utilisateur à votre application

En général, lorsque vous créez une application qui utilise le point de terminaison de consentement de l’administrateur, l’application doit disposer d’une page ou vue dans laquelle l’administrateur peut approuver ses autorisations. Cette page peut faire partie du flux d’inscription de l’application, des paramètres de l’application, ou ce peut être un flux de connexion dédié. Dans de nombreux cas, il est judicieux pour l’application d’afficher la vue de « connexion » uniquement après qu’un utilisateur se soit connecté avec un compte Microsoft professionnel ou scolaire.

Lorsque vous connectez l’utilisateur à votre application, vous pouvez identifier l’organisation à laquelle l’administrateur appartient, avant de lui demander d’approuver les autorisations nécessaires. Même si cela n’est pas strictement nécessaire, cela peut vous aider à créer une expérience plus intuitive pour les utilisateurs de l’organisation. Pour connecter l’utilisateur, suivez notre [didacticiels sur le protocole plateforme Microsoft identity](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Demander les autorisations à un administrateur d’annuaire

Lorsque vous êtes prêt à demander les autorisations d’administrateur de votre organisation, vous pouvez rediriger l’utilisateur vers la plateforme Microsoft identity *point de terminaison de consentement administrateur*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paramètre | Condition | Description  |
| --- | --- | --- |
| `tenant` | Obligatoire | Le client d’annuaire auquel vous souhaitez demander l’autorisation. Peut être fourni au format GUID ou sous forme de nom convivial OU référencé de manière générique avec `common` comme indiqué dans l’exemple. |
| `client_id` | Obligatoire | Le **ID d’Application (client)** qui le [portail Azure-inscriptions](https://go.microsoft.com/fwlink/?linkid=2083908) expérience affecté à votre application. |
| `redirect_uri` | Obligatoire |URI de redirection où vous souhaitez que la réponse soit envoyée pour être gérée par votre application. Il doit correspondre exactement à l’un des URI de redirection que vous avez inscrits dans le portail d’inscription des applications. |
| `state` | Recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Utilisez l’état pour encoder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou la vue sur laquelle ou laquelle il était positionné. |

À ce stade, Azure AD nécessite qu’un administrateur client se connecte pour terminer la demande. L’administrateur est invité à approuver toutes les autorisations que vous avez demandées pour votre application dans le portail d’inscription.

#### <a name="successful-response"></a>Réponse réussie

Si l’administrateur approuve les autorisations pour votre application, la réponse correcte ressemble à ce qui suit :

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paramètre | Description  |
| --- | --- |
| `tenant` | Client d’annuaire ayant accordé à votre application les autorisations demandées au format GUID. |
| `state` | Une valeur incluse dans la requête qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. La valeur d’état est utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| `admin_consent` | Sera défini sur `True`. |

#### <a name="error-response"></a>Réponse d’erreur

Si l’administrateur n’approuve pas les autorisations pour votre application, la réponse d’échec ressemble à ce qui suit :

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paramètre | Description  |
| --- | --- |
| `error` | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| `error_description` | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur. |

Une fois que vous avez reçu une réponse correcte du point de terminaison de consentement de l’administrateur, votre application a acquis les autorisations qu’elle avait demandées. Vous pouvez alors demander un jeton pour la ressource souhaitée.

## <a name="using-permissions"></a>Utilisation des autorisations

Une fois que l’utilisateur accepte les autorisations pour votre application, cette dernière peut acquérir des jetons d’accès représentant l’autorisation de votre application à accéder, dans une certaine capacité, à une ressource. Un jeton d’accès peut être utilisé pour une ressource uniquement, mais l’encodage de ce jeton comporte les informations relatives à toutes les autorisations octroyées pour cette ressource à votre application. Pour acquérir un jeton d’accès, votre application peut effectuer une demande au Microsoft identity plateforme jeton point de terminaison, comme suit :

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Vous pouvez utiliser le jeton d’accès obtenu dans les requêtes HTTP transmises à la ressource. Il indique de façon fiable à la ressource que votre application dispose de l’autorisation appropriée pour effectuer une tâche spécifique. 

Pour plus d’informations sur le protocole OAuth 2.0 et comment obtenir des jetons d’accès, consultez le [référence de protocole de point de terminaison de plateforme d’identité Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Étendue /.default

Vous pouvez utiliser le `/.default` étendue pour aider à migrer vos applications à partir du point de terminaison v1.0 pour le point de terminaison Microsoft identity platform. Il s’agit d’une étendue intégrée pour chaque application qui fait référence à la liste statique des autorisations configurées sur l’inscription d’application. Une valeur `scope` `https://graph.microsoft.com/.default` est fonctionnellement identique aux points de terminaison v1.0 `resource=https://graph.microsoft.com`, autrement dit elle demande un jeton avec les étendues sur Microsoft Graph auxquelles l’application s’est inscrite dans le portail Azure.

L’étendue de /.default peut être utilisé dans n’importe quel flux OAuth 2.0, mais il est nécessaire dans le [flux On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) et [flux des informations d’identification client](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> Les clients ne peut pas combiner statique (`/.default`) et consentement dynamique dans une demande unique. Par conséquent, `scope=https://graph.microsoft.com/.default+mail.read` entraîne une erreur en raison de la combinaison des types d’étendues.

### <a name="default-and-consent"></a>/.default et consentement

L’étendue `/.default` déclenche le comportement de point de terminaison v1.0 également pour `prompt=consent`. Elle demande le consentement pour toutes les autorisations inscrites par l’application, quelle que soit la ressource. Si inclus dans le cadre de la demande, le `/.default` étendue retourne un jeton qui contient les étendues pour la ressource demandée.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quand l’utilisateur a déjà donné son consentement

Comme `/.default` est fonctionnellement identique au comportement du point de terminaison v1.0 axé sur `resource`, elle s’accompagne aussi du comportement de consentement du point de terminaison v1.0. Autrement dit, `/.default` déclenche uniquement une invite de consentement si aucune autorisation n’a été accordée entre le client et la ressource par l’utilisateur. Si un tel consentement existe, un jeton qui contient toutes les étendues accordées par l’utilisateur pour cette ressource est alors retourné. Toutefois, si aucune autorisation n’a été accordée, ou si le paramètre `prompt=consent` a été fourni, une invite de consentement s’affiche pour toutes les étendues inscrites par l’application cliente.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemple 1 : L’utilisateur, ou l’administrateur de locataire, a accordé des autorisations

L’utilisateur (ou un administrateur de locataire) a accordé au client les autorisations Microsoft Graph `mail.read` et `user.read`. Si le client envoie une demande pour `scope=https://graph.microsoft.com/.default`, aucune invite de consentement ne s’affiche, quel que soit le contenu des autorisations inscrites par les applications clientes pour Microsoft Graph. Un jeton contenant les étendues `mail.read` et `user.read` doit être retourné.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemple 2 : L’utilisateur n’a pas accordé d’autorisations entre le client et la ressource

Aucun consentement pour l’utilisateur n’existe entre le client et Microsoft Graph. Le client s’est inscrit aux autorisations `user.read` et `contacts.read` ainsi qu’à l’étendue Azure Key Vault `https://vault.azure.net/user_impersonation`. Quand le client demande un jeton pour `scope=https://graph.microsoft.com/.default`, l’utilisateur voit un écran de consentement pour les étendues `user.read`, `contacts.read` et Key Vault `user_impersonation`. Le jeton retourné ne contient que les étendues `user.read` et `contacts.read`.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exemple 3 : L’utilisateur a donné son consentement et le client demande des étendues supplémentaires

L’utilisateur a déjà donné son consentement à `mail.read` pour le client. Le client s’est inscrit à l’étendue `contacts.read` dans son inscription. Quand le client effectue une demande pour un jeton à l’aide de `scope=https://graph.microsoft.com/.default` et demande un consentement via `prompt=consent`, l’utilisateur voit un écran de consentement uniquement pour toutes les autorisations inscrites par l’application. `contacts.read` est présent dans l’écran de consentement, mais pas `mail.read`. Le jeton retourné concerne Microsoft Graph et contient `mail.read` et `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Utilisation de l’étendue /.default avec le client

Il existe un cas spécial d’étendue `/.default` quand un client demande sa propre étendue `/.default`. L’exemple suivant illustre ce cas de figure.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Cela génère un écran de consentement pour toutes les autorisations inscrites (si cela est applicable en fonction des descriptions ci-dessus du consentement et de `/.default`), puis retourne un jeton id_token, plutôt qu’un jeton d’accès.  Ce comportement existe pour certains clients hérités de déplacement à partir de la bibliothèque ADAL à la bibliothèque MSAL et ne doit pas être utilisé par les nouveaux clients ciblant le point de terminaison Microsoft identity platform.  

## <a name="troubleshooting-permissions-and-consent"></a>Résolution des problèmes d’autorisations et de consentement

Si vous, ou les utilisateurs de votre application, constatez des erreurs inattendues au cours du processus de consentement, consultez cet article pour connaître les étapes de dépannage : [Erreur inattendue lors du consentement à une application](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
