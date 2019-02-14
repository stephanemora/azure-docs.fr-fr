---
title: 'Tutoriel : Intégration d’Azure Active Directory à HubSpot | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fd70b80dd512b530d367d57cd9fc04ec28f27b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211697"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutoriel : Intégration d’Azure AD à HubSpot

Dans ce didacticiel, vous allez apprendre à intégrer HubSpot dans Azure Active Directory (Azure AD).

L’intégration de HubSpot dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à HubSpot.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à HubSpot (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec HubSpot, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement HubSpot pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de HubSpot à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-hubspot-from-the-gallery"></a>Ajout de HubSpot à partir de la galerie

Pour configurer l’intégration de HubSpot à Azure AD, vous devez ajouter HubSpot à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter HubSpot à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **HubSpot**. Dans le panneau des résultats, sélectionnez **HubSpot**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec HubSpot avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur HubSpot équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur HubSpot associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec HubSpot, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **Création d’un utilisateur de test HubSpot** pour avoir un équivalent de Britta Simon dans HubSpot lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application HubSpot.

**Pour configurer l’authentification unique Azure AD avec HubSpot, procédez comme suit :**

1. Sur la page d’intégration de l’application **HubSpot** du portail Azure, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/tutorial_general_301.png)

3. Pour activer le mode **SAML** au détriment d’un autre mode, cliquez sur **Modifier le mode d’authentification unique** en haut de l’écran.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/tutorial_general_300.png)

4. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/tutorial_general_302.png)

5. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL HubSpot](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. Dans la zone de texte **Identificateur**, tapez l’URL au format suivant : `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant : `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeur avec l’identificateur et l’URL de réponse réels. La procédure est expliquée plus loin dans le didacticiel. Pour obtenir ces valeurs, contactez l’[équipe de support aux clients HubSpot](https://help.hubspot.com/).

    c. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL HubSpot](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    Dans la zone de texte **URL de connexion**, tapez l’URL : `https://app.hubspot.com/login`

6. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. Dans la section **Configurer HubSpot**, cliquez sur le bouton Copier pour copier les valeurs de l’**URL de connexion** et de l’**Identificateur Azure AD**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur HubSpot.

9. Cliquez sur l’**icône de paramètres** en haut à droite dans la page.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config1.png)

10. Cliquez ensuite sur **Valeurs par défaut du compte**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config2.png)

11. Faites défiler jusqu’à la section **Sécurité** et cliquez sur **Configurer**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config3.png)

12. Dans la section **Set up single sign-on**, procédez comme suit :

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config4.png)

    a. Cliquez sur le bouton **Copier** pour copier l’**URI d’audience (ID entité fournisseur de services)** et collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base** dans le portail Azure.

    b. Cliquez sur le bouton **Copier** pour copier la valeur **Sign on URl,ACS,Recipient ou Redirect** (URI de connexion, ACS, Destinataire ou Redirection) et collez-la dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** dans le portail Azure.

    c. Dans la zone de texte **Identity Provider Identifier or Issuer URL** (URL de l’identificateur ou l’émetteur du fournisseur d’identité), collez l’**Identificateur Azure AD** que vous avez copié sur le portail Azure.

    d. Dans la zone de texte **Identity Provider Single Sign-On URL** (URL d’authentification unique du fournisseur d’identité), collez l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    e. Ouvrez votre  **Certificat (Base64)**  téléchargé dans le Bloc-notes. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte  **Certificat X.509**.

    f. Cliquez sur **Vérifier**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](./media/hubspot-tutorial/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](./media/hubspot-tutorial/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-a-hubspot-test-user"></a>Création d’un utilisateur test HubSpot

Pour se connecter à HubSpot, les utilisateurs d’Azure AD doivent être configurés dans HubSpot.
Dans le cas de HubSpot, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **HubSpot** en tant qu’administrateur.

2. Cliquez sur l’**icône de paramètres** en haut à droite dans la page.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/config1.png)

3. Cliquez sur **Utilisateurs et équipes**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user1.png)

4. Cliquez sur **Create User** (Créer un utilisateur).

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user2.png)

5. Entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**, dans la zone de texte **Add email address(es)** (Ajouter une ou des adresses e-mail), puis cliquez sur **Suivant**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user3.png)

6. Dans la section **Créer des utilisateurs**, passez en revue chaque onglet individuel et sélectionnez les options et autorisations appropriées pour l’utilisateur, puis cliquez sur **Suivant**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user4.png)

7. Cliquez sur **Envoyer** pour envoyer l’invitation à l’utilisateur.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > L’utilisateur invité est activé après avoir accepté l’invitation.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à HubSpot.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **HubSpot**.

    ![Configurer l'authentification unique](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette HubSpot dans le panneau d’accès, vous accédez automatiquement à la page de connexion HubSpot.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png
