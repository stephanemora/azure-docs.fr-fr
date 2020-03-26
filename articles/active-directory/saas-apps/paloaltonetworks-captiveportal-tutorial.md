---
title: 'Tutoriel : Intégration d’Azure Active Directory à Palo Alto Networks Captive Portal | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Palo Alto Networks Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160169"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutoriel : Intégration d’Azure Active Directory à Palo Alto Networks Captive Portal

Dans ce tutoriel, vous découvrez comment intégrer Palo Alto Networks Captive Portal à Azure Active Directory (Azure AD).

Vous bénéficiez des avantages suivants quand vous intégrez Palo Alto Networks Captive Portal à Azure AD :

* Dans Azure AD, vous pouvez contrôler qui a accès à Palo Alto Networks Captive Portal.
* Vous pouvez connecter automatiquement des utilisateurs dans Palo Alto Networks Captive Portal (par le biais de l’authentification unique) à l’aide de comptes d’utilisateur Azure AD.
* Vous pouvez gérer vos comptes en un seul et même endroit : le Portail Azure.

Pour en savoir plus sur l’intégration d’applications software as a service (SaaS) à Azure AD, consultez l’article [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

Pour intégrer Azure AD à Palo Alto Networks Captive Portal, vous avez besoin des éléments suivants :

* Un abonnement Azure Active Directory. Si vous n’avez pas Azure AD, vous pouvez [vous inscrire pour un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Palo Alto Networks Captive Portal pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

Palo Alto Networks Captive Portal prend en charge les scénarios suivants :

* **Authentification unique démarrée par le fournisseur d’identité**
* **Attribution d’utilisateurs juste-à-temps**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Ajouter Palo Alto Networks Captive Portal depuis la galerie

Pour commencer, dans la galerie, ajoutez Palo Alto Networks Captive Portal à votre liste d’applications SaaS gérées :

1. Dans le menu gauche du [Portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Option Applications d’entreprise dans le menu](common/enterprise-applications.png)

3. Sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Palo Alto Networks Captive Portal**. Dans les résultats de la recherche, sélectionnez **Palo Alto Networks - Captive Portal**, puis sélectionnez **Ajouter**.

     ![Palo Alto Networks - Captive Portal dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Vous configurez et vous testez l’authentification unique Azure AD avec Palo Alto Networks Captive Portal sur un utilisateur de test nommé *Britta Simon*. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Palo Alto Networks Captive Portal équivalent. 

Pour configurer et tester l’authentification unique Azure AD avec Palo Alto Networks Captive Portal, effectuez les tâches suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)**  : autorisez l’utilisateur à utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Palo Alto Networks Captive Portal](#configure-palo-alto-networks-captive-portal-single-sign-on)**  : configurez les paramètres d’authentification unique dans l’application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)**  : testez l’authentification unique Azure AD avec l’utilisateur *Britta Simon*.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)**  : configurez Britta Simon afin qu’elle puisse utiliser l’authentification unique Azure AD.
5. **Créer un utilisateur de test Palo Alto Networks Captive Portal** : créez un utilisateur *Britta Simon* équivalent dans Palo Alto Networks Captive Portal lié à l’utilisateur Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)**  : vérifiez que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Tout d’abord, activez l’authentification unique Azure AD dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Palo Alto Networks - Captive Portal**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans le volet **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez l’icône **Modifier** en forme de crayon.

    ![Icône Modifier en forme de crayon](common/edit-urls.png)

4. Dans le volet **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Volet Configuration SAML de base pour Palo Alto Networks Captive Portal](common/idp-intiated.png)

   1. Pour **Identificateur**, entrez une URL au format `https://<customer_firewall_host_name>/SAML20/SP`.

   2. Pour **URL de réponse**, entrez une URL au format `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Mettez à jour les valeurs d’espace réservé à cette étape avec les URL d’identificateur et de réponse réelles. Pour obtenir les valeurs réelles, contactez l’[équipe de support client Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. Dans la section **Certificat de signature SAML**, en regard de **XML de métadonnées de fédération**, sélectionnez **Télécharger**. Enregistrez le fichier téléchargé sur votre ordinateur.

    ![Lien de téléchargement du XML de métadonnées de fédération](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Configurer l’authentification unique Palo Alto Networks Captive Portal

Ensuite, configurez l’authentification unique dans Palo Alto Networks Captive Portal :

1. Dans une autre fenêtre de navigateur, connectez-vous au site web Palo Alto Networks en tant qu’administrateur.

2. Sélectionnez l’onglet **Appareil**.

    ![Onglet Device (Appareil) sur le site web Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Dans le menu, sélectionnez **SAML Identity Provider** (Fournisseur d’identité SAML), puis sélectionnez **Import**.

    ![Bouton Import](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Dans la boîte de dialogue **SAML Identity Provider Server Profile Import** (Importer le profil de serveur du fournisseur d’identité SAML), effectuez les étapes suivantes :

    ![Configurer l’authentification unique Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Pour **Profile Name** (Nom du profil), entrez un nom, par exemple, **AzureAD-CaptivePortal**.
    
    2. En regard d’**Identity Provider Metadata** (Métadonnées du fournisseur d’identité), sélectionnez **Browse** (Parcourir). Sélectionnez le fichier metadata.xml que vous avez téléchargé dans le portail Azure.
    
    3. Sélectionnez **OK**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

Ensuite, créez un utilisateur de test nommé *Britta Simon* dans le portail Azure :

1. Dans le portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur**.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans le volet **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    1. Pour **Nom**, entrez **BrittaSimon**.
  
    2. Pour **Nom d’utilisateur**, entrez **BrittaSimon\@\<domaine_de_votre_entreprise\>** . Par exemple, **BrittaSimon\@contoso.com**.

    3. Pour **Mot de passe**, entrez un mot de passe. Nous vous recommandons de conserver un enregistrement du mot de passe que vous entrez. Vous pouvez cocher la case **Afficher le mot de passe** pour afficher le mot de passe.

    4. Sélectionnez **Create** (Créer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Ensuite, accordez l’accès à Palo Alto Networks Captive Portal afin que Britta Simon puisse utiliser l’authentification unique Azure :

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, entrez **Palo Alto Networks - Captive Portal**, puis sélectionnez l’application.

    ![Lien Palo Alto Networks - Captive Portal dans la liste des applications](common/all-applications.png)

3. Dans le menu, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la liste **Utilisateurs** du volet **Utilisateurs et groupes**, sélectionnez **Britta Simon**. Sélectionnez **Sélectionner**.

6. Pour ajouter une valeur de rôle à l’assertion SAML, dans le volet **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur. Sélectionnez **Sélectionner**.

7. Dans le volet **Ajouter une attribution**, sélectionnez **Attribuer**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Créer un utilisateur de test Palo Alto Networks Captive Portal

Ensuite, créez un utilisateur nommé *Britta Simon* dans Palo Alto Networks Captive Portal. Palo Alto Networks Captive Portal prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez pas besoin d’effectuer de tâches dans cette section. S’il n’existe pas encore d’utilisateur dans Palo Alto Networks Captive Portal, il en est créé un après l’authentification.

> [!NOTE]
> Si vous souhaitez créer un utilisateur manuellement, contactez l’[équipe de support client de Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Palo Alto Networks Captive Portal est installé derrière le pare-feu sur une machine virtuelle Windows. Pour tester l’authentification unique dans Palo Alto Networks Captive Portal, connectez-vous à la machine virtuelle Windows à l’aide du protocole RDP (Remote Desktop Protocol). Dans la session RDP, ouvrez un navigateur et accédez à n’importe quel site web. L’URL d’authentification unique s’ouvre et vous êtes invité à vous authentifier. Une fois l’authentification terminée, vous pouvez accéder à des sites web.

## <a name="additional-resources"></a>Ressources supplémentaires

Pour en savoir plus, consultez les articles suivants :

- [Tutoriels sur l’intégration d’applications SaaS à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Accès conditionnel dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

