---
title: 'Didacticiel : Intégration d’Azure Active Directory dans TextMagic | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.openlocfilehash: ed5107d581c880d130901bfb31d34afb9e986635
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190086"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Didacticiel : Intégration d’Azure Active Directory dans TextMagic

Ce didacticiel explique comment intégrer TextMagic avec Azure Active Directory (Azure AD).

L’intégration de TextMagic avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à TextMagic.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à TextMagic (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec TextMagic, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement TextMagic pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de TextMagic à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-textmagic-from-the-gallery"></a>Ajout de TextMagic à partir de la galerie

Pour configurer l’intégration de TextMagic avec Azure AD, vous devez ajouter TextMagic à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter TextMagic à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **TextMagic**, sélectionnez **TextMagic** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![TextMagic dans la liste des résultats](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec TextMagic sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur TextMagic correspondant à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur TextMagic associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec TextMagic, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test TextMagic](#creating-a-textmagic-test-user)** pour avoir dans TextMagic un équivalent de Britta Simon lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application TextMagic.

**Pour configurer l’authentification unique Azure AD avec TextMagic, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **TextMagic**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL TextMagic](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    Dans la zone de texte **Identificateur**, tapez une URL : `https://my.textmagic.com/saml/metadata`

5. L’application TextMagic attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur et revendications** dans la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur et revendications**.

    ![image](./media/textmagic-tutorial/i4-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur et revendications**, configurez le jeton SAML comme sur l’image ci-dessus, puis effectuez les étapes suivantes :

    | NOM  | Attribut source  | Espace de noms |
    | --------------- | --------------- | --------------- |
    | société | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
    a. Cliquez sur l’**icône de modification** pour modifier la **valeur du nom d’identificateur** **user.userprincipalname** en **user.mail**.

    ![Attribut TextMagic](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. Cliquez sur **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    c. Dans la zone de texte **Nom**, indiquez le nom d’attribut pour cette ligne.

    d. Entrez la valeur **Espace de noms**.

    e. Sélectionnez Source comme **Attribut**.

    f. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    g. Cliquez sur **OK**.

    h. Cliquez sur **Enregistrer**. 

7. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. Dans la section **Configurer TextMagic**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de TextMagic](common/configuresection.png)

9. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise TextMagic en tant qu’administrateur.

10. Sélectionnez **Paramètres du compte** sous le nom d’utilisateur.

    ![Configuration de TextMagic](./media/textmagic-tutorial/config1.png)

11. Cliquez sur l’onglet **Authentification unique (SSO)**, puis complétez les champs suivants :  
    
    ![Configuration de TextMagic](./media/textmagic-tutorial/config2.png)

    a. Dans la zone de texte  **ID d’entité du fournisseur d’identité** , collez la valeur  **Identificateur Azure AD** copiée à partir du Portail Azure.

    b. Dans la zone de texte  **URL SSO du fournisseur d’identité** , collez la valeur  **URL de connexion** copiée à partir du Portail Azure.

    c. Dans la zone de texte  **URL SLO du fournisseur d’identité** , collez la valeur  **URL de déconnexion** copiée à partir du Portail Azure.

    d. Ouvrez dans le Bloc-notes votre **certificat codé en base 64** téléchargé à partir du Portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte  **Certificat x509 public** .

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
  
### <a name="creating-a-textmagic-test-user"></a>Création d’un utilisateur de test TextMagic

L’application prend en charge la **configuration d’utilisateur juste à temps**, et après authentification, les utilisateurs sont créés automatiquement dans l’application. Vous devez entrer les informations une seule fois lors de la première connexion pour activer le sous-compte dans le système.
Vous n’avez aucune opération à effectuer dans cette section.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TextMagic.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **TextMagic**.

    ![Configurer l'authentification unique](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette TextMagic dans le panneau d’accès, vous devez être connecté automatiquement à votre application TextMagic.
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
