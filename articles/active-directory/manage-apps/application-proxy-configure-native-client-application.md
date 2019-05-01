---
title: Publier des applications clientes natives - Azure AD | Microsoft Docs
description: Explique comment activer des applications clientes natives de manière à communiquer avec le connecteur de proxy d'application Azure AD pour fournir un accès à distance sécurisé à vos applications locales.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4614d8190436ad89faa200f83b1a71bde10a8acb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684937"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Comment activer les applications clientes natives interagir avec des applications proxy

Vous pouvez utiliser le Proxy d’Application Azure Active Directory (Azure AD) pour publier des applications web, mais il peut également être utilisé pour publier des applications clientes natives qui sont configurées avec la bibliothèque d’authentification Azure AD (ADAL). Les applications clientes natives diffèrent des applications web, car ils sont installés sur un appareil, tandis que les applications web sont accessibles via un navigateur.

Pour prendre en charge les applications clientes natives, le Proxy d’Application accepte des jetons émis par AD Azure qui sont envoyées dans l’en-tête. Le service de Proxy d’Application effectue l’authentification pour les utilisateurs. Cette solution n’utilise des jetons d’application pour l’authentification.

![Relation entre les utilisateurs finaux, Azure Active Directory et les applications publiées](./media/application-proxy-configure-native-client-application/richclientflow.png)

Pour publier des applications natives, utilisez la bibliothèque d’authentification Azure AD, qui prend en charge l’authentification et prend en charge de nombreux environnements client. Le proxy d'application est conforme au [scénario Application Native vers API Web](../develop/native-app.md).

Cet article vous guide dans quatre étapes pour publier une application native avec le proxy d’application et la bibliothèque Azure AD Authentication.

## <a name="step-1-publish-your-proxy-application"></a>Étape 1 : Publiez votre application proxy

Publiez votre application proxy comme vous le feriez pour toute autre application et affectez des utilisateurs pour accéder à votre application. Pour plus d'informations, consultez [Publier des applications avec le proxy d'application](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Étape 2 : Inscrire votre application native

Vous devez maintenant inscrire votre application dans Azure AD, comme suit :

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/). Le **tableau de bord** pour le **centre d’administration Azure Active Directory** s’affiche.
2. Dans la barre latérale, sélectionnez **Azure Active Directory**. Le **Azure Active Directory** page Vue d’ensemble s’affiche.
3. Dans la barre latérale de vue d’ensemble Azure AD, sélectionnez **inscriptions**. La liste de toutes les inscriptions d’application s’affiche.
4. Sélectionnez **Nouvelle inscription**. Le **inscrire une application** page s’affiche.

   ![Créer une nouvelle inscription d’application](./media/application-proxy-configure-native-client-application/create.png)
5. Dans le **nom** en-tête, spécifiez un nom d’affichage de l’utilisateur pour votre application.
6. Sous le **pris en charge les types de comptes** titre, sélectionnez un niveau d’accès à l’aide de ces instructions :
   - Pour cibler uniquement les comptes qui sont internes à votre organisation, sélectionnez **comptes dans ce répertoire d’organisation uniquement**.
   - Pour cibler uniquement entreprise ou clients enseignement, sélectionnez **comptes dans n’importe quel répertoire organisation**.
   - Pour cibler l’ensemble plus large des identités de Microsoft, sélectionnez **comptes dans n’importe quel annuaire d’organisation et les comptes Microsoft personnels**.
7. Dans le **URI de redirection** titre, sélectionnez **client Public (mobile et bureau)**, puis tapez l’URI de redirection pour votre application.
8. Sélectionnez et lisez le **politiques de la plateforme Microsoft**, puis sélectionnez **inscrire**. Une page de présentation de la nouvelle inscription d’application est créée et affichée.

Pour plus d’informations sur la création d’une nouvelle inscription d’application, consultez [intégration d’applications dans Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Étape 3 : Accorder l’accès à votre application de proxy

Maintenant que vous avez inscrit votre application native, vous pouvez lui donner accès à d’autres applications dans votre annuaire, dans ce cas à accéder à l’application de proxy. Pour activer l’application native à exposer à l’application de proxy :

1. Dans la barre latérale de la nouvelle page de l’inscription d’application, sélectionnez **autorisations d’API**. Le **autorisations d’API** page pour la nouvelle inscription d’application s’affiche.
2. Sélectionnez **Ajouter une autorisation**. Le **autorisations d’API demande** page s’affiche.
3. Sous le **sélectionner une API** , sélectionnez **mon organisation utilise des API**. Une liste s’affiche, contenant les applications dans votre annuaire qui exposent des API.
4. Tapez dans la zone de recherche ou faites défiler pour trouver l’application de proxy que vous avez publiée dans [étape 1 : Publiez votre application proxy](#step-1-publish-your-proxy-application), puis sélectionnez l’application de proxy.
5. Dans le **quel type d’autorisations votre application nécessite-t-elle ?** titre, sélectionnez le type d’autorisation. Si votre application native doit accéder à l’API d’application de proxy en tant qu’utilisateur connecté, choisissez **autorisations déléguées**. Si votre application native s’exécute en tant que service en arrière-plan ou démon sans utilisateur connecté, choisissez **autorisations d’Application**.
6. Dans le **sélectionner les autorisations** titre, sélectionnez l’autorisation souhaitée, puis **ajouter des autorisations**. Le **autorisations d’API** page maintenant votre application native montre le proxy d’application et autorisation de l’API que vous avez ajouté.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Étape 4 : Modifier la bibliothèque d’authentification Active Directory

Modifiez le code de l’application native dans le contexte de l’authentification de l’Active Directory Authentication Library (ADAL) de manière à inclure le texte suivant :

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Les informations requises dans l’exemple de code peuvent être trouvée dans le portail Azure AD, comme suit :

| Informations requises | Comment le trouver dans le portail Azure AD |
| --- | --- |
| \<ID de locataire > | **Azure Active Directory** > **propriétés** > **ID de répertoire** |
| \<Url externe du Proxy d’application > | **Applications d’entreprise** > *votre proxy d’application* > **proxy d’Application** > **Url externe** |
| \<ID d’application de l’application Native > | **Applications d’entreprise** > *votre application native* > **propriétés** > **ID d’Application** |
| \<URI de redirection de l’application Native > | **Azure Active Directory** > **inscriptions** > *votre application native* > **URI de redirection** |
| \<Url de l’API application proxy > | **Azure Active Directory** > **inscriptions** > *votre application native* > **autorisations d’API**  >  **API / nom d’autorisations** |

Après avoir modifié la bibliothèque ADAL avec ces paramètres, vos utilisateurs peuvent s’authentifier auprès des applications clientes natives même lorsqu’ils sont en dehors du réseau d’entreprise.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux de l’application native, consultez [des applications natives dans Azure Active Directory](../develop/native-app.md).

En savoir plus sur la configuration de [l’authentification unique aux applications dans Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
