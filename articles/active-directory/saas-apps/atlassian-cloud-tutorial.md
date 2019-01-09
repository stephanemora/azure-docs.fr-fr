---
title: 'Tutoriel : Intégration d’Azure Active Directory dans Atlassian Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.openlocfilehash: 517c92336b8c5c6827717d4c2287c41eaaa14b55
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53810525"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Didacticiel : Intégration d’Azure Active Directory dans Atlassian Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Atlassian Cloud à Azure Active Directory (Azure AD).
L’intégration d’Atlassian Cloud dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Atlassian Cloud.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Atlassian Cloud (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Atlassian Cloud, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Atlassian Cloud pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Atlassian Cloud prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identités**

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Ajout d’Atlassian Cloud à partir de la galerie

Pour configurer l’intégration d’Atlassian Cloud à Azure AD, vous devez ajouter Atlassian Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Atlassian Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Atlassian Cloud**, sélectionnez **Atlassian Cloud** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Atlassian Cloud dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Atlassian Cloud, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur associé dans Atlassian Cloud doit être établie.

Pour configurer et tester l’authentification unique avec Azure AD avec Atlassian Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Atlassian Cloud](#configure-atlassian-cloud-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Atlassian Cloud](#create-atlassian-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Atlassian Cloud lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Atlassian Cloud, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Atlassian Cloud**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL [Nom de l’application]](common/idp-relay.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://auth.atlassian.com/saml/<unique ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Cliquez sur **Définir des URL supplémentaires**.

    d. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Les valeurs ci-dessus ne sont pas réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Ces valeurs réelles sont accessibles dans l’écran de configuration SAML pour Atlassian Cloud, comme nous le verrons plus loin dans ce tutoriel.

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL [Nom de l’application]](common/both-signonurl.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<instancename>.atlassian.net`

    > [!NOTE]
    > La valeur d’URL de connexion ci-dessus n’est pas une valeur réelle. Remplacez cette valeur par l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support technique Atlassian Cloud](https://support.atlassian.com/).

6. L’application Atlassian Cloud attend les assertions SAML dans un certain format, ce qui oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML.

    Par défaut, la valeur **Identificateur d’utilisateur** est associée à user.userprincipalname. Modifiez cette valeur de façon à la faire correspondre à user.mail. Vous pouvez également choisir une autre valeur adaptée à la configuration de votre organisation, mais, dans la plupart des cas, la messagerie électronique devrait fonctionner. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, procédez comme suit :

    a. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/atlassian-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/atlassian-cloud-tutorial/tutorial_usermailedit.png)

    b. Dans la liste **Attribut source**, sélectionnez **user.mail**.

    c. Cliquez sur **Enregistrer**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

9. Dans la section **Set up Atlassian Cloud** (Configurer Atlassian Cloud), copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-atlassian-cloud-single-sign-on"></a>Configurer l’authentification unique Atlassian Cloud

1. Pour configurer l’authentification unique dans votre application, connectez-vous au portail Atlassian avec des informations d’identification d’administrateur.

2. Vous devez vérifier votre domaine avant de passer à la configuration de l’authentification unique. Pour plus d’informations, consultez le document [Domain verification(Vérification de domaine) d’Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

3. Dans le volet gauche, sélectionnez **Authentification unique SAML**. Si ce n’est pas déjà fait, abonnez-vous à Atlassian Identity Manager.

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. Dans la fenêtre **Ajouter une configuration SAML**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Dans la zone **ID d’entité du fournisseur d’identité**, collez l’ID d’entité SAML que vous avez copié sur le Portail Azure.

    b. Dans la zone **URL d’authentification unique du fournisseur d’identité**, collez l’URL du service d’authentification unique SAML que vous avez copiée sur le Portail Azure.

    c. Ouvrez le certificat téléchargé sur le Portail Azure dans un fichier .txt, copiez la valeur (sans les lignes *Begin Certificate* et *End Certificate*), puis collez-la dans la zone **Certificat X509 public**.

    d. Cliquez sur **Enregistrer la configuration**.

5. Pour vérifier que les URL configurées sont correctes, mettez à jour les paramètres d’Azure AD en suivant les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Dans la fenêtre SAML, copiez **l’ID d’identité SP**. Ensuite, collez-le dans la zone **Identificateur** du Portail Azure, sous **Domaine et URL Atlassian Cloud**.

    b. Dans la fenêtre SAML, copiez **l’URL Assertion Consumer Service SP**. Ensuite, collez-le dans la zone **URL de réponse** du Portail Azure, sous **Domaine et URL Atlassian Cloud**. L’URL d’authentification est l’URL locataire de votre Atlassian Cloud.

    > [!NOTE]
    > Si vous êtes déjà client, après avoir mis à jour les valeurs **ID d’identité SP** et **URL Assertion Consumer Service SP** sur le Portail Azure, sélectionnez **Oui, mettre à jour la configuration**. Si vous êtes un nouveau client, vous pouvez ignorer cette étape.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Atlassian Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Atlassian Cloud** (Atlassian Cloud).

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Atlassian Cloud** (Atlassian Cloud).

    ![Lien Atlassian Cloud dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-atlassian-cloud-test-user"></a>Créer un utilisateur de test Atlassian Cloud

Pour permettre aux utilisateurs d’Azure AD de se connecter à Atlassian Cloud, approvisionnez manuellement les comptes d’utilisateur dans Atlassian Cloud en suivant les étapes ci-dessous :

1. Dans le volet **Administration**, sélectionnez **Utilisateurs**.

    ![Lien Utilisateurs Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Pour créer un utilisateur dans Atlassian Cloud, sélectionnez **Inviter un utilisateur**.

    ![Créer un utilisateur Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Dans la zone **Adresse e-mail**, entrez l’adresse e-mail de l’utilisateur, puis affectez l’accès à l’application.

    ![Créer un utilisateur Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Pour envoyer une invitation par e-mail à l’utilisateur, sélectionnez **Inviter les utilisateurs**. Une invitation est envoyée par e-mail à l’utilisateur ; dès qu’il aura accepté l’invitation, il sera actif dans le système.

> [!NOTE]
> Vous pouvez également créer des utilisateurs en bloc en cliquant sur le bouton **Créer en bloc** dans la section **Utilisateurs**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Atlassian Cloud dans le volet d’accès, vous devez être connecté automatiquement à l’application Atlassian Cloud pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   