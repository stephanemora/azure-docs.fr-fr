---
title: 'Didacticiel : Intégration d’Azure Active Directory à BlueJeans | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095226"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Didacticiel : Intégration d’Azure Active Directory à BlueJeans

Dans ce didacticiel, vous allez apprendre à intégrer BlueJeans à Azure Active Directory (Azure AD).

L’intégration de BlueJeans à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à BlueJeans.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à BlueJeans (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à BlueJeans, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement BlueJeans pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de BlueJeans à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-bluejeans-from-the-gallery"></a>Ajout de BlueJeans à partir de la galerie

Pour configurer l’intégration de BlueJeans à Azure AD, vous devez ajouter BlueJeans à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter BlueJeans à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **BlueJeans**, sélectionnez **BlueJeans** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![BlueJeans dans la liste des résultats](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec BlueJeans avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur BlueJeans correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur BlueJeans associé.

Pour configurer et tester l’authentification unique Azure AD avec BlueJeans, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test BlueJeans](#creating-a-bluejeans-test-user)** pour avoir un équivalent de Britta Simon dans BlueJeans lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application BlueJeans.

**Pour configurer l’authentification unique Azure AD avec BlueJeans, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **BlueJeans**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](./media/bluejeans-tutorial/tutorial_general_301.png)

4. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > La valeur de connexion n’est pas réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe de support client BlueJeans](https://support.bluejeans.com/contact).

6. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. Dans la section **Configurer BlueJeans**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

9. Accédez à **ADMIN \> GROUP SETTINGS \> SECURITY**.

    ![Administrateur](./media/bluejeans-tutorial/IC785868.png "Administrateur")

10. Dans la section **SECURITY**, procédez comme suit :

    ![Authentification unique SAML](./media/bluejeans-tutorial/IC785869.png "Authentification unique SAML")

    a. Sélectionnez **SAML Single Sign On**.

    b. Sélectionnez **Enable automatic provisioning**.

11. Poursuivez en procédant comme suit :

    ![Chemin d’accès du certificat](./media/bluejeans-tutorial/IC785870.png "Chemin d’accès du certificat")

    a. Cliquez sur **Choose File** (Choisir un fichier) pour charger le certificat codé en base 64 que vous avez téléchargé à partir du portail Azure.

    b. Dans la zone de texte **Login URL** (URL de connexion), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Password Change URL** (URL de modification de mot de passe), collez la valeur de l’**URL de modification de mot de passe** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

12. Poursuivez en procédant comme suit :

    ![Enregistrer les modifications](./media/bluejeans-tutorial/IC785874.png "Enregistrer les modifications")

    a. Dans la zone de texte **User ID** (ID utilisateur), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Dans la zone de texte **Email**, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Cliquez sur **ENREGISTRER LES MODIFICATIONS**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-a-bluejeans-test-user"></a>Création d’un utilisateur de test BlueJeans

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans BlueJeans. BlueJeans prend en charge l’attribution automatique d’utilisateurs, qui est activée par défaut. Vous trouverez [ici](bluejeans-provisioning-tutorial.md) plus d’informations sur la façon de configurer l’attribution automatique d’utilisateurs.

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

2. Accédez à **ADMIN \> MANAGE USERS \> ADD USER**.

    ![Administrateur](./media/bluejeans-tutorial/IC785877.png "Administrateur")

    >[!IMPORTANT]
    >L’onglet **ADD USER** est disponible seulement si, sous l’onglet **SECURITY**, l’option **Enable automatic provisioning** (Activer l’approvisionnement automatique) est décochée. 

3. Dans la section **ADD USER** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un utilisateur](./media/bluejeans-tutorial/IC785886.png "Ajouter un utilisateur")

    a. Dans la zone de texte **Prénom**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last Name** (Nom), saisissez le nom de famille de l’utilisateur, par exemple **Simon**.

    c. Dans la zone de texte **Pick a BlueJeans Username** (Choisir un nom d’utilisateur BlueJeans), entrez le nom de l’utilisateur, comme par exemple **Brittasimon**

    d. Dans la zone de texte **Create a Password** (Créer un mot de passe), entrez votre mot de passe.

    e. Dans la zone de texte **Company** (Entreprise), entrez votre entreprise.

    f. Dans la zone de texte **Adresse e-mail**, saisissez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.

    g. Dans la zone de texte **Create a BlueJeans Meeting I.D**  (Créer un ID de réunion BlueJeans), entrez votre ID de réunion.

    h. Dans la zone de texte **Pick a Moderator Passcode** (Choisir un code secret de modérateur), entrez votre code secret.

    i. Cliquez sur **CONTINUE** (Continuer).

    ![Ajouter un utilisateur](./media/bluejeans-tutorial/IC785887.png "Ajouter un utilisateur")

    J. Cliquez sur**ADD USER** (Ajouter un utilisateur).

>[!NOTE]
>Vous pouvez utiliser un autre outil ou une autre API de création de compte d’utilisateur fournis par BlueJeans pour provisionner des comptes d’utilisateur Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à BlueJeans.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **BlueJeans**.

    ![Configurer l'authentification unique](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette BlueJeans dans le panneau d’accès, vous devez vous connecter automatiquement à votre application BlueJeans.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
