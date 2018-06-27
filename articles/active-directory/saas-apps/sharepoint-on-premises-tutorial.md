---
title: 'Tutoriel : Intégration d’Azure Active Directory avec SharePoint (local) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SharePoint (local).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 8cae1e2670ba449c0db28209c54b740e927bbb73
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210375"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutoriel : Intégration d’Azure Active Directory avec SharePoint (local)

Dans ce tutoriel, vous allez apprendre à intégrer SharePoint (local) avec Azure Active Directory (Azure AD).

L’intégration de SharePoint (local) avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SharePoint (local).
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SharePoint (local) (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SharePoint (local), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SharePoint (local) pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SharePoint (local) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Ajout de SharePoint (local) à partir de la galerie
Pour configurer l’intégration de SharePoint (local) avec Azure AD, vous devez l’ajouter à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter SharePoint (local) à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SharePoint (local)**, sélectionnez **SharePoint (local)** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SharePoint (local) dans la liste des résultats](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SharePoint (local) grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SharePoint (local) équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur SharePoint (local) associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec SharePoint (local), vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test SharePoint (local)](#create-a-sharePoint-on-premises-test-user)** pour avoir un équivalent de Britta Simon dans SharePoint (local) lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SharePoint (local).

**Pour configurer l’authentification unique Azure AD avec SharePoint (local), effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SharePoint (local)**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. Dans la section **Domaine et URL SharePoint (local)**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL de SharePoint (local)](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `urn:sharepoint:<YourSharePointServerURL>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support client SharePoint (local)](https://support.office.com/).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur avec l’extension .cer. Copiez et collez le chemin complet du fichier de métadonnées téléchargé dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de SharePoint (local)**, cliquez sur **Configurer SharePoint (local)** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de SharePoint (local)](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > L’application SharePoint (local) utilise un jeton SAML 1.1. Azure Active Directory attend donc une demande WS Fed en provenance de SharePoint Server, et après l’authentification il émet le jeton SAML 1.1.

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise SharePoint (local) en tant qu’administrateur.

8. **Configurer un nouveau fournisseur d’identité approuvé dans SharePoint Server 2016**

    Connectez-vous au serveur SharePoint Server 2016, puis ouvrez SharePoint 2016 Management Shell. Renseignez les valeurs de $realm, $wsfedurl et $filepath à partir du portail Azure et exécutez les commandes suivantes pour configurer un nouveau fournisseur d’identité approuvé.

    > [!TIP]
    > Si vous ne connaissez pas PowerShell ou que vous souhaitez en savoir plus sur son fonctionnement, consultez [SharePoint PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Ensuite, effectuez les étapes suivantes pour activer le fournisseur d’identité approuvé pour votre application :

    a. Dans l’Administration centrale, accédez à **Gérer les applications web** et sélectionnez l’application web que vous souhaitez sécuriser avec Azure AD.

    b. Dans le ruban, cliquez sur **Fournisseurs d’authentification** et choisissez la zone que vous souhaitez utiliser.

    c. Sélectionnez **Fournisseur d’identité approuvé** et sélectionnez le fournisseur d’identité que vous venez d’inscrire, nommé *AzureAD*.

    d. Dans le paramètre d’URL de page de connexion, sélectionnez **Page de connexion personnalisée** et spécifiez la valeur « /_trust/ ».

    e. Cliquez sur **OK**.

    ![Configuration de votre fournisseur d’authentification](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.

### <a name="create-a-sharepoint-on-premises-test-user"></a>Créer un utilisateur de test SharePoint (local)

1. Dans l’Administration centrale, cliquez sur **Gestion des applications**.

2. Dans la page **Gestion des applications**, dans la section **Applications Web**, cliquez sur **Gérer les applications web**.

3. Cliquez sur l’application web appropriée, puis cliquez sur **Stratégie utilisateur**.

4. Dans Stratégie pour l’application web, cliquez sur **Ajouter des utilisateurs**.

    ![Recherche d’un utilisateur en fonction de sa revendication de nom](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. Dans la boîte de dialogue **Ajouter des utilisateurs**, cliquez sur la zone appropriée dans **Zones**, puis sur **Suivant**.

6. Dans la boîte de dialogue **Stratégie pour l’application web**, dans la section **Choisir les utilisateurs**, cliquez sur l’icône **Parcourir**.

7. Dans la zone de texte **Rechercher**, tapez le nom de connexion d’un utilisateur dans votre annuaire, puis cliquez sur **Rechercher**. </br>Par exemple : *demouser@blueskyabove.onmicrosoft.com*.

8. Sous l’en-tête AzureAD dans la liste, sélectionnez la propriété de nom, cliquez sur **Ajouter**, puis sur **OK** pour fermer la boîte de dialogue.

9. Dans Autorisations, cliquez sur **Contrôle total**.

    ![Octroi du contrôle total à un utilisateur de revendications](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Cliquez sur **Terminer**, puis sur **OK**.

### <a name="fixing-people-picker"></a>Correction du sélecteur de personnes

Les utilisateurs peuvent maintenant se connecter à SharePoint 2016 à l’aide d’identités Azure AD, mais il est encore possible d’améliorer l’expérience utilisateur. Par exemple, la recherche d’un utilisateur présente plusieurs résultats de recherche dans le sélecteur de personnes. Il existe un résultat de recherche pour chacun des trois types de revendications qui ont été créés dans le mappage de revendications. Pour choisir un utilisateur à l’aide du sélecteur de personnes, vous devez taper son nom d’utilisateur exactement et choisir le résultat de revendication **nom**.

![Résultats de recherche de revendications](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Il n’existe aucune validation des valeurs que vous recherchez, ce qui peut entraîner des fautes d’orthographe ou faire en sorte que des utilisateurs choisissent accidentellement le type de revendication incorrect à affecter, comme la revendication **SurName**. Cela risque d’empêcher les utilisateurs d’accéder aux ressources.

Pour faciliter ce scénario, il existe une solution open source appelée [AzureCP](https://yvand.github.io/AzureCP/) qui offre un fournisseur de revendications personnalisé pour SharePoint 2016. Elle utilise Azure AD Graph pour résoudre les valeurs entrées par les utilisateurs et effectuer la validation. Pour en savoir plus, consultez [cette page](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SharePoint (local).

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à SharePoint (local), effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **SharePoint (local)**.

    ![Lien SharePoint dans la liste des applications](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette SharePoint (local) dans le volet d’accès, vous devez être connecté automatiquement à votre application SharePoint (local).
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/sharepoint-on-premises-tutorial/tutorial_general_203.png

