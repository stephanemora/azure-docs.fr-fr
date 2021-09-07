---
title: Authentification
description: Explique le fonctionnement de l’authentification
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 01b2dbaa8ed318f08fd68660078ae6fb9923221d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112969736"
---
# <a name="configure-authentication"></a>configurer l’authentification ;

Azure Remote Rendering utilise un mécanisme d’authentification identique à [Azure Spatial Anchors (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Les clients doivent définir *un* des éléments suivants pour appeler correctement les API REST :

* **AccountKey** peut être obtenu sous l’onglet « Clés » correspondant au compte Remote Rendering dans le portail Azure. Les clés de compte sont uniquement recommandées pour le développement/prototypage.
    ![Account ID](./media/azure-account-primary-key.png) (ID de compte)

* **AccountDomain** : peut être obtenu sous l’onglet « Vue d’ensemble » correspondant au compte de rendu à distance dans le portail Azure.
    ![Domaine du compte](./media/azure-account-domain.png)

* **AuthenticationToken** est un jeton Azure AD, qui peut être obtenu à l’aide de la [bibliothèque ADAL](../../active-directory/develop/msal-overview.md). Plusieurs flux différents sont disponibles pour accepter les informations d’identification de l’utilisateur et utiliser ces informations d’identification pour obtenir un jeton d’accès.

* **MRAccessToken** est un jeton MR, qui peut être obtenu à partir du service d’émission de jeton de sécurité (STS) Azure Mixed Reality. Récupéré à partir du point de terminaison `https://sts.<accountDomain>` à l’aide d’un appel REST semblable au suivant :

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
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

### <a name="azure-ad-user-authentication"></a>Authentification utilisateur Azure AD

L’authentification Azure AD est décrite dans la [documentation d’Azure Spatial Anchors](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication).

Suivez les étapes permettant de configurer l’authentification utilisateur Azure Active Directory dans le portail Azure.

1. Inscrivez votre application dans Azure Active Directory. Dans le cadre de l’inscription, vous devez déterminer si votre application doit être multilocataire. Vous devez également fournir les URL de redirection autorisées pour votre application dans le panneau Authentification.
:::image type="content" source="./media/azure-active-directory-app-setup.png" alt-text="Configuration de l’authentification":::

1. Sous l’onglet API autorisées, demandez des **autorisations déléguées** pour l’étendue **mixedreality.signin** sous **mixedreality**.
:::image type="content" source="./media/azure-active-directory-app-api-permissions.png" alt-text="API autorisées":::

1. Accordez le consentement administrateur sous l’onglet Sécurité -> Autorisations. :::image type="content" source="./media/azure-active-directory-grant-admin-consent.png" alt-text="Consentement administrateur":::

1. Ensuite, accédez à votre ressource Azure Remote Rendering. Dans le volet Contrôle d’accès, accordez les [rôles](#azure-role-based-access-control) souhaités pour vos applications et utilisateur, au nom desquels vous souhaitez utiliser des autorisations d’accès déléguées sur votre ressource Azure Remote Rendering.
:::image type="content" source="./media/azure-remote-rendering-add-role-assignment.png" alt-text="Ajout d’autorisations":::
:::image type="content" source="./media/azure-remote-rendering-role-assignments.png" alt-text="Affectations de rôle":::

Pour plus d’informations sur l’utilisation de l’authentification utilisateur Azure AD dans votre code d’application, consultez le [Tutoriel : Sécurisation d’Azure Remote Rendering et du stockage de modèles - Authentification Azure Active Directory](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication).

## <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure

Pour mieux contrôler le niveau d’accès accordé à votre service, utilisez les rôles suivants lorsque vous donnez l’accès en fonction du rôle :

* **Administrateur Remote Rendering** : Fournit à l’utilisateur des fonctionnalités de conversion, de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering.
* **Client Remote Rendering** : Fournit à l’utilisateur des fonctionnalités de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un compte](create-an-account.md)
* [Utiliser les API Azure Frontend pour l’authentification](frontend-apis.md)
* [Exemples de scripts PowerShell](../samples/powershell-example-scripts.md)
