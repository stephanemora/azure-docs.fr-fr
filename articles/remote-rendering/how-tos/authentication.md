---
title: Authentification
description: Explique le fonctionnement de l’authentification
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 8f3b144a7790c3122d59d27183b3037998ddadd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565859"
---
# <a name="configure-authentication"></a>configurer l’authentification ;

Azure Remote Rendering utilise un mécanisme d’authentification identique à [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Les clients doivent définir *un* des éléments suivants pour appeler correctement les API REST :

* **AccountKey** peut être obtenu sous l’onglet « Clés » correspondant au compte Remote Rendering dans le portail Azure. Les clés de compte sont uniquement recommandées pour le développement/prototypage.
    ![Account ID](./media/azure-account-primary-key.png) (ID de compte)

* **AuthenticationToken** est un jeton Azure AD, qui peut être obtenu à l’aide de la [bibliothèque ADAL](https://docs.microsoft.com/azure/active-directory/develop/msal-overview). Plusieurs flux différents sont disponibles pour accepter les informations d’identification de l’utilisateur et utiliser ces informations d’identification pour obtenir un jeton d’accès.

* **MRAccessToken** est un jeton MR, qui peut être obtenu à partir du service d’émission de jeton de sécurité (STS) Azure Mixed Reality. Récupéré à partir du point de terminaison `https://sts.mixedreality.azure.com` à l’aide d’un appel REST semblable à l’appel ci-dessous :

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Où l’en-tête Authorization est mis en forme comme suit : `Bearer <Azure_AD_token>` ou `Bearer <accoundId>:<accountKey>`. La première est préférable pour des raisons de sécurité. Le jeton retourné par cet appel REST est le jeton d’accès MR.

## <a name="authentication-for-deployed-applications"></a>Authentification des applications déployées

Les clés de compte sont recommandées pour un prototypage rapide, pendant le développement uniquement. Il est recommandé de ne pas envoyer votre application en production à l’aide d’une clé de compte incorporée. Il est recommandé d’utiliser une approche d’authentification Azure AD basée sur l’utilisateur ou sur le service.

 L’authentification Azure AD est décrite dans la section [Authentification utilisateur Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) du service [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/).

 Pour plus d’informations, consultez le [Tutoriel : Sécurisation d’Azure Remote Rendering et du stockage de modèles - Authentification Azure Active Directory](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Pour mieux contrôler le niveau d’accès accordé à votre service, utilisez les rôles suivants lorsque vous donnez l’accès en fonction du rôle :

* **Administrateur Remote Rendering** : Fournit à l’utilisateur des fonctionnalités de conversion, de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering.
* **Client Remote Rendering** : Fournit à l’utilisateur des fonctionnalités de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un compte](create-an-account.md)
* [Utiliser les API Azure Frontend pour l’authentification](frontend-apis.md)
* [Exemples de scripts PowerShell](../samples/powershell-example-scripts.md)
