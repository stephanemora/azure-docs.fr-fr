---
title: 'Didacticiel : Intégration d’Azure Active Directory à Zoom | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e36d1bb91e70e21ee1940e189bfedaebafa4412
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975950"
---
# <a name="tutorial-integrate-zoom-with-azure-active-directory"></a>Didacticiel : Intégrer Zoom à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer Zoom dans Azure Active Directory (Azure AD). Quand vous intégrez Zoom à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Zoom.
* Permettre à vos utilisateurs de se connecter automatiquement à Zoom avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Zoom pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Zoom prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**

## <a name="adding-zoom-from-the-gallery"></a>Ajout de Zoom à partir de la galerie

Pour configurer l’intégration de Zoom avec Azure AD, vous devez ajouter Zoom, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Zoom** dans la zone de recherche.
1. Sélectionnez **Zoom** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Configurer et tester l’authentification unique Azure AD pour Zoom

Configurez et testez l’authentification unique Azure AD avec Zoom à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Zoom associé.

Pour configurer et tester l’authentification unique Azure AD avec Zoom, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Zoom](#configure-zoom-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Zoom](#create-zoom-test-user)** pour disposer, dans Zoom, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Zoom**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<companyname>.zoom.us`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `<companyname>.zoom.us`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Zoom](https://support.zoom.us/hc/en-us). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application Zoom s’attend à recevoir les assertions SAML dans un certain format, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de votre jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône  **Modifier** pour ouvrir la boîte de dialogue  **Attributs utilisateur** .

    ![image](common/edit-attribute.png)

6. En plus de ce qui précède, l’application Zoom s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous : 

    | Nom | Espace de noms  |  Attribut source|
    | ---------------| --------------- | --------- |
    | Adresse de messagerie  | user.mail  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail` |
    | Prénom  | user.givenname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | Nom  | user.surname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | Numéro de téléphone  | user.telephonenumber  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone` |
    | department  | user.department  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department` |
    | role |    user.assignedrole |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role` |

    > [!NOTE]
    > Cliquez [ici](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) pour savoir comment configurer un rôle dans Azure AD.

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Sélectionnez Source comme **Attribut**.

    d. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    e. Cliquez sur **OK**.

    f. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > &frankly peut attendre une revendication de groupe dans la charge utile SAML. Par conséquent, si vous avez créé un groupe, contactez l’[équipe de support client Zoom](https://support.zoom.us/hc/en-us) et communiquez-lui les informations de groupe pour qu’elle puisse également les configurer de son côté. Vous devez également fournir l’ID d’objet à l’[équipe de support client Zoom](https://support.zoom.us/hc/en-us) afin qu’elle le configure de son côté. Pour obtenir l’ID d’objet, veuillez suivre le [document](https://support.zoom.us/hc/en-us/articles/115005887566).

4. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Zoom**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zoom.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Zoom**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-zoom-sso"></a>Configurer l’authentification unique Zoom

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zoom en tant qu’administrateur.

2. Cliquez sur l’onglet **Single Sign-On** .

    ![Onglet Single Sign-On](./media/zoom-tutorial/ic784700.png "Single sign-on")

3. Cliquez sur l’onglet **Security Control**, puis accédez aux paramètres **Single Sign-On**.

4. Dans la section Single Sign-On, procédez comme suit :

    ![Section Single Sign-On](./media/zoom-tutorial/ic784701.png "Single sign-on")

    a. Dans la zone de texte **URL de la page de connexion** , collez la valeur **URL de connexion** que vous avez copiée à partir du portail Azure.

    b. Pour la valeur d’**URL de la page de déconnexion** valeur, vous devez vous rendre sur le portail Azure et cliquer sur **Azure Active Directory** à gauche, puis accéder à **Inscriptions d’applications**.

    ![Bouton Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Cliquez sur **Points de terminaison**

    ![Bouton Point de terminaison](./media/zoom-tutorial/endpoint.png)

    d. Copiez **SAML-P SIGN-OUT ENDPOINT** et collez-le dans la zone de texte **URL de la page de déconnexion**.

    ![Bouton Copier le point de terminaison](./media/zoom-tutorial/endpoint1.png)

    e. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity provider certificate** .

    f. Dans la zone de texte **Émetteur**, collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure. 

    g. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Pour plus d’informations, consultez la documentation de Zoom à l’adresse [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Créer un utilisateur de test Zoom

Pour pouvoir se connecter à Zoom, les utilisateurs d’Azure AD doivent être provisionnés dans Zoom. Dans le cas de Zoom, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise **Zoom** en tant qu’administrateur.

2. Cliquez sur l’onglet **Account Management**, puis sur **User Management**.

3. Dans la section User Management, cliquez sur **Add users**.

    ![Gestion des utilisateurs](./media/zoom-tutorial/ic784703.png "gestion des utilisateurs")

4. Dans la page **Add users** , procédez comme suit :

    ![Ajouter des utilisateurs](./media/zoom-tutorial/ic784704.png "Ajouter des utilisateurs")

    a. Comme **User Type**, sélectionnez **Basic**.

    b. Tapez l’adresse e-mail du compte Azure AD valide que vous souhaitez approvisionner dans la zone de texte **Emails**.

    c. Cliquez sur **Add**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zoom pour approvisionner des comptes d’utilisateur Azure Active Directory.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Zoom dans le panneau d’accès doit vous connecter automatiquement à l’application Zoom pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

