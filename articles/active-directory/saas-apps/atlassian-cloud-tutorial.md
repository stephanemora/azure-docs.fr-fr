---
title: 'Didacticiel : Intégration d’Azure Active Directory dans Atlassian Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 7609cea0d16a52a927f87ee9ab6d4445bfc2eb20
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228922"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Didacticiel : Intégration d’Azure Active Directory dans Atlassian Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Atlassian Cloud à Azure Active Directory (Azure AD).

L’intégration d’Atlassian Cloud dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Atlassian Cloud.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Atlassian Cloud (authentification unique) avec leur compte Azure AD.
- Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour plus d’informations sur l’intégration d’applications SaaS (software as a service) à Azure AD, consultez la page [Qu’est-ce que l’accès aux applications et l’authentification unique à Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Atlassian Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Pour pouvoir activer l’authentification unique SAML (Security Assertion Markup Language) pour les produits Atlassian Cloud, vous devez configurer Identity Manager. Cliquez [ici]( https://www.atlassian.com/enterprise/cloud/identity-manager) pour en savoir plus sur Identity Manager.

> [!NOTE]
> Nous déconseillons d’utiliser un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

* Ajout d’Atlassian Cloud à partir de la galerie
* Configuration et test de l’authentification unique Azure AD

## <a name="add-atlassian-cloud-from-the-gallery"></a>Ajouter Atlassian Cloud à partir de la galerie
Pour configurer l’intégration d’Atlassian Cloud à Azure AD, ajoutez Atlassian Cloud à votre liste d’applications SaaS gérées sur la galerie, en procédant de la façon suivante :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Volet Applications d’entreprise][2]
    
3. Pour ajouter une application, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Atlassian Cloud** ; dans la liste des résultats, sélectionnez **Atlassian Cloud**, puis **Ajouter**.

    ![Atlassian Cloud dans la liste des résultats](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD à Atlassian Cloud, avec un utilisateur de test nommé *Britta Simon*.

Pour que l’authentification unique fonctionne, Azure AD doit identifier l’utilisateur Atlassian Cloud et son équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé sur Atlassian Cloud.

Pour établir ce lien, affectez au *Nom d’utilisateur* d’Atlassian Cloud la valeur affectée au *Nom d’utilisateur* d’Azure AD.

Pour configurer et tester l’authentification unique Azure AD à Atlassian Cloud, vous devez exécuter les blocs de construction des sections suivantes.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Atlassian Cloud.

Pour configurer l’authentification unique Azure AD à Atlassian Cloud, suivez les étapes ci-dessous :

1. Sur le volet d’intégration d’applications **Atlassian Cloud** du Portail Azure, sélectionnez **Authentification unique**.

    ![Configurer le lien d’authentification unique][4]

2. Dans la zone **Mode d’authentification unique** de la fenêtre **Authentification unique**, sélectionnez **Authentification SAML**.

    ![Fenêtre Authentification unique](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Pour configurer l’application en mode initié par IDP, suivez les étapes ci-dessous sous **Domaine et URL Atlassian Cloud** :

    ![Informations d’authentification unique dans Domaine et URL Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. Dans la zone **Identificateur**, tapez **`https://auth.atlassian.com/saml/<unique ID>`**.
    
    b. Dans la zone **URL de réponse**, tapez : **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`**.

    c. Dans la zone **État de relais**, tapez une URL suivant cette syntaxe : **`https://<instancename>.atlassian.net`**.

4. Pour configurer l’application en mode initié par SP, sélectionnez **Afficher les paramètres d’URL avancés**, puis, dans la zone **URL de connexion**, tapez une URL suivant cette syntaxe : **`https://<instancename>.atlassian.net`**.

    ![Informations d’authentification unique dans Domaine et URL Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > Les valeurs ci-dessus ne sont pas réelles. Remplacez-les par les valeurs réelles de l’identificateur, de l’URL de réponse et de l’URL d’authentification. Celles-ci sont accessibles sur l’écran de configuration SAML d’Atlassian Cloud. Nous les expliquerons dans la suite du didacticiel.

5. Sous **Certificat de signature SAML**, sélectionnez **Certificat (Base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. L’application Atlassian Cloud attend les assertions SAML dans un certain format, ce qui oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. 

    Par défaut, la valeur **Identificateur d’utilisateur** est associée à user.userprincipalname. Modifiez cette valeur de façon à la faire correspondre à user.mail. Vous pouvez également choisir une autre valeur adaptée à la configuration de votre organisation, mais, dans la plupart des cas, la messagerie électronique devrait fonctionner.

    ![Lien Téléchargement de certificat](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la fenêtre Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

8. Pour ouvrir la fenêtre **Configurer l’authentification**, sélectionnez **Configurer Atlassian Cloud** dans la section **Configuration d’Atlassian Cloud**.

9. Dans la section **Référence rapide**, copiez **l’ID d’entité SAML** et **l’URL du service d’authentification unique SAML**.

    ![Configuration d’Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. Pour configurer l’authentification unique dans votre application, connectez-vous au portail Atlassian avec des informations d’identification d’administrateur.

11. Vous devez vérifier votre domaine avant de passer à la configuration de l’authentification unique. Pour plus d’informations, consultez le document [Domain verification(Vérification de domaine) d’Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

12. Dans le volet gauche, sélectionnez **Authentification unique SAML**. Si ce n’est pas déjà fait, abonnez-vous à Atlassian Identity Manager.

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. Dans la fenêtre **Ajouter une configuration SAML**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Dans la zone **ID d’entité du fournisseur d’identité**, collez l’ID d’entité SAML que vous avez copié sur le Portail Azure.

    b. Dans la zone **URL d’authentification unique du fournisseur d’identité**, collez l’URL du service d’authentification unique SAML que vous avez copiée sur le Portail Azure.

    c. Ouvrez le certificat téléchargé sur le Portail Azure dans un fichier .txt, copiez la valeur (sans les lignes *Begin Certificate* et *End Certificate*), puis collez-la dans la zone **Certificat X509 public**.
    
    d. Sélectionnez **Enregistrer la configuration**.
     
14. Pour vérifier que les URL configurées sont correctes, mettez à jour les paramètres d’Azure AD en suivant les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Dans la fenêtre SAML, copiez **l’ID d’identité SP**. Ensuite, collez-le dans la zone **Identificateur** du Portail Azure, sous **Domaine et URL Atlassian Cloud**.
    
    b. Dans la fenêtre SAML, copiez **l’URL Assertion Consumer Service SP**. Ensuite, collez-le dans la zone **URL de réponse** du Portail Azure, sous **Domaine et URL Atlassian Cloud**. L’URL d’authentification est l’URL locataire de votre Atlassian Cloud.

    > [!NOTE]
    > Si vous êtes déjà client, après avoir mis à jour les valeurs **ID d’identité SP** et **URL Assertion Consumer Service SP** sur le Portail Azure, sélectionnez **Oui, mettre à jour la configuration**. Si vous êtes un nouveau client, vous pouvez ignorer cette étape.
    
15. Sur le Portail Azure, sélectionnez **Enregistrer**.

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure en procédant comme suit :

   ![Créer un utilisateur de test Azure AD][100]

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/atlassian-cloud-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, sélectionnez **Utilisateurs et groupes** > **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/atlassian-cloud-tutorial/create_aaduser_02.png)

3. Dans la fenêtre **Tous les utilisateurs**, sélectionnez **Ajouter**.

    ![Bouton Ajouter](./media/atlassian-cloud-tutorial/create_aaduser_03.png)

4. Dans la fenêtre **Utilisateur**, suivez les étapes ci-dessous :

    ![Fenêtre Utilisateur](./media/atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.

### <a name="create-an-atlassian-cloud-test-user"></a>Créer un utilisateur de test Atlassian Cloud

Pour permettre aux utilisateurs d’Azure AD de se connecter à Atlassian Cloud, approvisionnez manuellement les comptes d’utilisateur dans Atlassian Cloud en suivant les étapes ci-dessous :

1. Dans le volet **Administration**, sélectionnez **Utilisateurs**.

    ![Lien Utilisateurs Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Pour créer un utilisateur dans Atlassian Cloud, sélectionnez **Inviter un utilisateur**.

    ![Créer un utilisateur Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Dans la zone **Adresse e-mail**, entrez l’adresse e-mail de l’utilisateur, puis affectez l’accès à l’application.

    ![Créer un utilisateur Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Pour envoyer une invitation par e-mail à l’utilisateur, sélectionnez **Inviter les utilisateurs**. Une invitation est envoyée par e-mail à l’utilisateur ; dès qu’il aura accepté l’invitation, il sera actif dans le système.

>[!NOTE]
>Vous pouvez également créer des utilisateurs en bloc en cliquant sur le bouton **Créer en bloc** dans la section **Utilisateurs**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Atlassian Cloud. Pour ce faire, procédez comme suit :

![Attribuer le rôle utilisateur][200]

1. Sur le Portail Azure, ouvrez la vue **Applications**, accédez à la vue Répertoire, puis sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste **Applications**, sélectionnez **Atlassian Cloud**.

    ![Lien Atlassian Cloud dans la liste des applications](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. Dans le volet gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez **Ajouter** puis, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la liste **Utilisateurs** de la fenêtre **Utilisateurs et groupes**, sélectionnez **Britta Simon**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Sélectionner**.

7. Dans la fenêtre **Ajouter une affectation**, sélectionnez **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Après avoir sélectionné la vignette **Atlassian Cloud** dans le volet d’accès, vous devriez être automatiquement connecté à votre application Atlassian Cloud.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/atlassian-cloud-tutorial/tutorial_general_203.png
