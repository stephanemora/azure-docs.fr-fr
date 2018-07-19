---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Secret Server (On-Premises) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Secret Server (On-Premises).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 37ae91743077a9cb9ef9b8f97747563a580fada9
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051276"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Didacticiel : Intégration d’Azure Active Directory avec Secret Server (On-Premises)

Dans ce didacticiel, vous allez apprendre à intégrer Secret Server (On-Premises) avec Azure Active Directory (Azure AD).

L’intégration de Secret Server (On-Premises) avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Secret Server (On-Premises).
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Secret Server (On-Premises) (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Secret Server (On-Premises), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Secret Server (On-Premises) pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Secret Server (On-Premises) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Ajout de Secret Server (On-Premises) à partir de la galerie
Pour configurer l’intégration de Secret Server (On-Premises) avec Azure AD, vous devez l’ajouter à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Secret Server (On-Premises) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Secret Server (On-Premises)**, sélectionnez **Secret Server (On-Premises)** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Secret Server (On-Premises) dans la liste des résultats](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Secret Server (On-Premises) grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur de Secret Server (On-Premises) équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur de Secret Server (On-Premises) associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Secret Server (On-Premises), vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test de Secret Server (On-Premises)](#create-a-secret-server-on-premises-test-user)** pour avoir un équivalent de Britta Simon dans Secret Server (On-Premises) lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Secret Server (On-Premises).

**Pour configurer l’authentification unique Azure AD avec Secret Server (On-Premises), procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Secret Server (On-Premises)**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Boîte de dialogue Authentification unique](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

3. Dans la section **Domaine et URL de Secret Server (On-Premises)**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL de Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. Dans la zone de texte **Identificateur**, saisissez la valeur choisie par l’utilisateur comme exemple : `https://secretserveronpremises.azure`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `

    > [!NOTE]
    > L’ID d’entité indiquée ci-dessus est seulement un exemple et vous êtes libre de choisir une valeur unique qui identifie votre instance Secret Server dans Azure AD. Vous devez envoyer cet ID d’entité à [l’équipe du support client Secret Server (On-Premises)](https://thycotic.force.com/support/s/) qui va la configurer de son côté. Pour plus de détails, veuillez lire [cet article](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL de Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe du support client Secret Server (On-Premises)](https://thycotic.force.com/support/s/).

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

6. Cochez la case **Afficher les paramètres avancés de signature de certificat** et sélectionnez **Option de signature** pour **Signer la réponse et l’assertion SAML**.

    ![Options de signature](./media/secretserver-on-premises-tutorial/signing.png)

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
8. Dans la section **Configuration de Secret Server (On-Premises)**, cliquez sur **Configurer Secret Server (On-Premises)** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

9. Pour configurer l’authentification unique côté **Secret Server (On-Premises)**, vous devez envoyer le **Certificat (Base64) téléchargé, l’URL de déconnexion, l’URL du service d’authentification unique SAML** et **l’ID d’entité SAML** à [l’équipe de support de Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Créer un utilisateur de test de Secret Server (On-Premises)

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Secret Server (On-Premises). Pour ajouter les utilisateurs dans la plateforme Secret Server (On-Premises), faites-vous aider par [l’équipe de support de Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Secret Server (On-Premises).

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Secret Server (On-Premises), procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Secret Server (On-Premises)**.

    ![Le lien Secret Server (On-Premises) dans la liste des applications](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Secret Server (On-Premises) dans le volet d’accès, vous devez être connecté automatiquement à votre application Secret Server (On-Premises).
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

