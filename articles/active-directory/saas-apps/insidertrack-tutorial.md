---
title: 'Tutoriel : Intégration d’Azure Active Directory à Insider Track | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Insider Track.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 53b6a928-e8a4-4cf1-9952-50cd3f013b7c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: jeedes
ms.openlocfilehash: e23949ac4957325c3949312dad61071a1f527789
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191106"
---
# <a name="tutorial-azure-active-directory-integration-with-insider-track"></a>Tutoriel : Intégration d’Azure Active Directory à Insider Track

Ce didacticiel vous montrera comment intégrer Insider Track à Azure Active Directory (Azure AD).

L’intégration d’Insider Track dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Insider Track.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Insider Track (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Insider Track, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Insider Track pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Insider Track à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-insider-track-from-the-gallery"></a>Ajout d’Insider Track à partir de la galerie
Pour configurer l’intégration d’Insider Track à Azure AD, vous devez ajouter Insider Track, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Insider Track à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **Insider Track**, sélectionnez **Insider Track** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Insider Track dans la liste des résultats](./media/insidertrack-tutorial/tutorial_insidertrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Insider Track sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur dans Insider Track équivaut à quel utilisateur dans Azure AD. En d’autres termes, vous devez établir une relation de lien entre un utilisateur Azure AD et l’utilisateur Insider Track associé.

Dans Insider Track, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** (Nom d’utilisateur) pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec Insider Track, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Insider Track](#create-an-insider-track-test-user)** pour avoir un équivalent de Britta Simon dans Insider Track qui soit lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Insider Track.

**Pour configurer l’authentification unique Azure AD avec Insider Track, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Insider Track**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/insidertrack-tutorial/tutorial_insidertrack_samlbase.png)

3. Dans la section **Domaine et URL Insider Track**, procédez comme suit :

    ![Informations d’authentification unique Domaine et URL Insider Track](./media/insidertrack-tutorial/tutorial_insidertrack_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>/InsiderTrack.Portal.<companyname>/Sso/`

    > [!NOTE] 
    > La valeur de l’URL de connexion n’est pas réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Contactez [l’équipe de support client Insider Track](https://cytecsolutions.com/contact/) pour obtenir cette valeur.

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/insidertrack-tutorial/tutorial_insidertrack_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/insidertrack-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration d’Insider Track**, cliquez sur **Configurer Insider Track** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration d’Insider Track](./media/insidertrack-tutorial/tutorial_insidertrack_configure.png) 

7. Pour configurer l’authentification unique côté **Insider Track**, vous devez envoyer le **XML des métadonnées téléchargé, l’URL de déconnexion, l’ID d’entité SAML** et l’**URL du service d’authentification unique SAML** à l’[équipe de support d’Insider Track](https://cytecsolutions.com/contact/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/insidertrack-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/insidertrack-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/insidertrack-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/insidertrack-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-insider-track-test-user"></a>Créer un utilisateur Insider Track

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Insider Track. Collaborez avec l’ [équipe du support technique d’Insider Track](https://cytecsolutions.com/contact/)  pour ajouter des utilisateurs à la plateforme d’Insider Track. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Insider Track.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Insider Track, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Insider Track**.

    ![Lien Insider Track dans la liste des applications](./media/insidertrack-tutorial/tutorial_insidertrack_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Insider Track dans le panneau d’accès, vous devriez être connecté automatiquement à votre application Insider Track.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insidertrack-tutorial/tutorial_general_01.png
[2]: ./media/insidertrack-tutorial/tutorial_general_02.png
[3]: ./media/insidertrack-tutorial/tutorial_general_03.png
[4]: ./media/insidertrack-tutorial/tutorial_general_04.png

[100]: ./media/insidertrack-tutorial/tutorial_general_100.png

[200]: ./media/insidertrack-tutorial/tutorial_general_200.png
[201]: ./media/insidertrack-tutorial/tutorial_general_201.png
[202]: ./media/insidertrack-tutorial/tutorial_general_202.png
[203]: ./media/insidertrack-tutorial/tutorial_general_203.png

