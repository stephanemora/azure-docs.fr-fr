---
title: 'Didacticiel : Intégration d’Azure Active Directory à Procore SSO | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 1cd82188ef9f5a4e0078a8ea21882f99bb8b8f44
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171284"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Didacticiel : Intégration d’Azure Active Directory à Procore SSO

Dans ce didacticiel, vous allez apprendre à intégrer Procore SSO à Azure Active Directory (Azure AD).

L’intégration de Procore SSO dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Procore SSO.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Procore SSO par le biais de l’authentification unique (SSO) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Procore SSO, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Procore SSO pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Procore SSO à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-procore-sso-from-the-gallery"></a>Ajout de Procore SSO à partir de la galerie

Pour configurer l’intégration de Procore SSO à Azure AD, vous devez ajouter Procore SSO à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Procore SSO à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **Procore SSO**, sélectionnez **Procore SSO** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Procore SSO dans la liste des résultats](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Procore SSO avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Procore SSO équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Procore SSO associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Procore SSO, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Procore SSO](#creating-a-procore-sso-test-user)** pour avoir un équivalent de Britta Simon dans Procore SSO lié à la représentation Azure AD associée de l'utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure et configurer l’authentification unique dans votre application Procore SSO.

**Pour configurer l’authentification unique Azure AD avec Procore SSO, procédez comme suit :**

1. Sur le portail Azure, accédez à la page d’intégration de l’application **Procore SSO** et cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

    ![Informations d’authentification unique dans Domaine et URL Procore SSO](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. Sur la page **Certificat de signature SAML**, accédez à la section **Certificat de signature SAML** et cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. Dans la section **Configurer Procore SSO**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de Procore SSO](common/configuresection.png)

7. Pour configurer l’authentification unique sur **Procore SSO**, connectez-vous à votre site d’entreprise Procore en tant qu’administrateur.

8. Dans la liste déroulante de boîte à outils, cliquez sur **Admin** pour ouvrir la page de paramètres de l’authentification unique.

    ![Configurer l'authentification unique](./media/procoresso-tutorial/procore_tool_admin.png)

9. Collez les valeurs dans les zones comme décrit ci-dessous-

    ![Configurer l'authentification unique](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Dans la zone de texte **URL de l'émetteur de l'authentification unique**, collez la valeur de l'**Identificateur Azure AD** copiée à partir du portail Azure.

    b. Dans la zone **URL de la cible de l'authentification SAML**, collez la valeur de l'**URL de connexion** copiée à partir du portail Azure.

    c. Ouvrez maintenant le fichier **XML de métadonnées de fédération** téléchargé à partir du portail Azure et copiez le certificat dans la balise nommée **X509Certificate**. Collez la valeur copiée dans la zone **Certificat x509 d’authentification unique**.

10. Cliquez sur **Enregistrer les modifications**.

11. Après ces paramètres, vous devez envoyer le **nom de domaine** (par ex. **contoso.com**) via lequel vous connectez à Procore à [l’équipe de support technique de Procore](https://support.procore.com/), qui activera l’authentification unique fédérée pour ce domaine.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
       Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-a-procore-sso-test-user"></a>Création d’un utilisateur de test Procore SSO

Suivez les étapes ci-dessous pour créer un utilisateur de test Procore du côté Procore SSOc.

1. Connectez-vous à votre site d’entreprise Procore en tant qu’administrateur.  

2. Dans la liste déroulante de boîte à outils, cliquez sur **Annuaire** pour ouvrir la page d’annuaire de l’entreprise.

    ![Configurer l'authentification unique](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Cliquez sur l’option **Ajouter une personne** pour ouvrir le formulaire et réglez les options suivantes -

    ![Configurer l'authentification unique](./media/procoresso-tutorial/Procore_user_add.png)

    a. Dans la zone de texte **First Name**, tapez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name**, tapez le nom de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Adresse e-mail** , tapez l’adresse de messagerie de l’utilisateur, par exemple **BrittaSimon@contoso.com**.

    d. Sélectionnez **Modèle d’autorisation** pour **Appliquer le modèle d’autorisation plus tard**.

    e. Cliquez sur **Créer**.

4. Vérifiez et mettez à jour les détails du contact nouvellement ajouté.

    ![Configurer l'authentification unique](./media/procoresso-tutorial/Procore_user_check.png)

5. Cliquez sur **Enregistrer et envoyer l’invitation** (si une invitation par e-mail est requise) ou **Enregistrer** (enregistrer directement) pour terminer l’inscription de l’utilisateur.
    
    ![Configurer l'authentification unique](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Procore SSO.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Procore SSO**.

    ![Configurer l'authentification unique](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Procore SSO dans le volet d’accès, vous devez être connecté automatiquement à votre application Procore SSO.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
