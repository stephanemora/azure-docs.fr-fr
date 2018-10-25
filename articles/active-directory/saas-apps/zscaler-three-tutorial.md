---
title: 'Tutoriel : Intégration d’Azure Active Directory à Zscaler Three | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler Three.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.openlocfilehash: 0ef8fc2ea8b006d49dd54d638183a58bf78a5797
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311953"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Tutoriel : Intégration d’Azure Active Directory à Zscaler Three

L’objectif de ce tutoriel est de montrer comment intégrer Zscaler Three à Azure Active Directory (Azure AD).

L’intégration de Zscaler Three à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler l’accès à Zscaler Three.
- Vous pouvez autoriser les utilisateurs à être automatiquement connectés à Zscaler Three (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Zscaler Three, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Zscaler Three pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Zscaler Three à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-zscaler-three-from-the-gallery"></a>Ajouter Zscaler Three à partir de la galerie

Pour configurer l’intégration de Zscaler Three à Azure AD, vous devez ajouter Zscaler Three, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Zscaler Three à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![APPLICATIONS][2]

3. Cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans le panneau de résultats, sélectionnez **Zscaler Three**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zscaler Three grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur Zscaler Three correspondant à l’utilisateur Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Zscaler Three qui lui est associé.

Pour configurer et tester l’authentification unique Azure AD avec Zscaler Three, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configuration des paramètres de proxy](#configuring-proxy-settings)** pour configurer les paramètres de proxy dans Internet Explorer
3. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Création d’un utilisateur de test Zscaler Three](#creating-a-zscaler-three-test-user)** pour avoir un équivalent de Britta Simon dans Zscaler Three qui est lié à la représentation de l’utilisateur Azure AD.
5. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
6. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Zscaler Three.

**Pour configurer l’authentification unique Azure AD avec Zscaler Three, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Zscaler Three**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. Pour activer le mode **SAML** au détriment d’un autre mode, cliquez sur **Modifier le mode d’authentification unique** en haut de l’écran.

    ![Configurer l'authentification unique](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    Dans la zone de texte URL de réponse, entrez l’URL suivante : `https://login.zscalerthree.net/sfc_sso`

    > [!NOTE]
    > Vous devez mettre à jour cette valeur avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez [l’équipe du support Zscaler Three](https://www.zscaler.com/company/contact).

6. Dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **certificat (Base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l'authentification unique](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. Dans la section **Configurer Zscaler Three**, copiez l’**URL de connexion**.

    ![Configurer l'authentification unique](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zscaler Three en tant qu’administrateur.

10. Dans le menu situé dans la partie supérieure, cliquez sur **Administration**.

    ![Administration](./media/zscaler-three-tutorial/ic800206.png "Administration")

9. Sous **Manage Administrators & Roles**, cliquez sur **Manage Users & Authentication**.

    ![Gérer les utilisateurs et l’authentification](./media/zscaler-three-tutorial/ic800207.png "Gérer les utilisateurs et l’authentification")

10. Dans la section **Choose Authentication Options for your Organization** , procédez comme suit :

    ![Authentication](./media/zscaler-three-tutorial/ic800208.png "Authentication")

    a. Sélectionnez **Authenticate using SAML Single Sign-On**.

    b. Cliquez sur **Configure SAML Single Sign-On Parameters**.

11. Sur la page de dialogue **Configurer les paramètres d’authentification unique SAML**, procédez comme suit, puis cliquez sur **Terminé**

    ![Authentification unique](./media/zscaler-three-tutorial/ic800209.png "Authentification unique")

    a. Collez **l’URL de connexion** que vous avez copiée à partir du portail Azure dans la zone de texte **URL of the SAML Portal to which users are sent for authentication** (URL du portail SAML vers lequel les utilisateurs sont redirigés afin de s’authentifier).

    b. Dans la zone de texte **Attribute containing Login Name**, tapez **NameID**.

    c. Pour charger le certificat téléchargé, cliquez sur **Zscaler pem**.

    d. Sélectionnez **Enable SAML Auto-Provisioning**.

12. Dans la page **Configure User Authentication** , procédez comme suit :

    ![Administration](./media/zscaler-three-tutorial/ic800210.png "Administration")

    a. Cliquez sur **Enregistrer**.

    b. Cliquez sur **Activate Now**.

## <a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer

1. Démarrez **Internet Explorer**.

2. Pour ouvrir la boîte de dialogue **Options Internet**, sélectionnez **Options Internet** dans le menu **Outils**.

     ![Options Internet](./media/zscaler-three-tutorial/ic769492.png "Options Internet")

3. Cliquez sur l’onglet **Connexions** .
  
     ![Connexions](./media/zscaler-three-tutorial/ic769493.png "Connexions")

4. Cliquez sur **Paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres réseau**.

5. Dans la section Serveur proxy, procédez comme suit :

    ![Serveur proxy](./media/zscaler-three-tutorial/ic769494.png "Serveur proxy")

    a. Sélectionnez **Utiliser un serveur proxy pour le réseau local**.

    b. Dans la zone de texte Address, tapez **gateway.zscalerthree.net**.

    c. Dans la zone de texte Port, tapez **80**.

    d. Sélectionnez **Ne pas utiliser de serveur proxy pour les adresses locales**.

    e. Cliquez sur **OK** pour fermer la boîte de dialogue **Paramètres du réseau local**.

6. Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="creating-a-zscaler-three-test-user"></a>Création d’un utilisateur de test Zscaler Three

Pour se connecter à Zscaler Three, les utilisateurs d’Azure AD doivent être attribués dans Zscaler Three. Dans le cas de Zscaler Three, le provisionnement est une tâche manuelle.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Connectez-vous au locataire **Zscaler Three**.

2. Cliquez sur **Administration**.

    ![Administration](./media/zscaler-three-tutorial/ic781035.png "Administration")

3. Cliquez sur **User Management**.

     ![Ajouter](./media/zscaler-three-tutorial/ic781036.png "Ajouter")

4. Sous l’onglet **Utilisateurs**, cliquez sur **Ajouter**.

    ![Ajouter](./media/zscaler-three-tutorial/ic781037.png "Ajouter")

5. Dans la section Add User, procédez comme suit :

    ![Ajouter un utilisateur](./media/zscaler-three-tutorial/ic781038.png "Ajouter un utilisateur")

    a. Renseignez les zones de texte **UserID** (ID d’utilisateur), **User Display Name** (Nom d’affichage de l’utilisateur), **Password** (Mot de passe) et **Confirm Password** (Confirmer le mot de passe), puis sélectionnez **Groups** (Groupes), ainsi que l’attribut **Department** (Département) d’un compte Azure AD valide que vous souhaitez approvisionner.

    b. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zscaler Three pour provisionner des comptes d’utilisateur Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zscaler Three.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Zscaler Three**.

    ![Configurer l'authentification unique](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

En cliquant sur la vignette Zscaler Three dans le Panneau d’accès, vous allez en principe être connecté automatiquement à votre application Zscaler Three.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png