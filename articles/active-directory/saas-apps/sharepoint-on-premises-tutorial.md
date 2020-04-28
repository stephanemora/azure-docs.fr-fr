---
title: 'Tutoriel : Intégration d’Azure Active Directory à SharePoint (local) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SharePoint (local).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867108"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à SharePoint (local)

Dans ce tutoriel, vous allez apprendre à intégrer SharePoint (local) à Azure Active Directory (Azure AD). Quand vous intégrez SharePoint (local) à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SharePoint (local).
* Autoriser les utilisateurs à se connecter automatiquement à SharePoint (local) avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure Active Directory avec SharePoint (local), vous avez besoin des éléments suivants :

* Un abonnement Azure Active Directory. Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Une batterie de serveurs SharePoint 2013 ou version ultérieure.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure Active Directory dans un environnement de test. Les utilisateurs Azure Active Directory pourront accéder à votre site SharePoint local.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Créer les applications d’entreprise dans le portail Azure

Pour configurer l’intégration de SharePoint (local) avec Azure AD, vous devez l’ajouter à votre liste d’applications SaaS gérées à partir de la galerie.

Pour ajouter SharePoint (local) à partir de la galerie, effectuez les étapes suivantes :

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

 > [!NOTE]
 > Si l’élément n’est pas disponible, il peut également être ouvert par le biais du lien fixe **Tous les services** en haut du panneau de navigation de gauche. Dans la présentation suivante, le lien **Azure Active Directory** se trouve dans la section **Identité** ou peut être recherché à l’aide de la zone de texte de filtre.

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

