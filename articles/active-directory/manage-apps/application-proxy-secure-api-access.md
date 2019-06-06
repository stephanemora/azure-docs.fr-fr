---
title: Accès API avec le Proxy d’Application Azure AD sur site
description: Proxy d’Application Azure Active Directory permet d’accéder en toute sécurité les API des applications natives et la logique métier que vous hébergez sur site ou sur des machines virtuelles cloud.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 47f6678f8d18d734176d964f18a6febecea957ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481413"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Sécuriser l’accès aux API locales avec le Proxy d’Application Azure AD

Vous pouvez avoir une logique métier API qui s’exécutent sur site ou hébergés sur des machines virtuelles dans le cloud. Vos applications Android, iOS, Mac ou Windows natives doivent interagir avec les points de terminaison d’API à utiliser des données ou fournir une interaction utilisateur. Proxy d’Application Azure AD et le [bibliothèques Azure Active Directory Authentication (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) permettent à vos applications natives accèdent en toute sécurité vos API en local. Azure Active Directory Application Proxy est une solution plus rapide et plus sûre que l’ouverture des ports de pare-feu et de contrôle de l’authentification et autorisation au niveau de la couche application. 

Cet article vous guide dans la configuration d’une solution de Proxy d’Application Azure AD pour l’hébergement d’un service API web qui peuvent accéder à des applications natives. 

## <a name="overview"></a>Présentation

Le diagramme suivant illustre une méthode traditionnelle pour publier des API locales. Cette approche requiert l’ouverture des ports entrants 80 et 443.

![Accès à l’API classique](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Le diagramme suivant montre comment vous pouvez utiliser le Proxy d’Application Azure AD à publier en toute sécurité les API sans ouvrir des ports entrants :

![Accès API de Proxy d’Application AD Azure](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Le Proxy d’Application Azure AD constituent le cœur de la solution, fonctionne comme un point de terminaison public pour l’accès aux API et en fournissant l’authentification et l’autorisation. Vous pouvez également accéder à vos API à partir d’un large éventail de plateformes à l’aide de la [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) bibliothèques. 

Étant donné que l’autorisation et l’authentification du Proxy d’Application Azure AD s’appuient sur Azure AD, vous pouvez utiliser l’accès conditionnel Azure AD pour s’assurer uniquement les appareils de confiance peuvent accéder aux API publiées via le Proxy d’Application. Utiliser Azure AD Join ou joint à Azure AD hybride pour les ordinateurs de bureau et gérés par Intune pour les appareils. Vous pouvez également tirer parti des fonctionnalités d’Azure Active Directory Premium telles que l’authentification multifacteur Azure et la sécurité reposant sur l’apprentissage machine de [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Conditions préalables

Pour suivre cette procédure pas à pas, vous devez :

- Accès administrateur à un annuaire Azure, avec un compte qui peut créer et inscrire des applications
- L’exemple d’API web et les applications clientes natives à partir de [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publier l’API via le Proxy d’Application

Pour publier une API en dehors de votre intranet via le Proxy d’Application, vous suivez le même modèle que la publication des applications web. Pour plus d’informations, consultez [Tutoriel : Ajouter une application en local pour l’accès à distance via le Proxy d’Application dans Azure Active Directory](application-proxy-add-on-premises-application.md).

Pour publier l’API de web SecretAPI via le Proxy d’Application :

1. Générer et publier l’exemple de projet SecretAPI comme une application web ASP.NET sur votre ordinateur local ou un intranet. Assurez-vous que vous pouvez accéder à l’application web localement. 
   
1. Dans le [Azure portal](https://portal.azure.com), sélectionnez **Azure Active Directory** dans le volet de navigation gauche. Ensuite, dans le **vue d’ensemble** page, sélectionnez **applications d’entreprise**.
   
1. En haut de la **applications d’entreprise - toutes les applications** page, sélectionnez **nouvelle application**.
   
1. Sur le **ajouter une application** page sous **ajouter votre propre application**, sélectionnez **On-premises application**. 
   
1. Si vous n’avez pas un connecteur de Proxy d’Application installé, vous allez être invité à l’installer. Sélectionnez **télécharger un connecteur de Proxy d’Application** pour télécharger et installer le connecteur. 
   
1. Une fois que vous avez installé le connecteur de Proxy d’Application, sur le **ajouter votre propre application en local** page :
   
   1. Entrez *SecretAPI* regard **nom**.
      
   1. Entrez l’URL que vous utilisez pour accéder à côté de l’API à partir de votre intranet **Url interne**. 
      
   1. Assurez-vous que **pré-authentification** a la valeur **Azure Active Directory**. 
      
   1. Sélectionnez **ajouter** en haut de la page, puis attendez que l’application doit être créé.
   
   ![Ajouter l’application API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Sur le **applications d’entreprise - toutes les applications** page, sélectionnez le **SecretAPI** application. 
   
1. Sur le **SecretAPI - vue d’ensemble** page, sélectionnez **propriétés** dans le volet de navigation gauche.
   
1. Vous ne souhaitez pas API soient disponibles pour les utilisateurs finaux dans le **MyApps** du panneau, afin de définir **Visible par les utilisateurs** à **non** en bas de la **propriétés**page, puis sélectionnez **enregistrer**.
   
   ![Non visible pour les utilisateurs](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Vous avez publié votre API web via le Proxy d’Application Azure AD. Maintenant, ajoutez les utilisateurs qui peuvent accéder à l’application. 

1. Sur le **SecretAPI - vue d’ensemble** page, sélectionnez **utilisateurs et groupes** dans le volet de navigation gauche.
   
1. Sur le **utilisateurs et groupes** page, sélectionnez **ajouter un utilisateur**.  
   
1. Sur le **ajouter une attribution** page, sélectionnez **utilisateurs et groupes**. 
   
1. Sur le **utilisateurs et groupes** page, recherchez et sélectionnez les utilisateurs pouvant accéder à l’application, y compris au moins vous-même. Après avoir sélectionné tous les utilisateurs, sélectionnez **sélectionnez**. 
   
   ![Sélectionner et affecter l’utilisateur](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Sur le **ajouter une attribution** page, sélectionnez **affecter**. 

> [!NOTE]
> Les API qui utilisent l’authentification intégrée de Windows peuvent nécessiter [des étapes supplémentaires](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Inscrire l’application native et accorder l’accès à l’API

Applications natives sont des programmes développés pour utiliser sur une plateforme spécifique ou un périphérique. Avant que votre application native peut se connecter et accéder à une API, vous devez l’inscrire dans Azure AD. Les étapes suivantes montrent comment inscrire une application native et lui donner accès à l’API que vous avez publié via le Proxy d’Application web.

Pour inscrire l’application native AppProxyNativeAppSample :

1. Sur Azure Active Directory **vue d’ensemble** page, sélectionnez **inscriptions**et en haut de la **inscriptions** volet, sélectionnez **nouvelle inscription** .
   
1. Sur le **inscrire une application** page :
   
   1. Sous **nom**, entrez *AppProxyNativeAppSample*. 
      
   1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**. 
      
   1. Sous **l’URL de redirection**, liste déroulante et sélectionnez **client Public (mobile et bureau)** , puis entrez *https :\//appproxynativeapp*. 
      
   1. Sélectionnez **inscrire**et attendez que l’application être correctement inscrit. 
      
      ![Nouvelle inscription d’application](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Vous avez maintenant inscrit l’application AppProxyNativeAppSample dans Azure Active Directory. Pour donner à votre application native l’accès à l’API de SecretAPI web :

1. Sur Azure Active Directory **vue d’ensemble** > **inscriptions** page, sélectionnez le **AppProxyNativeAppSample** application. 
   
1. Sur le **AppProxyNativeAppSample** page, sélectionnez **autorisations d’API** dans le volet de navigation gauche. 
   
1. Sur le **autorisations d’API** page, sélectionnez **ajouter une autorisation**.
   
1. Le premier **autorisations d’API demande** page, sélectionnez le **mon organisation utilise des API** sous l’onglet, puis recherchez et sélectionnez **SecretAPI**. 
   
1. Sur la prochaine **autorisations d’API demande** , sélectionnez la case à cocher à côté **user_impersonation**, puis sélectionnez **ajouter des autorisations**. 
   
    ![Sélectionnez une API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Sur le **autorisations d’API** page, vous pouvez sélectionner **accorder le consentement de l’administrateur de Contoso** pour empêcher les autres utilisateurs de devoir individuellement donner son consentement à l’application. 

## <a name="configure-the-native-app-code"></a>Configurer le code de l’application native

La dernière étape consiste à configurer l’application native. L’extrait de code suivant à partir de la *Form1.cs* fichier dans l’exemple d’application NativeClient provoque la bibliothèque ADAL acquérir le jeton de demande de l’appel d’API et l’attacher en tant que du porteur à l’en-tête de l’application. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Pour configurer l’application native pour vous connecter à Azure Active Directory et appeler le Proxy d’application API, mettre à jour les valeurs d’espace réservé dans le *App.config* fichier de l’exemple d’application NativeClient avec des valeurs à partir d’Azure AD : 

- Coller le **ID de répertoire (locataire)** dans le `<add key="ida:Tenant" value="" />` champ. Vous pouvez trouver et copier cette valeur (GUID) à partir de la **vue d’ensemble** page d’un de vos applications. 
  
- Collez le AppProxyNativeAppSample **ID d’Application (client)** dans le `<add key="ida:ClientId" value="" />` champ. Vous pouvez trouver et copiez cette valeur (GUID) à partir de la AppProxyNativeAppSample **vue d’ensemble** page.
  
- Collez le AppProxyNativeAppSample **URI de redirection** dans le `<add key="ida:RedirectUri" value="" />` champ. Vous pouvez trouver et copiez cette valeur (URI) à partir de la AppProxyNativeAppSample **authentification** page. 
  
- Collez le SecretAPI **URI ID d’Application** dans le `<add key="todo:TodoListResourceId" value="" />` champ. Vous pouvez trouver et copiez cette valeur (URI) à partir de la SecretAPI **exposer une API** page.
  
- Collez le SecretAPI **URL de la Page d’accueil** dans le `<add key="todo:TodoListBaseAddress" value="" />` champ. Vous pouvez trouver et copiez cette valeur (URL) à partir de la SecretAPI **Branding** page.

Après avoir configuré les paramètres, générez et exécutez l’application native. Lorsque vous sélectionnez le **Sign In** bouton, l’application vous permet de se connecter, puis affiche un écran de confirmation pour confirmer qu’il a été connecté à la SecretAPI.

![Succès](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Ajouter une application en local pour l’accès à distance via le Proxy d’Application dans Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Démarrage rapide : Configurer une application cliente pour accéder aux API web](../develop/quickstart-configure-app-access-web-apis.md)
- [Comment activer les applications clientes natives interagir avec des applications proxy](application-proxy-configure-native-client-application.md)
