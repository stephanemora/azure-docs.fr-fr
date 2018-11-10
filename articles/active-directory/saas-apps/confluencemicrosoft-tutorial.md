---
title: 'Didacticiel : Intégration d’Azure Active Directory à Confluence SAML SSO by Microsoft | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Confluence SAML SSO by Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: jeedes
ms.openlocfilehash: 8c669e13ec3345ff75024c54eda8c13ad194705a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741128"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Didacticiel : Intégration d’Azure Active Directory à Confluence SAML SSO by Microsoft

Dans ce didacticiel, vous allez apprendre à intégrer Confluence SAML SSO by Microsoft à Azure Active Directory (Azure AD).

L’intégration de Confluence SAML SSO by Microsoft à Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Confluence SAML SSO by Microsoft.
- Vous pouvez permettre à vos utilisateurs d’être automatiquement authentifiés dans Confluence SAML SSO by Microsoft (authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="description"></a>Description :

Utilisez votre compte Microsoft Azure Active Directory avec le serveur Atlassian Confluence pour activer l’authentification unique. Ainsi, l’ensemble des utilisateurs de votre organisation peuvent utiliser les informations d’identification d’Azure AD pour se connecter à l’application Confluence. Ce plug-in utilise SAML 2.0 pour la fédération.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Confluence SAML SSO by Microsoft, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une application serveur Confluence installée sur un serveur Windows 64 bits (local ou dans l’infrastructure IaaS cloud)
- L’activation du HTTPS dans le serveur
- Notez que les versions prises en charge par le plug-in Confluence sont mentionnées dans la section ci-dessous.
- L’accessibilité du serveur Confluence via Internet (particulièrement pour la page de connexion Azure AD pour l’authentification) et la capacité à recevoir le jeton d’Azure AD
- La création d’informations d’identification administrateur dans Confluence
- La désactivation de WebSudo dans Confluence
- La création d’un utilisateur de test dans l’application serveur Confluence

> [!NOTE]
> Il est déconseillé d’utiliser un environnement de production Confluence pour tester les étapes de ce didacticiel. Testez d’abord l’intégration dans l’environnement de développement ou l’environnement intermédiaire de l’application, puis passez à l’environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez bénéficier de [l’offre d’essai](https://azure.microsoft.com/pricing/free-trial/) d’une durée d’un mois.

## <a name="supported-versions-of-confluence"></a>Versions de Confluence prises en charge

Les versions suivantes de Confluence sont actuellement prises en charge :

- Confluence : 5.0 à 5.10
- Confluence : 6.0.1
- Confluence : 6.2.1
- Confluence : 6.6.2
- Confluence : 6.8.1
- Confluence : 6.12.0

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Confluence SAML SSO by Microsoft à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Ajout de Confluence SAML SSO by Microsoft à partir de la galerie

Pour configurer l’intégration de Confluence SAML SSO by Microsoft à Azure AD, vous devez ajouter Confluence SAML SSO by Microsoft, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Confluence SAML SSO by Microsoft à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Confluence SAML SSO by Microsoft**, sélectionnez **Confluence SAML SSO by Microsoft** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Confluence SAML SSO by Microsoft dans la liste des résultats](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Confluence SAML SSO by Microsoft, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Confluence SAML SSO by Microsoft équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Confluence SAML SSO by Microsoft associé.

Pour configurer et tester l’authentification unique Azure AD avec Confluence SAML SSO by Microsoft, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Confluence SAML SSO by Microsoft](#creating-confluence-saml-sso-by-microsoft-test-user)** pour avoir un équivalent de Britta Simon dans Confluence SAML SSO by Microsoft lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Confluence SAML SSO by Microsoft.

**Pour configurer l’authentification unique Azure AD avec Confluence SAML SSO by Microsoft, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Confluence SAML SSO by Microsoft**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Confluance SAML SSO by Microsoft](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<domain:port>/plugins/servlet/saml/auth`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<domain:port>/`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Le port est facultatif s’il s’agit d’une URL nommée. Ces valeurs sont reçues durant la configuration du plug-in Confluence qui est décrite plus loin dans le didacticiel.

5. Sur la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’ **URL des métadonnées de fédération de l’application** , puis collez-la dans le bloc-notes.

    ![Lien Téléchargement de certificat](./media/confluencemicrosoft-tutorial/tutorial_metadataurl.png)

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre instance de Confluence en tant qu’administrateur.

7. Pointez sur le roue dentée, puis cliquez sur **Modules complémentaires**.

    ![Configurer l'authentification unique](./media/confluencemicrosoft-tutorial/addon1.png)

8. Téléchargez le plug-in depuis le [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=56503). Chargez manuellement le plug-in fourni par Microsoft à l’aide du menu **Upload add-on** (Charger le module complémentaire). Le téléchargement du plug-in est couvert dans [Contrat de Services Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configurer l'authentification unique](./media/confluencemicrosoft-tutorial/addon12.png)

9. Une fois que le plug-in est installé, il s’affiche sous **User Installed** (Installé par l’utilisateur), dans la section **Manage add-ons** (Gérer les modules complémentaires). Cliquez sur **Configurer** pour configurer le nouveau plug-in.

    ![Configurer l'authentification unique](./media/confluencemicrosoft-tutorial/addon13.png)

10. Effectuez les opérations suivantes dans la page de configuration :

    ![Configurer l'authentification unique](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Vérifiez qu’un seul certificat est associé à l’application pour éviter toute erreur liée à la résolution des métadonnées. Si plusieurs certificats sont associés, l’administrateur verra un message d’erreur s’afficher lors de la résolution des métadonnées.

    a. Dans la zone de texte **URL des métadonnées**, collez la valeur **URL des métadonnées de fédération de l’application** que vous avez copiée sur le portail Azure, puis cliquez sur le bouton **Résoudre**. L’URL des métadonnées IdP est alors lue et tous les champs sont renseignés.

    b. Copiez les valeurs des champs **Identifier, Reply URL et Sign on URL**, puis collez-les dans les zones de texte **Identificateur, URL de réponse et URL de connexion** correspondantes dans la section **Domaine et URL Confluence SAML SSO by Microsoft** du portail Azure.

    c. Dans **Login Button Name** (Nom du bouton de connexion), tapez le nom du bouton que les utilisateurs doivent voir sur l’écran de connexion.

    d. Dans **SAML User ID Locations** (Emplacements de l’identificateur d’utilisateur SAML), sélectionnez **User ID is in the NameIdentifier element of the Subject statement** (L’ID utilisateur se trouve dans l’élément NameIdentifier de l’instruction Subject ) ou **User ID is in an Attribute element** (L’identificateur d’utilisateur se trouve dans l’élément Attribute).  Cet ID doit être l’ID utilisateur Confluence. Si aucun identificateur d’utilisateur correspondant n’est trouvé, le système n’autorise pas l’utilisateur à se connecter. 

    > [!Note]
    > Par défaut, l’emplacement de l’identificateur d’utilisateur SAML est défini sur l’élément NameIdentifier. Vous pouvez remplacer cela par une option d’attribut et entrer le nom de l’attribut souhaité.
    
    e. Si vous sélectionnez l’option **User ID is in an Attribute element**, dans la zone de texte **Attribute name** (Nom de l’attribut), tapez le nom de l’attribut dans lequel l’identificateur d’utilisateur doit se trouver. 

    f. Si vous utilisez le domaine fédéré (AD FS, etc.) avec Azure AD, cochez l’option **Enable Home Realm Discovery** (Activer la détection de domaine d’accueil), puis entrez un nom de domaine sous **Domain Name**.
    
    g. Si la connexion est basée sur AD FS, tapez le nom du domaine dans le champ **Domain Name**.

    h. Cochez l’option **Enable Single Sign out** (Activer la déconnexion unique) si vous souhaitez qu’un utilisateur soit déconnecté d’Azure AD lorsqu’il se déconnecte de Confluence. 

    i. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

    > [!NOTE]
    > Pour plus d’informations sur l’installation et la résolution des problèmes, consultez la page [Guide d’administration du connecteur d’authentification unique MS Confluence](../ms-confluence-jira-plugin-adminguide.md) et la [FAQ](../ms-confluence-jira-plugin-faq.md).

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

### <a name="creating-confluence-saml-sso-by-microsoft-test-user"></a>Création d’un utilisateur de test Confluence SAML SSO by Microsoft

Pour permettre aux utilisateurs Azure AD de se connecter à un serveur local Confluence, ils doivent être provisionnés dans Confluence SAML SSO by Microsoft. Pour Confluence SAML SSO by Microsoft, l’attribution des utilisateurs se fait manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre serveur local Confluence en tant qu’administrateur.

2. Pointez sur la roue dentée, puis cliquez sur **Gestion des utilisateurs**.

    ![Ajouter un employé](./media/confluencemicrosoft-tutorial/user1.png) 

3. Dans la section Utilisateurs, cliquez sur l’onglet **Add users** (Ajouter des utilisateurs). Dans la page de boîte de dialogue **Add a User** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un employé](./media/confluencemicrosoft-tutorial/user2.png) 

    a. Dans la zone de texte **Username** (Nom d’utilisateur), tapez le nom d’un utilisateur, par exemple, Britta Simon.

    b. Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet d’un utilisateur, par exemple, Britta Simon.

    c. Dans la zone de texte **Email** (E-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de Britta Simon.

    e. Cliquez sur **Confirm Password** (Confirmer le mot de passe), puis entrez de nouveau le mot de passe.

    f. Cliquez sur le bouton **Ajouter**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Confluence SAML SSO by Microsoft.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Confluence SAML SSO by Microsoft**.

    ![Configurer l'authentification unique](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Confluence SAML SSO by Microsoft dans le volet d’accès, vous devez être connecté automatiquement à votre application Confluence SAML SSO by Microsoft.
Pour plus d’informations concernant le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

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
