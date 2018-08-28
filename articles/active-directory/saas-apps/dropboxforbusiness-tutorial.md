---
title: "Didacticiel : Intégration d'Azure Active Directory à Dropbox for Business | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: jeedes
ms.openlocfilehash: eadf6724891d348c2ea3654bcf19ef0d74078049
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143833"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Didacticiel : Intégration d'Azure Active Directory à Dropbox for Business

Dans ce didacticiel, vous allez apprendre à intégrer Dropbox for Business à Azure Active Directory (Azure AD).

L’intégration de Dropbox for Business à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Dropbox for Business.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Dropbox for Business (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Dropbox for Business, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Dropbox for Business pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Dropbox for Business à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Ajout de Dropbox for Business à partir de la galerie

Pour configurer l’intégration de Dropbox for Business à Azure AD, vous devez ajouter Dropbox for Business depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Dropbox for Business à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Dropbox for Business**, sélectionnez **Dropbox for Business** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Dropbox for Business dans la liste des résultats](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Dropbox for Business, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Dropbox for Business équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Dropbox for Business associé doit être établi.

Dans Dropbox for Business, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Dropbox for Business, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Dropbox for Business](#create-a-dropbox-for-business-test-user)** pour avoir un équivalent de Britta Simon dans Dropbox for Business lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Dropbox for Business.

**Pour configurer l’authentification unique Azure AD avec Dropbox for Business, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Dropbox for Business**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. Dans la section **Domaine et URL Dropbox for Business**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans la section Domaine et URL Dropbox for Business](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.dropbox.com/sso/<id>`

    b. Dans la zone de texte **Identificateur**, tapez une valeur : `Dropbox`

    > [!NOTE]
    > La valeur URL de connexion ci-dessus n’est pas une valeur réelle. Vous mettrez à jour la valeur avec l’URL de connexion réelle. La procédure est expliquée plus loin dans le didacticiel.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Dropbox for Business**, cliquez sur **Configurer Dropbox for Business** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l **’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Dropbox for Business](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Pour configurer l’authentification unique du côté de **Dropbox for Business**, accédez et connectez-vous à votre locataire Dropbox for Business.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/ic769509.png "Configurer l’authentification unique")

8. Cliquez sur l’**Icône Utilisateur** et sélectionnez l’onglet **Paramètres**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure1.png "Configurer l’authentification unique")

9. Dans le volet de navigation gauche, cliquez sur **Console d’administration**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure2.png "Configurer l’authentification unique")

10. Dans la **Console d’administration**, cliquez sur **Paramètres** dans le volet de navigation gauche.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure3.png "Configurer l’authentification unique")

11. Sélectionnez **Authentification unique** sous la section **Authentification**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure4.png "Configurer l’authentification unique")

12. Dans la section **Authentification unique**, procédez comme suit :  

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure5.png "Configurer l’authentification unique")

    a. Sélectionnez **Requis** en tant qu’option dans la liste déroulante de l’**Authentification unique**.

    b. Cliquez sur **Ajouter une URL de connexion**, puis sur la zone de texte **URL de connexion du fournisseur d’identité**, collez la valeur **URL du service d’authentification unique SAML** copiée dans le portail Azure, et sélectionnez **Terminé**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure6.png "Configurer l’authentification unique")

    c. Cliquez sur **Charger un certificat**, puis cherchez votre **Fichier de certificat codé en Base64** que vous avez téléchargé à partir du portail Azure.

    d. Cliquez sur **Copier le lien** et collez la valeur copiée dans la zone de texte de l’**URL de connexion** de la section **Domaine et URL Dropbox for Business** du portail Azure.

    e. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/dropboxforbusiness-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/dropboxforbusiness-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/dropboxforbusiness-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Créer un utilisateur de test Dropbox for Business

Dans cette section, un utilisateur appelé Britta Simon est créé dans Dropbox for Business. Dropbox for Business prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Dropbox for Business, un nouveau est créé lorsque vous tentez d’accéder à Dropbox for Business.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe de prise en charge des clients Dropbox for Business](https://www.dropbox.com/business/contact). 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Dropbox for Business.

![Attribuer le rôle utilisateur][200] 

**Pour assigner des utilisateurs à Dropbox for Business, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Dropbox for Business**.

    ![Lien Dropbox for Business dans la liste Applications](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Dropbox for Business dans le volet d’accès, vous devez obtenir la page de connexion de votre application Dropbox for Business.
 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/dropboxforbusiness-tutorial/tutorial_general_203.png

