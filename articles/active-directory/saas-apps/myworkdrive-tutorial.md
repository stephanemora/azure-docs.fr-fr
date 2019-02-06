---
title: 'Tutoriel : Intégration d’Azure Active Directory à MyWorkDrive | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: f0e2c499619df938bd6f4b05757ba607a9edf244
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183354"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Tutoriel : Intégration d’Azure Active Directory à MyWorkDrive

Dans ce didacticiel, vous allez apprendre à intégrer MyWorkDrive à Azure Active Directory (Azure AD).

L’intégration de MyWorkDrive à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à MyWorkDrive.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à MyWorkDrive (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à MyWorkDrive, vous devez disposer des éléments suivants :

- Un abonnement Azure AD
- Un abonnement MyWorkDrive pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de MyWorkDrive à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-myworkdrive-from-the-gallery"></a>Ajout de MyWorkDrive à partir de la galerie

Pour configurer l’intégration de MyWorkDrive à Azure AD, vous devez ajouter MyWorkDrive à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter MyWorkDrive à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **MyWorkDrive**, sélectionnez **MyWorkDrive** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![MyWorkDrive dans la liste des résultats](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec MyWorkDrive grâce à un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur MyWorkDrive correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur MyWorkDrive associé.

Pour configurer et tester l’authentification unique Azure AD avec MyWorkDrive, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test MyWorkDrive](#creating-a-myworkdrive-test-user)** pour avoir un équivalent de Britta Simon dans MyWorkDrive lié à la représentation Azure AD associée de l'utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application MyWorkDrive.

**Pour configurer l’authentification unique Azure AD avec MyWorkDrive, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **MyWorkDrive**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode démarré par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

     Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles.  Entrez le nom d’hôte du serveur MyWorkDrive de votre propre société : p. ex.
    > 
    > URL de réponse : `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de connexion :`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Contactez l’équipe de support client MyWorkDrive si vous avez des doutes sur la façon de configurer vos propres nom d’hôte et certificat SSL pour ces valeurs.

6. Sur la page **Certificat de signature SAML**, accédez à la section **Certificat de signature SAML** et cliquez sur l'**icône** Copier pour copier l'**URL des métadonnées de fédération d'application** et l'enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/myworkdrive-tutorial/tutorial_myworkdrive_certificate.png)

7. Ouvrez une autre fenêtre de navigateur web et connectez-vous à MyWorkDrive en tant qu’administrateur de la sécurité.

8. Sur le serveur MyWorkDrive, accédez au panneau d'administration, cliquez sur **ENTREPRISE** et procédez comme suit :

    ![Administrateur](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Activez **SAML/ADFS SSO**.

    b. Sélectionnez **SAML - Azure AD**

    c. Dans la zone de texte **URL des métadonnées de fédération d'application Azure**, collez la valeur de l'**URL des métadonnées de fédération d'application** que vous avez copiée à partir du portail Azure.

    d. Cliquez sur **Enregistrer**.

    >[!NOTE]
    >Pour plus d'informations, consultez l'[article de support MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

### <a name="creating-a-myworkdrive-test-user"></a>Création d'un utilisateur de test MyWorkDrive

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans MyWorkDrive. Collaborez avec l’ [équipe du support technique MyWorkDrive](mailto:support@myworkdrive.com) pour ajouter les utilisateurs dans la plateforme MyWorkDrive. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à MyWorkDrive.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **MyWorkDrive**.

    ![Configurer l'authentification unique](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette MyWorkDrive dans le volet d’accès, vous devez être connecté automatiquement à votre application MyWorkDrive.
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
