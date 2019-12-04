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
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 7feb62bb3e38452a441c505107569457d7c90a3f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233445"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutoriel : Intégration d’Azure Active Directory à SharePoint (local)

Dans ce tutoriel, vous allez apprendre à intégrer SharePoint (local) avec Azure Active Directory (Azure AD).
L’intégration de SharePoint (local) avec Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à SharePoint (local).
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SharePoint (local) (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SharePoint (local), vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/)
* Abonnement SharePoint (local) pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SharePoint (local) prend en charge l’authentification unique initiée par **SP**

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Ajout de SharePoint (local) à partir de la galerie

Pour configurer l’intégration de SharePoint (local) avec Azure AD, vous devez l’ajouter à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter SharePoint (local) à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

    > [!NOTE]   
    > Si l’élément n’est pas disponible, il peut également être ouvert par le biais du lien fixe **Tous les services** en haut du panneau de navigation de gauche. Dans la présentation suivante, le lien **Azure Active Directory** se trouve dans la section **Identité** ou peut être recherché à l’aide de la zone de texte de filtre.

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **SharePoint (local)** , sélectionnez **SharePoint (local)** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SharePoint (local) dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SharePoint (local) grâce à un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur SharePoint (local) associé.

Pour configurer et tester l’authentification unique Azure AD avec SharePoint (local), vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique SharePoint (local)](#configure-sharepoint-on-premises-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Créer un groupe de sécurité Azure Active Directory dans le portail Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** pour activer un nouveau groupe de sécurité dans Azure Active Directory pour l’authentification unique.
5. **[Accorder l’accès à un groupe de sécurité local SharePoint](#grant-access-to-sharepoint-on-premises-security-group)**  afin d’accorder l’accès pour un groupe particulier à Azure AD.
6. **[Affecter le groupe de sécurité Azure Active Directory dans le portail Azure](#assign-the-azure-ad-security-group-in-the-azure-portal)** pour affecter le groupe particulier à Azure Active Directory pour l’authentification.
7. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec SharePoint (local), procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **SharePoint (local)** , sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL de SharePoint (local)](common/sp-identifier-reply.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YourSharePointServerURL>/_trust/default.aspx`.

    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `urn:sharepoint:federation`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support client SharePoint (local)](https://support.office.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

    > [!Note]
    > Notez le chemin d’accès dans lequel vous avez téléchargé le fichier de certificat. En effet, vous devrez l’utiliser ultérieurement dans le script PowerShell pour la configuration.

6. Dans la section **Configurer SharePoint (local)** , copiez la ou les URL appropriées en fonction de vos besoins. Sous **URL du service d’authentification unique**, indiquez une valeur respectant le format suivant : `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ est l’ID de locataire de l’abonnement Azure AD.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    > [!NOTE]
    > L’application SharePoint (local) utilise un jeton SAML 1.1. Azure Active Directory attend donc une demande WS Fed en provenance de SharePoint Server, et après l’authentification il émet le jeton SAML 1.1.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Configurer l’authentification unique SharePoint (local)

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise SharePoint (local) en tant qu’administrateur.

2. **Configurer un nouveau fournisseur d’identité approuvé dans SharePoint Server 2016**

    Connectez-vous au serveur SharePoint Server 2016, puis ouvrez SharePoint 2016 Management Shell. Renseignez les valeurs de $realm (valeur de l’identificateur figurant dans la section SharePoint on-premises Domain and URLs (Domaine et URL SharePoint en local)), $wsfedurl (URL du service d’authentification unique) et $filepath (chemin d’accès dans lequel vous avez téléchargé le fichier de certificat) à partir du Portail Azure, puis exécutez les commandes suivantes pour configurer un nouveau fournisseur d’identité approuvé.

    > [!TIP]
    > Si vous ne connaissez pas PowerShell ou que vous souhaitez en savoir plus sur son fonctionnement, consultez [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Ensuite, effectuez les étapes suivantes pour activer le fournisseur d’identité approuvé pour votre application :

    a. Dans l’Administration centrale, accédez à **Gérer les applications web** et sélectionnez l’application web que vous souhaitez sécuriser avec Azure AD.

    b. Dans le ruban, cliquez sur **Fournisseurs d’authentification** et choisissez la zone que vous souhaitez utiliser.

    c. Sélectionnez **Fournisseur d’identité approuvé** et sélectionnez le fournisseur d’identité que vous venez d’inscrire, nommé *AzureAD*.

    d. Dans le paramètre d’URL de page de connexion, sélectionnez **Page de connexion personnalisée** et spécifiez la valeur « /_trust/ ».

    e. Cliquez sur **OK**.

    ![Configuration de votre fournisseur d’authentification](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Certains utilisateurs externes, dont l’UPN est altéré (par exemple, `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`) ne pourront pas utiliser cette intégration de l’authentification unique. Les clients pourront bientôt configurer la gestion de l’UPN selon le type d’utilisateur sur l’application. Par la suite, tous vos utilisateurs invités devraient être en mesure d’utiliser l’authentification unique de façon transparente en tant qu’employés de l’organisation.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez `brittasimon@yourcompanydomain.extension`  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Créer un groupe de sécurité Azure Active Directory dans le portail Azure

1. Cliquez sur **Azure Active Directory > Tous les groupes**.

    ![Créer un groupe de sécurité Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Cliquez sur **Nouveau groupe**.

    ![Créer un groupe de sécurité Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Complétez les champs **Type de groupe**, **Nom du groupe**, **Description du groupe**, **Type d’appartenance**. Cliquez sur la flèche pour sélectionner des membres, puis recherchez le membre que vous souhaitez ajouter au groupe ou cliquez dessus. Cliquez sur **Sélectionner** pour ajouter les membres sélectionnés, puis cliquez sur **Créer**.

    ![Créer un groupe de sécurité Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Pour affecter des groupes de sécurité Azure Active Directory à SharePoint en local, il est nécessaire d’installer et de configurer [AzureCP](https://yvand.github.io/AzureCP/) dans la batterie de serveurs SharePoint en local OU de développer et de configurer un autre fournisseur de revendications personnalisé pour SharePoint.  Consultez la section d’informations supplémentaires à la fin du document concernant la création de votre propre fournisseur de revendications personnalisé, si vous n’utilisez pas le fournisseur de revendications Azure.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Accorder l’accès à un groupe de sécurité local SharePoint

**Configurer des groupes de sécurité et des autorisations sur l’inscription d’application**

1. Dans le portail Azure, sélectionnez **Azure Active Directory**, puis **Inscriptions d’applications**.

    ![Panneau Applications d’entreprise](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Dans la zone de recherche, tapez et sélectionnez **SharePoint local**.

    ![SharePoint (local) dans la liste des résultats](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Cliquez sur **Manifeste**.

    ![Option Manifeste](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Modifiez `groupMembershipClaims` : `NULL` en `groupMembershipClaims`: `SecurityGroup`. Cliquez ensuite sur Enregistrer.

    ![Modifier le manifeste](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Cliquez sur **Paramètres**, puis sur **Autorisations requises**.

    ![Autorisations requises](./media/sharepoint-on-premises-tutorial/settings.png)

6. Cliquez sur **Ajouter**, puis sur **Sélectionner une API**.

    ![Accès à l’interface de programmation d’applications](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Ajoutez **Windows Azure Active Directory** et **API Microsoft Graph**, mais il n’est possible de sélectionner qu’une option à la fois.

    ![Sélection d’API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Sélectionnez Windows Azure Active Directory, cochez des données dans l’annuaire Lecture, puis cliquez sur Sélectionner. Revenez en arrière et ajoutez Microsoft Graph, puis sélectionnez également des données dans l’annuaire Lecture.  Cliquez sur Sélectionner, puis sur Terminé.

    ![Activer l’accès](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. À présent, sous Paramètres requis, cliquez sur **Accorder des autorisations**, puis cliquez sur Oui pour Accorder des autorisations.

    ![Accorder des autorisations](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Vérifiez les notifications pour déterminer si les autorisations ont été correctement accordées.  Si elles ne le sont pas, le fournisseur de revendications Azure ne fonctionnera pas correctement et il ne sera pas possible de configurer SharePoint (local) avec des groupes de sécurité Azure Active Directory.

10. Configurez le fournisseur de revendications Azure sur la batterie de serveurs SharePoint (local) ou une autre solution de fournisseur de revendications personnalisé.  Dans cet exemple, nous utilisons le fournisseur de revendications Azure.

    > [!NOTE]
    > Veuillez noter que le fournisseur de revendications Azure n’est pas un produit Microsoft ou pris en charge par le Support technique Microsoft. Téléchargez, installez et configurez le fournisseur de revendications Azure sur la batterie de serveurs SharePoint (local) en procédant de la manière décrite dans https://yvand.github.io/AzureCP/. 

11. **Accordez l’accès au groupe de sécurité Azure Active Directory dans SharePoint (local)**  : les groupes doivent avoir accès à l’application dans SharePoint (local).  Procédez comme suit pour définir les autorisations d’accès à l’application web.

12. Dans Administration centrale, cliquez sur Gestion d’applications, Gérer les applications web, sélectionnez l’application web pour activer le ruban, puis cliquez sur Stratégie d’utilisateur.

    ![Administration centrale](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Sous Stratégie de l’application web, cliquez sur Ajouter des utilisateurs, sélectionnez la zone, puis cliquez sur Suivant.  Cliquez sur le Carnet d’adresses.

    ![Stratégie de l’application web](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Ensuite, recherchez et ajoutez le groupe de sécurité Azure Active Directory, puis cliquez sur OK.

    ![Ajout de groupe de sécurité](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Sélectionnez les autorisations, puis cliquez sur Terminer.

    ![Ajout de groupe de sécurité](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Vous pouvez voir que le groupe Azure Active Directory a été ajouté sous Stratégie de l’application web.  La revendication de groupe affiche l’ID d’objet du groupe de sécurité Azure Active Directory pour le nom d’utilisateur.

    ![Ajout de groupe de sécurité](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Accédez à la collection de sites SharePoint et ajoutez-y également le groupe. Cliquez sur Paramètres du Site, puis sur Autorisations du site et Accorder des autorisations.  Recherchez la revendication Rôle de groupe, affecter le niveau d’autorisation, puis cliquez sur Partager.

    ![Ajout de groupe de sécurité](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Configuration d’un fournisseur d’identité approuvé pour plusieurs applications web

La configuration peut s’appliquer à une application web. Toutefois, si vous prévoyez d’utiliser le même fournisseur d’identité approuvé pour plusieurs applications web, une configuration supplémentaire sera nécessaire. Par exemple, supposons que nous avons étendu une application web pour utiliser l’URL `https://portal.contoso.local`, et que nous voulons maintenant aussi authentifier les utilisateurs auprès de `https://sales.contoso.local`. Pour ce faire, nous devons mettre à jour le fournisseur d’identité de manière à honorer le paramètre WReply, et mettre à jour l’inscription de l’application dans Azure AD pour ajouter une URL de réponse.

1. Dans le portail Azure, ouvrez le répertoire Azure Active Directory. Cliquez sur **Inscriptions des applications**, puis cliquez sur **Afficher toutes les applications**. Cliquez sur l’application que vous avez créée précédemment (intégration SAML de SharePoint).

2. Cliquez sur **Settings**.

3. Dans le panneau Paramètres, cliquez sur **URL de réponse**.

4. Entrez l’URL de l’application web supplémentaire en y ajoutant `/_trust/default.aspx` (comme dans `https://sales.contoso.local/_trust/default.aspx`), puis cliquez sur **Enregistrer**.

5. Sur le serveur SharePoint, ouvrez **SharePoint 2016 Management Shell**, puis exécutez les commandes suivantes, en utilisant le nom de l’émetteur de jeton d’identité approuvé que vous avez utilisé précédemment.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. Sur le site Administration centrale, accédez à l’application web et activez le fournisseur d’identité approuvé existant. N’oubliez pas de configurer l’URL de la page de connexion comme une page de connexion personnalisée `/_trust/`.

7. Sur le site Administration centrale, cliquez sur l’application web et choisissez **Stratégie utilisateur**. Ajoutez un utilisateur disposant des autorisations nécessaires, comme indiqué précédemment dans cet article.

### <a name="fixing-people-picker"></a>Correction du sélecteur de personnes

Les utilisateurs peuvent maintenant se connecter à SharePoint 2016 à l’aide d’identités Azure Active Directory, mais il est encore possible d’améliorer l’expérience utilisateur. Par exemple, la recherche d’un utilisateur présente plusieurs résultats de recherche dans le sélecteur de personnes. Il existe un résultat de recherche pour chacun des trois types de revendications qui ont été créés dans le mappage de revendications. Pour choisir un utilisateur à l’aide du sélecteur de personnes, vous devez taper son nom d’utilisateur exactement et choisir le résultat de revendication **nom**.

![Résultats de recherche de revendications](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Il n’existe aucune validation des valeurs que vous recherchez, ce qui peut entraîner des fautes d’orthographe ou faire en sorte que des utilisateurs choisissent accidentellement le type de revendication incorrect à affecter, comme la revendication **SurName**. Cela risque d’empêcher les utilisateurs d’accéder aux ressources.

Pour faciliter ce scénario, il existe une solution open source appelée [AzureCP](https://yvand.github.io/AzureCP/) qui offre un fournisseur de revendications personnalisé pour SharePoint 2016. Elle utilise Azure AD Graph pour résoudre les valeurs entrées par les utilisateurs et effectuer la validation. Pour en savoir plus, consultez [cette page](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Affecter un groupe de sécurité Azure Active Directory dans le portail Azure

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **SharePoint (local)** .

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **SharePoint (local)** .

    ![Lien SharePoint (local) dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le **Ajouter un utilisateur**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Recherchez le groupe de sécurité à utiliser, puis cliquez sur le groupe pour l’ajouter à la section Sélectionner des membres. Cliquez sur **Sélectionner**, puis sur **Affecter**.

    ![Recherche de groupe de sécurité](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Cochez les notifications dans la barre de menus pour être informé que le groupe a été correctement affecté à l’Application d’entreprise dans le portail Azure.

### <a name="create-sharepoint-on-premises-test-user"></a>Créer un utilisateur de test SharePoint (local)

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SharePoint (local). Pour ajouter les utilisateurs dans la plateforme SharePoint (local), faites-vous aider par  [l’équipe de support de SharePoint (local)](https://support.office.com/). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette SharePoint (local) dans le volet d’accès, vous devez être connecté automatiquement à l’application SharePoint (local) pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
