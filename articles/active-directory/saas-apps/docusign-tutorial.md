---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à DocuSign | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306297"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à DocuSign

Dans ce tutoriel, vous allez découvrir comment intégrer DocuSign à Azure Active Directory (Azure AD). Quand vous intégrez DocuSign à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à DocuSign.
* Permettre à vos utilisateurs de se connecter automatiquement à DocuSign avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement DocuSign pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* DocuSign prend en charge l’authentification unique lancée par le **fournisseur de services**

* DocuSign prend en charge le provisionnement d’utilisateurs **juste-à-temps**

* DocuSign prend en charge le [provisionnement automatique d’utilisateurs](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>Ajout de DocuSign à partir de la galerie

Pour configurer l’intégration de DocuSign à Azure AD, vous devez ajouter DocuSign à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **DocuSign** dans la zone de recherche.
1. Sélectionnez **DocuSign** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Configurer et tester l’authentification unique Azure AD pour DocuSign

Configurez et testez l’authentification unique Azure AD avec DocuSign au moyen d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur DocuSign associé.

Pour configurer et tester l’authentification unique Azure AD avec DocuSign, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer DocuSign](#configure-docusign-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur test DocuSign](#create-docusign-test-user)** pour avoir un équivalent de B.Simon dans DocuSign lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **DocuSign**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. La procédure est expliquée ultérieurement dans le tutoriel, dans la section **Afficher les points de terminaison SAML 2.0**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer DocuSign**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en accordant l’accès à DocuSign.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **DocuSign**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-docusign-sso"></a>Configurer l’authentification unique DocuSign

1. Pour automatiser la configuration dans DocuSign, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer DocuSign** pour être dirigé vers l’application DocuSign. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à DocuSign. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 5.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement DocuSign, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise DocuSign en tant qu’administrateur et effectuez les étapes suivantes :

4. Dans l’angle supérieur droit, cliquez sur **l’icône de profil**, puis cliquez sur **Accéder à Admin**.
  
    ![Configuration de l'authentification unique][51]

5. Dans la page des solutions de votre domaine, cliquez sur **Domaines**.

    ![Configuration de l'authentification unique][50]

6. Dans la section **Domaines**, cliquez sur **REVENDIQUER LE DOMAINE**.

    ![Configuration de l'authentification unique][52]

7. Dans la boîte de dialogue de **revendication du domaine**, dans la zone de texte **Nom du domaine**, indiquez votre domaine d’entreprise, puis cliquez sur **REVENDIQUER**. Veillez à vérifier le domaine et assurez-vous que son état est actif.

    ![Configuration de l'authentification unique][53]

8. Dans la page des solutions de votre domaine, cliquez sur **Fournisseurs d'identité**.
  
    ![Configuration de l'authentification unique][54]

9. Dans la section **Fournisseurs d’identité**, cliquez sur **AJOUTER UN FOURNISSEUR D’IDENTITÉ**. 

    ![Configuration de l'authentification unique][55]

10. Dans la page **Identity Provider Settings** (Paramètres du fournisseur d’identité), effectuez les actions suivantes :

    ![Configuration de l'authentification unique][56]

    a. Dans la zone de texte **Nom** , entrez le nom de votre configuration. N’utilisez pas d’espaces.

    b. Dans la zone de texte **Émetteur du fournisseur d’identité**, collez la valeur de l’**identificateur Azure AD** que vous avez copié dans le portail Azure.

    c. Dans la zone de texte **URL de connexion du fournisseur d'identité**, collez la valeur de l’**URL de connexion** que vous avez copiée dans le portail Azure.

    d. Dans la zone de texte **URL de déconnexion du fournisseur d'identité**, collez la valeur de l’**URL de déconnexion** que vous avez copiée dans le portail Azure.

    e. Sélectionnez **Signer la demande d’authentification**.

    f. Sous **Send AuthN request by** (Envoyer la demande d’authentification par), sélectionnez **POST**.

    g. Sous **Send logout request by** (Envoyer la demande de déconnexion par), sélectionnez **GET**.

    h. Dans la section **Mappage des attributs personnalisés**, cliquez sur **AJOUTER UN NOUVEAU MAPPAGE**.

    ![Configuration de l'authentification unique][62]

    i. Choisissez le champ que vous voulez mapper avec la revendication Azure AD. Dans cet exemple, la revendication **emailaddress** est mappée sur la valeur de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Il s’agit du nom de revendication par défaut d’Azure AD pour la revendication e-mail. Ensuite, cliquez sur **ENREGISTRER**.

    ![Configuration de l'authentification unique][57]

    > [!NOTE]
    > Utilisez l’**identificateur d’utilisateur** approprié pour mapper l’utilisateur d’Azure AD au mappage utilisateur DocuSign. Sélectionnez le champ correct et entrez la valeur appropriée en fonction des paramètres de votre organisation.

    j. Dans la section **Certificats du fournisseur d'identité**, cliquez sur **AJOUTER UN CERTIFICAT**, puis chargez le certificat que vous avez téléchargé à partir du portail Azure AD avant de cliquer sur **ENREGISTRER**.

    ![Configuration de l'authentification unique][58]

    k. Dans la section **Fournisseurs d'identité**, cliquez sur **ACTIONS**, puis sur **Points de terminaison**.

    ![Configuration de l'authentification unique][59]

    l. Dans la section **View SAML 2.0 Endpoints** (Afficher les points de terminaison SAML 2.0) du **portail d’administration DocuSign**, effectuez les étapes suivantes :

    ![Configuration de l'authentification unique][60]

    * Copiez la valeur **Service Provider Issuer URL** (URL de l’émetteur du fournisseur d’identité) et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** du portail Azure.

    * Copiez la valeur **Service Provider Login URL** (URL de connexion du fournisseur de services) et collez-la dans la zone de texte **URL de connexion** de la section **Configuration SAML de base** du portail Azure.

    * Cliquez sur **Fermer**

### <a name="create-docusign-test-user"></a>Créer un utilisateur de test DocuSign

Dans cette section, un utilisateur appelé B.Simon est créé dans DocuSign. DocuSign prend en charge le provisionnement d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans DocuSign, il est créé après l’authentification.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette DocuSign dans le panneau d’accès doit vous connecter automatiquement à l’application DocuSign pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer DocuSign avec Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png