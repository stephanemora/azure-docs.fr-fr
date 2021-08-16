---
title: Ajouter Google comme fournisseur d’identité pour B2B – Azure AD
description: Fédérer avec Google pour permettre à des utilisateurs invités de se connecter à vos applications Azure AD avec leur propre compte Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/08/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c21e03870a53858fe877410a7cd75fdc7e82a83b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963511"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B

En configurant la fédération avec Google, vous pouvez autoriser les utilisateurs invités à se connecter à vos applications et ressources partagées avec leur propre compte Gmail, sans avoir besoin de créer un compte Microsoft.

Une fois que vous avez ajouté Google aux options de connexion de votre application, dans la page **Connexion**, l’utilisateur doit simplement entrer l’adresse e-mail dont il se sert pour se connecter à Google, ou sélectionner **Options de connexion** et choisir **Se connecter avec Google**. Dans les deux cas, il est redirigé vers la page de connexion de Google pour s’authentifier.

![Options de connexion pour les utilisateurs Google](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> La fédération Google est conçue spécialement pour les utilisateurs Gmail. Pour fédérer avec les domaines G Suite, utilisez la [fédération de fournisseur d’identité (IdP) SAML/WS-Fed](direct-federation.md).

> [!IMPORTANT]
> **À partir du second semestre 2021**, Google [déprécie la prise en charge de la connexion aux vues web](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si vous utilisez la fédération Google pour les invitations B2B ou [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md), ou bien si vous utilisez l’inscription en libre-service avec Gmail, les utilisateurs de Google Gmail ne pourront pas se connecter si vos applications effectuent l’authentification des utilisateurs via une vue web incorporée. [Plus d’informations](#deprecation-of-web-view-sign-in-support)

## <a name="what-is-the-experience-for-the-google-user"></a>Quelle est l’expérience de l’utilisateur Google ?

Quand un utilisateur Google accepte votre invitation, son expérience varie selon qu’il est déjà connecté à Google :

- Les utilisateurs invités qui ne sont pas connectés à Google seront invités à le faire.
- Les utilisateurs invités déjà connectés à Google seront invités à choisir le compte qu’ils souhaitent utiliser. Il doit choisir le compte que vous avez utilisé pour l’inviter.

Les utilisateurs invités qui voient une erreur « header too long » (en-tête trop long) peuvent effacer leurs cookies ou ouvrir une fenêtre privée ou incognito, puis essayer de se reconnecter.

![Capture d’écran montrant la page de connexion Google.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Points de terminaison de connexion

Les utilisateurs invités Google peuvent désormais se connecter à vos applications multilocataires ou à vos applications principales Microsoft à l’aide d’un [point de terminaison commun](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (en d’autres termes, une URL d’application générale qui n’inclut pas le contexte de votre locataire). Durant le processus de connexion, l’utilisateur invité choisit **Options de connexion**, puis sélectionne **Sign in to an organization** (Se connecter à une organisation). L’utilisateur tape ensuite le nom de votre organisation et poursuit le processus de connexion à l’aide de ses informations d’identification Google.

Les utilisateurs invités Google peuvent également se servir des points de terminaison d’application qui incluent les informations de votre locataire, par exemple :

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Vous pouvez également fournir aux utilisateurs invités Google un lien direct vers une application ou une ressource en incluant les informations de votre locataire, par exemple `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`.

## <a name="deprecation-of-web-view-sign-in-support"></a>Dépréciation de la prise en charge de la connexion via vue web

À partir du second semestre 2021, Google [déprécie la prise en charge de la connexion via vue web](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Si vous utilisez la fédération Google pour B2B ou [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md), ou bien si vous utilisez [l’inscription en libre-service avec Gmail](identity-providers.md), si vos applications effectuent l’authentification des utilisateurs via une vue web incorporée, les utilisateurs de Google Gmail ne pourront pas s’authentifier.

Voici quelques scénarios connus ayant une incidence sur les utilisateurs de Gmail :
- Applications Windows qui utilisent le contrôle [WebView](/windows/communitytoolkit/controls/wpf-winforms/webview), [WebView2](/microsoft-edge/webview2/) ou l’ancien contrôle WebBrowser pour l’authentification. Ces applications doivent migrer vers l’utilisation du gestionnaire de comptes web (WAM).
- Applications Android utilisant l’élément d’interface utilisateur WebView 
- applications iOS utilisant UIWebView/WKWebview 
- Applications utilisant ADAL

Cette modification n’a aucune incidence sur les scénarios suivants :

- Applications Microsoft sur Windows
- Applications web
- Applications mobiles utilisant des vues web système pour l’authentification ([SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) sur iOS, [Onglets personnalisés](https://developer.chrome.com/docs/android/custom-tabs/overview/) sur Android).  
- Identités G Suite, par exemple la [fédération basée sur SAML](direct-federation.md) utilisée avec G Suite

Nous vérifions actuellement auprès de Google si cette modification affecte les éléments suivants :
- Applications Windows qui utilisent le gestionnaire de compte web (WAM) ou le répartiteur d’authentification web (WAB).  

Nous continuons à tester différentes plateformes et différents scénarios. Cet article sera mis à jour en conséquence.
### <a name="action-needed-for-embedded-web-views"></a>Action requise pour les vues web incorporées
Modifiez vos applications de façon à utiliser le navigateur système pour la connexion. Pour plus d’informations, consultez [Interface utilisateur incorporée ou Web System](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) dans la documentation de MSAL.NET. Tous les kits SDK MSAL utilisent la vue web système par défaut.
### <a name="what-to-expect"></a>À quoi s’attendre
Avant que Google n’applique ces modifications au second semestre 2021, Microsoft déploiera une solution de contournement pour les applications qui utilisent toujours des vues web incorporées afin que l’authentification ne soit pas bloquée.

Les applications qui sont migrées vers une vue web autorisée pour l’authentification ne sont pas affectées, et les utilisateurs sont autorisés à s’authentifier via Google comme d’habitude.

Si les applications ne sont pas migrées vers une vue Web autorisée pour l’authentification, les utilisateurs Gmail affectés verront l’écran suivant.

![Erreur de connexion Google si les applications ne sont pas migrées vers des navigateurs système](media/google-federation/google-sign-in-error-ewv.png)

Ce document sera mis à jour quand les dates et détails supplémentaires seront annoncés par Google.

### <a name="distinguishing-between-cefelectron-and-embedded-web-views"></a>Distinction entre CEF/Electron et les vues web incorporées
En plus de la [dépréciation de la prise en charge de la vue web incorporée et de la connexion à l’infrastructure](#deprecation-of-web-view-sign-in-support), Google [déprécie également l’authentification Gmail basée sur le CEF (Chromium Embedded Framework)](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Pour les applications basées sur CEF, telles que les applications Electron, Google désactivera l’authentification le 30 juin 2021. Les applications affectées ont reçu directement une notification de Google et ne sont pas abordées dans cette documentation.  Ce document concerne les vues web incorporées décrites ci-dessus que Google va restreindre ultérieurement en 2021.

### <a name="action-needed-for-embedded-frameworks"></a>Action requise pour les infrastructures incorporées
Suivez les [conseils de Google](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) pour déterminer si vos applications sont affectées.

## <a name="step-1-configure-a-google-developer-project"></a>Étape 1 : Configurer un projet de développeur Google
Commencez par créer un projet dans la console des développeurs Google pour obtenir un ID client et une clé secrète client que vous pourrez ajouter plus tard à Azure Active Directory (Azure AD). 
1. Accédez aux API Google à l’adresse https://console.developers.google.com et connectez-vous avec votre compte Google. Nous vous recommandons d’utiliser un compte Google d’équipe partagé.
2. Acceptez les conditions d’utilisation du service si vous y êtes invité.
3. Créez un projet : Dans le coin supérieur gauche de la page, sélectionnez la liste des projets, puis dans la page **Sélectionner un projet**, sélectionnez **Nouveau projet**.
4. Dans la page **Nouveau projet**, attribuez un nom au projet (par exemple **Azure AD B2B**), puis sélectionnez **Créer**: 
   
   ![Capture d’écran montrant une page Nouveau projet.](media/google-federation/google-new-project.png)

4. Dans la page **API et Services**, sélectionnez **Afficher** sous votre nouveau projet.

5. Sélectionnez **Accéder à l’aperçu des API** sur la carte des API. Sélectionnez **Écran d’autorisation OAuth**.

6. Sélectionnez **Externe**, puis sélectionnez **Créer**. 

7. Dans l’**écran de consentement OAuth**, entrez un **nom d’application** :

   ![Capture d’écran montrant l’écran de consentement OAuth de Google.](media/google-federation/google-oauth-consent-screen.png)

8. Accédez à la section **Domaines autorisés** et entrez **microsoftonline.com** :

   ![Capture d’écran montrant la section Domaines autorisés.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Sélectionnez **Enregistrer**.

10. Sélectionnez **Credentials (Informations d’identification)**. Dans le menu **Créer les informations d’identification**, sélectionnez **ID client OAuth** :

    ![Capture d’écran montrant le menu Créer les informations d’identification des API Google.](media/google-federation/google-api-credentials.png)

11. Sous **Type d’application**, sélectionnez **Application web**. Donnez à l’application un nom approprié, par exemple **Azure AD B2B**. Sous **URI de redirection autorisés**, entrez les URI suivants :
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(où `<tenant ID>` est votre ID de locataire)
   
    > [!NOTE]
    > Pour trouver votre ID de locataire, accédez au [portail Azure](https://portal.azure.com). Sous **Azure Active Directory**, sélectionnez **Propriétés** et copiez l’**ID de locataire**.

    ![Capture d’écran montrant la section URI de redirection autorisée.](media/google-federation/google-create-oauth-client-id.png)

12. Sélectionnez **Créer**. Copiez l’ID client et la clé secrète client. Vous les utiliserez lorsque vous ajouterez le fournisseur d’identité dans le portail Azure.

    ![Capture d’écran montrant l’ID client et la clé secrète client OAuth.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Étape 2 : Configurer la fédération de Google dans Azure AD 
Maintenant vous définirez l’ID client Google et la clé secrète client. Pour cela, vous pouvez utiliser le portail Azure ou PowerShell. Pensez à tester votre configuration de fédération de Google en invitant vous-même. Utilisez une adresse Gmail et essayez de donner suite à l’invitation avec votre compte Google invité. 

**Pour configurer la fédération de Google dans le portail Azure** 
1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Azure Active Directory** dans le volet de gauche. 
2. Sélectionnez **Identités externes**.
3. Sélectionnez **Tous les fournisseurs d’identité**, puis cliquez sur le bouton **Google**.
4. Entrez l’ID client et la clé secrète client obtenus précédemment. Sélectionnez **Enregistrer** : 

   ![Capture d’écran montrant la page d’ajout de fournisseur d’identité Google.](media/google-federation/google-identity-provider.png)

**Pour configurer la fédération de Google à l’aide de PowerShell**
1. Installez la dernière version d’Azure AD PowerShell pour le module Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Exécutez cette commande : `Connect-AzureAD`.
3. À l’invite de connexion, connectez-vous avec le compte d’administrateur général géré.  
4. Exécutez la commande suivante : 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Utilisez l’ID client et la clé secrète client à partir de l’application créée à l’« étape 1 : Configurer un projet de développeur Google ». Pour plus d’informations, consultez [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true). 
 
## <a name="how-do-i-remove-google-federation"></a>Comment supprimer la fédération de Google ?
Vous pouvez supprimer votre configuration de fédération de Google. Si vous le faites, les utilisateurs invités Google qui ont déjà utilisé leur invitation ne pourront plus se connecter. Toutefois, vous pouvez autoriser de nouveau l’accès à vos ressources en [réinitialisant l’état d’acceptation](reset-redemption-status.md).
 
**Pour supprimer la fédération de Google dans le portail Azure AD**
1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Azure Active Directory** dans le volet de gauche. 
2. Sélectionnez **Identités externes**.
3. Sélectionnez **Tous les fournisseurs d’identité**.
4. Dans la ligne **Google**, sélectionnez le bouton représentant des points de suspension ( **...** ), puis choisissez **Supprimer**. 
   
   ![Capture d’écran montrant le bouton de suppression du fournisseur d’identité sociale.](media/google-federation/google-social-identity-providers.png)

1. Sélectionnez **Oui** pour confirmer la suppression. 

**Pour supprimer la fédération de Google à l’aide de PowerShell** 
1. Installez la dernière version d’Azure AD PowerShell pour le module Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Exécutez `Connect-AzureAD`.  
4. Dans l’invite de connexion, connectez-vous avec le compte d’administrateur général géré.  
5. Entrez la commande suivante :

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Pour plus d’informations, consultez [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true).