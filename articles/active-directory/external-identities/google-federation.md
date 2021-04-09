---
title: Ajouter Google comme fournisseur d’identité pour B2B – Azure AD
description: Fédérer avec Google pour permettre à des utilisateurs invités de se connecter à vos applications Azure AD avec leur propre compte Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2e9acda136654a0dd654eded7b4c17e560fc0aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709699"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B

En configurant la fédération avec Google, vous pouvez autoriser les utilisateurs invités à se connecter à vos applications et ressources partagées avec leur propre compte Gmail, sans avoir besoin de créer un compte Microsoft.

Une fois que vous avez ajouté Google aux options de connexion de votre application, dans la page **Connexion**, l’utilisateur doit simplement entrer l’adresse e-mail dont il se sert pour se connecter à Google, ou sélectionner **Options de connexion** et choisir **Se connecter avec Google**. Dans les deux cas, il est redirigé vers la page de connexion de Google pour s’authentifier.

![Options de connexion pour les utilisateurs Google](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> La fédération Google est conçue spécialement pour les utilisateurs Gmail. Pour fédérer avec les domaines G Suite, utilisez la [fédération directe](direct-federation.md).

> [!IMPORTANT]
> **À compter du 4 janvier 2021**, Google [déconseille la prise en charge de la connexion WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Si vous utilisez la fédération Google ou l’inscription en libre-service avec Gmail, [testez la compatibilité de vos applications métier natives](google-federation.md#deprecation-of-webview-sign-in-support).

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

## <a name="deprecation-of-webview-sign-in-support"></a>Prise en charge de la connexion WebView déconseillée

À compter du 4 janvier 2021, Google [déconseille la prise en charge de la connexion WebView incorporée](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Si vous utilisez la fédération Google ou [l’inscription en libre-service avec Gmail](identity-providers.md), testez la compatibilité de vos applications métier natives. Si vos applications incluent du contenu WebView pour lequel une authentification est nécessaire, les utilisateurs de Google Gmail ne pourront pas s’authentifier. Voici quelques scénarios connus ayant une incidence sur les utilisateurs de Gmail :

- Applications Windows qui utilisent la connexion WebView incorporée ou WebAccountManager (WAM) sur des versions antérieures de Windows
- Autres applications natives développées par vos soins, qui utilisent une infrastructure de navigateur incorporée pour l’authentification

Cette modification n’a aucune incidence sur les scénarios suivants :

- Applications Windows qui utilisent la connexion WebView incorporée ou WebAccountManager (WAM) sur les dernières versions de Windows
- Applications Microsoft iOS
- Identités G Suite, par exemple la [fédération directe](direct-federation.md) basée sur SAML utilisée avec G Suite

Nous continuons à tester différentes plateformes et différents scénarios. Cet article sera mis à jour en conséquence.
### <a name="to-test-your-apps-for-compatibility"></a>Test de la compatibilité des applications

1. Suivez les [conseils de Google](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) pour déterminer si vos applications sont affectées.
2. À l’aide de Fiddler ou d’un autre outil de test, injectez un en-tête lors de la connexion et utilisez une identité Google externe pour tester la connexion :

   1. Ajoutez Google-Accounts-Check-OAuth-Login:true à vos en-têtes de requête HTTP lorsque les demandes sont envoyées à accounts.google.com.
   1. Essayez de vous connecter à l’application en entrant une adresse Gmail sur la page de connexion accounts.google.com.
   1. Si la connexion échoue et qu’une erreur du type « Ce navigateur ou cette application n’est peut-être pas sécurisé » apparaît, la connexion des identités Google externes sera bloquée.

3. Résolvez le problème en procédant de l’une des façons suivantes :

   - Si votre application Windows utilise la connexion WebView incorporée ou WebAccountManager (WAM) sur une version antérieure de Windows, passez à la dernière version de Windows.
   - Modifiez vos applications de façon à utiliser le navigateur système pour la connexion. Pour plus d’informations, consultez [Interface utilisateur incorporée ou Web System](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) dans la documentation de MSAL.NET.  


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
Vous pouvez supprimer votre configuration de fédération de Google. Si vous le faites, les utilisateurs invités Google qui ont déjà utilisé leur invitation ne pourront plus se connecter. Mais vous pouvez leur donner accès à vos ressources à nouveau en les supprimant du répertoire et en les réinvitant. 
 
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
