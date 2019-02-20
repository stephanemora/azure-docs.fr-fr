---
title: 'Tutoriel : Intégration d’Azure Active Directory à Hornbill | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Hornbill.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 173061e4-ac1d-458f-bb9b-e9a2493aab0e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f23a1520175827f775553e1ba949c62567cf83
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201922"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>Didacticiel : Intégration d’Azure Active Directory à Hornbill

Dans ce didacticiel, vous allez apprendre à intégrer Hornbill à Azure Active Directory (Azure AD).

L’intégration de Hornbill dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Hornbill.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Hornbill (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Hornbill, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Hornbill pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Hornbill à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-hornbill-from-the-gallery"></a>Ajout de Hornbill à partir de la galerie
Pour configurer l’intégration de Hornbill à Azure AD, vous devez ajouter Hornbill depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Hornbill à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Hornbill**, sélectionnez **Hornbill** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Hornbill dans la liste des résultats](./media/hornbill-tutorial/tutorial_hornbill_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Hornbill avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Hornbill équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur associé dans Hornbill doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Hornbill, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test Hornbill](#create-a-hornbill-test-user)** pour avoir un équivalent de Britta Simon dans Hornbill lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Hornbill.

**Pour configurer l'authentification unique Azure AD avec Hornbill, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Hornbill**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/hornbill-tutorial/tutorial_hornbill_samlbase.png)

3. Dans la section **Domaine et URL Hornbill**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Hornbill](./media/hornbill-tutorial/tutorial_hornbill_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support aux clients Hornbill](https://www.hornbill.com/support/?request/). 

4. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/hornbill-tutorial/tutorial_hornbill_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/hornbill-tutorial/tutorial_general_400.png)
 
6. Dans une autre fenêtre de navigateur web, connectez-vous à Hornbill comme Administrateur de la sécurité.

7. Sur la page d’accueil, cliquez sur **Système**.

    ![Système Hornbill](./media/hornbill-tutorial/tutorial_hornbill_system.png)

8. Naviguez vers **Sécurité**.

    ![Sécurité Hornbill](./media/hornbill-tutorial/tutorial_hornbill_security.png)

9. Cliquez sur **Profils SSO**.

    ![Hornbill SSO](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

10. À droite de la page, cliquez sur **Ajouter un logo**.

    ![Ajout Hornbill](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

11. Sur la barre **Détails du profil**, cliquez sur **Importer le logo SAML Meta**.

    ![Logo Hornbill](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

12. Dans la fenêtre contextuelle de la zone de texte **URL**, collez l’**URL des métadonnées de fédération de l’application** que vous avez copiée dans le portail Azure, puis cliquez sur **Process** (Traiter).

    ![Traitement Hornbill](./media/hornbill-tutorial/tutorial_hornbill_process.png)

13. Après avoir cliqué sur Process, les valeurs sont remplies automatiquement dans la section **Détails du profil**.

    ![Hornbill page 1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Hornbill page 2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Hornbill page 3](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

14. Cliquez sur **Enregistrer les modifications**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/hornbill-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/hornbill-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/hornbill-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/hornbill-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-hornbill-test-user"></a>Créer un utilisateur de test Hornbill

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Hornbill. Hornbill prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, l’utilisateur est créé lors de la tentative d’accès à Hornbill.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez  [l’équipe du support technique du client Hornbill](https://www.hornbill.com/support/?request/).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Hornbill.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Hornbill, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Hornbill**.

    ![Lien Hornbill dans la liste des applications](./media/hornbill-tutorial/tutorial_hornbill_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Hornbill dans le volet d’accès, vous devez être connecté automatiquement à votre application Hornbill.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hornbill-tutorial/tutorial_general_01.png
[2]: ./media/hornbill-tutorial/tutorial_general_02.png
[3]: ./media/hornbill-tutorial/tutorial_general_03.png
[4]: ./media/hornbill-tutorial/tutorial_general_04.png

[100]: ./media/hornbill-tutorial/tutorial_general_100.png

[200]: ./media/hornbill-tutorial/tutorial_general_200.png
[201]: ./media/hornbill-tutorial/tutorial_general_201.png
[202]: ./media/hornbill-tutorial/tutorial_general_202.png
[203]: ./media/hornbill-tutorial/tutorial_general_203.png