4. Dans la zone de recherche, tapez **SharePoint (local)** , puis sélectionnez **SharePoint (local)** dans le volet de résultats.

    <kbd>![SharePoint (local) dans la liste des résultats](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Spécifiez un nom pour votre site SharePoint local, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

1. Dans la nouvelle application d’entreprise, cliquez sur Propriétés et vérifiez la valeur de l’option **Affectation de l’utilisateur obligatoire**.

   <kbd>![SharePoint (local) dans la liste des résultats](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

Dans notre scénario, cette valeur est définie sur **Non**.

## <a name="configure-and-test-azure-ad"></a>Configurer et tester Azure AD

Dans cette section, vous allez configurer l’authentification unique Azure AD avec SharePoint (local).
Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur SharePoint (local) associé.

Pour configurer et tester l’authentification unique Azure Active Directory avec SharePoint (local), suivez les modules ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Configurer SharePoint (local)](#configure-sharepoint-on-premises)** pour configurer les paramètres de l’authentification unique côté application
1. **[Créer un utilisateur de test Azure AD dans le portail Azure](#create-an-azure-ad-test-user-in-the-azure-portal)** pour créer un utilisateur dans Azure AD pour l’authentification unique
1. **[Créer un groupe de sécurité AD dans le portail Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** pour créer un groupe de sécurité dans Azure AD pour l’authentification unique
1. **[Accorder à un compte Azure Active Directory des autorisations d’accès à SharePoint (local)](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** pour accorder des autorisations à un utilisateur Azure AD
1. **[Accorder à un groupe Azure AD des autorisations d’accès à SharePoint (local)](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** pour accorder des autorisations à un groupe Azure AD
1. **[Accorder à un compte invité l’accès à SharePoint (local) dans le portail Azure](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** pour accorder des autorisations à un compte invité dans Azure AD pour SharePoint (local)
1. **[Configuration du fournisseur d’identité approuvé pour plusieurs applications web](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** pour savoir comment utiliser le même fournisseur d’identité approuvé pour plusieurs applications web

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec SharePoint (local), procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez le répertoire Azure AD, cliquez sur **Applications d’entreprise**, cliquez sur le **nom de l’application d’entreprise créée antérieurement**, puis cliquez sur **Authentification unique**.

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur le mode **SAML** pour activer l’authentification unique.
 
3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL de SharePoint (local)](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `urn:<sharepointFarmName>:<federationName>`

    1. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<YourSharePointSiteURL>/_trust/`

    1. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YourSharePointSiteURL>/`.
    1. Cliquez sur Enregistrer.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. Dans la section **Configurer SharePoint (local)** , copiez la ou les URL appropriées en fonction de vos besoins.
    
    1. **URL de connexion**
    
        Copiez l’URL de connexion. Remplacez **/saml2** à la fin par **/wsfed**. Elle doit se présenter ainsi : **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** . (L’URL présentée ici n’est pas exacte.)

    1. **Identificateur Azure AD**
    1. **URL de déconnexion**
    > [!NOTE]
    > Cette URL ne peut pas être utilisée telle quelle dans SharePoint : vous devez remplacer **/saml2** par **/wsfed**. L’application SharePoint (local) utilise un jeton SAML 1.1. Azure Active Directory attend donc une demande WS Fed en provenance de SharePoint Server, et après l’authentification il émet le jeton SAML 1.1.

### <a name="configure-sharepoint-on-premises"></a>Configurer SharePoint (local)

1. **Créer un fournisseur d’identité approuvé dans SharePoint Server 2016**

    Connectez-vous au serveur SharePoint, puis ouvrez SharePoint Management Shell. Spécifiez les valeurs suivantes :
    1. **$realm** : valeur d’identificateur de la section Domaine et URL pour SharePoint (local) dans le portail Azure
    1. **$wsfedurl** : URL du service d’authentification unique
   1. **$filepath** : chemin dans lequel le fichier de certificat a été téléchargé (copié à partir du portail Azure)

    Exécutez les commandes suivantes pour configurer un nouveau fournisseur d’identité approuvé.

    > [!TIP]
    > Si vous ne connaissez pas PowerShell ou que vous souhaitez en savoir plus sur son fonctionnement, consultez [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **Activer le fournisseur d’identité approuvé pour votre application**

    a. Dans l’Administration centrale, accédez à **Gérer les applications web** et sélectionnez l’application web que vous souhaitez sécuriser avec Azure AD.

    b. Dans le ruban, cliquez sur **Fournisseurs d’authentification** et choisissez la zone que vous souhaitez utiliser.

    c. Sélectionnez **Fournisseur d’identité approuvé** et sélectionnez le fournisseur d’identité que vous venez d’inscrire, nommé *AzureAD*.

    d. Cliquez sur **OK**.

    ![Configuration de votre fournisseur d’authentification](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Créer un utilisateur de test Azure AD dans le portail Azure

L’objectif de cette section est de créer un utilisateur de test dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**. Dans le volet **Gérer**, sélectionnez **Utilisateurs**.

2. Sélectionnez ensuite **Tous les utilisateurs**, puis **Nouvel utilisateur** en haut de l’écran.

3. Sélectionnez l’option **Créer un utilisateur** puis, dans les propriétés de l’utilisateur, effectuez les étapes suivantes.  
   Vous pourrez peut-être créer des utilisateurs dans votre annuaire Azure AD à l’aide de votre suffixe de locataire ou de n’importe quel domaine vérifié. 

    a. Dans le champ **Nom**, entrez le nom d’utilisateur. Nous utilisons ici **TestUser**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `TestUser@yourcompanydomain.extension`  
    Par exemple : TestUser@contoso.com

    ![Boîte de dialogue Utilisateur](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

    e. Vous pouvez maintenant partager le site avec TestUser@contoso.com et autoriser cet utilisateur à y accéder.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Créer un groupe de sécurité Azure Active Directory dans le portail Azure

1. Cliquez sur **Azure Active Directory > Groupes**.

2. Cliquez sur **Nouveau groupe**.

3. Complétez les champs **Type de groupe**, **Nom du groupe**, **Description du groupe**, **Type d’appartenance**. Cliquez sur la flèche pour sélectionner des membres, puis recherchez le membre que vous souhaitez ajouter au groupe ou cliquez dessus. Cliquez sur **Sélectionner** pour ajouter les membres sélectionnés, puis cliquez sur **Créer**.

![Créer un groupe de sécurité Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Accorder à un compte Azure Active Directory des autorisations d’accès à SharePoint (local)

Pour accorder à l’utilisateur Azure Active Directory l’accès à SharePoint (local), vous devez partager la collection de sites ou ajouter l’utilisateur Azure Active Directory à l’un des groupes de la collection de sites. Les utilisateurs peuvent désormais se connecter à SharePoint 201x avec des identités d’Azure AD. Cependant, l’expérience utilisateur peut encore être améliorée. Par exemple, la recherche d’un utilisateur présente plusieurs résultats de recherche dans le sélecteur de personnes. Il existe un résultat de recherche pour chacun des types de revendications créés dans le mappage de revendications. Pour choisir un utilisateur à l’aide du sélecteur de personnes, vous devez taper son nom d’utilisateur exactement et choisir le résultat de revendication **nom**.

![Résultats de recherche de revendications](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Il n’existe aucune validation des valeurs que vous recherchez, ce qui peut entraîner des fautes d’orthographe ou faire en sorte que des utilisateurs choisissent accidentellement le type de revendication incorrect. Cela risque d’empêcher les utilisateurs d’accéder aux ressources.

**Pour corriger le sélecteur de personnes** dans ce scénario, il existe une solution open source appelée [AzureCP](https://yvand.github.io/AzureCP/) qui offre un fournisseur de revendications personnalisé pour SharePoint 2013, 2016 et 2019. Elle utilise l’API Microsoft Graph pour résoudre les valeurs entrées par les utilisateurs et effectuer la validation. Pour en savoir plus, consultez [cette page](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Si vous n’utilisez pas AzureCP, vous pouvez ajouter des groupes en ajoutant l’ID du groupe Azure AD. Cependant, cette méthode n’est pas conviviale et n’est pas fiable. Cela se présente ainsi :  
  >   
  >![Ajouter un groupe Azure AD à un groupe SharePoint](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Accorder à un groupe Azure AD des autorisations d’accès à SharePoint (local)

Pour affecter des groupes de sécurité Azure Active Directory à SharePoint (local), il est nécessaire d’utiliser un fournisseur de revendications personnalisé pour SharePoint Server. Dans notre exemple, nous avons utilisé AzureCP.

 > [!NOTE]
 > Veuillez noter que le fournisseur de revendications Azure n’est pas un produit Microsoft ou pris en charge par le Support technique Microsoft. Téléchargez, installez et configurez le fournisseur de revendications Azure sur la batterie de serveurs SharePoint (local) en procédant de la manière décrite dans https://yvand.github.io/AzureCP/. 

1. Configurez le fournisseur de revendications Azure sur la batterie de serveurs SharePoint (local) ou une autre solution de fournisseur de revendications personnalisé. Les étapes de configuration d’AzureCP sont disponibles à l’adresse https://yvand.github.io/AzureCP/Register-App-In-AAD.html.

1. Dans le portail Azure, ouvrez le répertoire Azure Active Directory. Cliquez sur **Applications d’entreprise**, cliquez sur le **nom de l’application d’entreprise créée antérieurement**, puis cliquez sur **Authentification unique**.

1. Dans la page **Configurer l’authentification unique avec SAML**, modifiez la section **Attributs utilisateur et revendications**.

1. Cliquez sur **Ajouter une revendication de groupe**.

1. Sélectionnez les groupes associés à l’utilisateur, qui doivent être retournés dans la revendication. Dans le cas présent, sélectionnez **Tous les groupes** puis, dans la section Attribut source, sélectionnez **ID de groupe**. Cliquez ensuite sur **Enregistrer**.

Pour accorder au groupe de sécurité Azure Active Directory l’accès à SharePoint (local), vous devez partager la collection de sites ou ajouter le groupe de sécurité Azure Active Directory à l’un des groupes de la collection de sites.

1. Accédez à la collection de sites SharePoint. Sous Paramètres du site pour la collection de sites, cliquez sur « Personnes et groupes ». Sélectionnez le groupe SharePoint. Cliquez sur Nouveau, puis sur « Ajouter des utilisateurs à ce groupe ». Commencez à taper le nom de votre groupe. Le sélecteur de personnes affiche alors le groupe de sécurité Azure Active Directory.

    ![Ajouter un groupe Azure AD à un groupe SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Accorder à un compte invité l’accès à SharePoint (local) dans le portail Azure

Il est désormais possible d’accorder à un compte invité l’accès à votre site SharePoint de manière cohérente. L’UPN est parfois modifié. L’utilisateur jdoe@outlook.com sera représenté sous la forme `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Vous pouvez partager votre site avec des utilisateurs externes de manière fluide. Pour cela, vous devrez apporter quelques modifications à la section **Attributs utilisateur et revendications** du portail Azure.

1. Dans le portail Azure, ouvrez le répertoire Azure Active Directory. Cliquez sur **Applications d’entreprise**, cliquez sur le **nom de l’application d’entreprise créée antérieurement**, puis cliquez sur **Authentification unique**.

1. Dans la page **Configurer l’authentification unique avec SAML**, modifiez la section **Attributs utilisateur et revendications**.

1. Dans la zone **Revendication requise**, cliquez sur **Identificateur unique de l’utilisateur (ID de nom)** .

1. Remplacez la propriété **Attribut source** en spécifiant la valeur **user.localuserprincipalname**, puis cliquez sur **Enregistrer**.

    ![Attributs utilisateur et revendications - présentation initiale](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. À l’aide du ruban, accédez de nouveau à **Authentification SAML**. À présent la section **Attributs utilisateur et revendications** doit se présenter comme suit : 

    ![Attributs utilisateur et revendications - présentation finale](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Avec cette configuration, le nom et le prénom ne sont pas obligatoires.

1. Dans le portail Azure, dans le volet de gauche, sélectionnez **Azure Active Directory**, puis sélectionnez **Utilisateurs**.

1. Cliquez sur **Nouvel utilisateur invité**.

1. Sélectionnez l’option **Inviter un utilisateur**, spécifiez les propriétés de l’utilisateur, puis cliquez sur **Inviter**.

1. Vous pouvez maintenant partager le site avec MyGuestAccount@outlook.com et autoriser cet utilisateur à y accéder.

    ![Partage d’un site avec un compte invité](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Configuration du fournisseur d’identité approuvé pour plusieurs applications web

La configuration peut s’appliquer à une application web. Toutefois, si vous prévoyez d’utiliser le même fournisseur d’identité approuvé pour plusieurs applications web, une configuration supplémentaire sera nécessaire. Par exemple, supposons que nous avons étendu une application web pour utiliser l’URL `https://sales.contoso.com`, et que nous voulons maintenant aussi authentifier les utilisateurs auprès de `https://marketing.contoso.com`. Pour ce faire, nous devons mettre à jour le fournisseur d’identité de manière à honorer le paramètre WReply, et mettre à jour l’inscription de l’application dans Azure AD pour ajouter une URL de réponse.

1. Dans le portail Azure, ouvrez le répertoire Azure Active Directory. Cliquez sur **Applications d’entreprise**, cliquez sur le **nom de l’application d’entreprise créée antérieurement**, puis cliquez sur **Authentification unique**.

2. Dans la page **Configurer l’authentification unique avec SAML**, modifiez la section **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. Sous **URL de réponse (URL Assertion Consumer Service)** ajoutez l’URL pour les applications web supplémentaires, puis cliquez sur **Enregistrer**.

    ![Modifier la configuration SAML de base](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Sur le serveur SharePoint, ouvrez **SharePoint 201x Management Shell**, puis exécutez les commandes suivantes, en utilisant le nom de l’émetteur de jeton d’identité approuvé que vous avez utilisé précédemment.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. Sur le site Administration centrale, accédez à l’application web et activez le fournisseur d’identité approuvé existant.

Dans le cadre d’un autre scénario, vous souhaiterez peut-être accorder aux utilisateurs internes l’accès à votre site SharePoint (local). Dans ce cas, vous devrez déployer Microsoft Azure Active Directory Connect pour synchroniser vos utilisateurs locaux avec Azure Active Directory. Cette configuration fera l’objet d’un autre article. 

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Présentation de l’identité hybride avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
