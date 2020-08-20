---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à Ekarda | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: b1cf45fd57e159993cdb3a677c505911d013122e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544245"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à Ekarda

Dans ce tutoriel, vous allez apprendre à intégrer Ekarda à Azure Active Directory (Azure AD). Quand vous intégrez Ekarda à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Ekarda.
* Permettre à vos utilisateurs de se connecter automatiquement à Ekarda avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Ekarda pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Ekarda prend en charge l’authentification unique initiée par le **fournisseur de services et le fournisseur d’identité**.
* Ekarda prend en charge le provisionnement d’utilisateurs **juste-à-temps**.

* Après avoir configuré Ekarda, vous pouvez appliquer le contrôle de session, qui protège l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ekarda-from-the-gallery"></a>Ajout d’Ekarda à partir de la galerie

Pour configurer l’intégration d’Ekarda à Azure AD, vous devez ajouter Ekarda à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Ekarda** dans la zone de recherche.
1. Sélectionnez **Ekarda** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Configurer et tester l’authentification unique Azure AD pour Ekarda

Configurez et testez l’authentification unique Azure AD avec Ekarda à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Ekarda associé.

Pour configurer et tester l’authentification unique Azure AD avec Ekarda, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Ekarda](#configure-ekarda-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Ekarda](#create-ekarda-test-user)** pour avoir un équivalent de B.Simon dans Ekarda lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Ekarda**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :
    
    a. Cliquez sur **Charger un fichier de métadonnées**.

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    c. Une fois le fichier de métadonnées correctement chargé, les valeurs **Identificateur** et **URL de réponse** sont renseignées automatiquement dans les zones de texte de la section Ekarda :

    > [!Note]
    > Si les valeurs **Identificateur** et **URL de réponse** ne sont pas automatiquement renseignées, renseignez-les manuellement en fonction de vos besoins.

1. Si vous ne disposez pas d’un **fichier de métadonnées du fournisseur de services**, dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support client Ekarda](mailto:contact@ekarda.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier le **Certificat (Base64)** , puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Ekarda**, copiez la ou les URL appropriées selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Ekarda.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Ekarda**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-ekarda-sso"></a>Configurer l’authentification unique Ekarda

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Ekarda en tant qu’administrateur.

1. Cliquez sur **Administrateur** -> **Mon compte**.

    ![Configuration d’Ekarda](./media/ekarda-tutorial/ekarda.png)    

1. Au bas de la page, vous trouverez la section **PARAMÈTRES SAML** dans laquelle vous allez configurer cette intégration SAML.
1. Dans la page suivante, effectuez les étapes suivantes :

    ![Configuration d’Ekarda](./media/ekarda-tutorial/ekarda1.png)

    a. Cliquez sur le lien **Métadonnées du fournisseur de services** et enregistrez-les en tant que fichier sur votre ordinateur.

    b. Cochez la case **Activer SAML**.

    c. Dans la zone de texte **ID d’entité de fournisseur d’identité**, collez la valeur **ID d’entité** que vous avez copiée sur le portail Azure.

    d. Dans la zone de texte **URL de connexion IDP**, collez la valeur d’**URL de connexion** que vous avez copiée sur le portail Azure.

    e. Dans la zone de texte **URL de déconnexion IDP**, collez la valeur d’**URL de connexion** que vous avez copiée sur le portail Azure

    f. Ouvrez le **certificat (base64)** téléchargé à partir du portail Azure dans le Bloc-notes et collez le contenu dans la zone de texte **Certificat IDP x509**.

    g. Sélectionnez **Activer SLO** dans la section **OPTIONS**.

    h. Cliquez sur **Mettre à jour**.

### <a name="create-ekarda-test-user"></a>Créer un utilisateur de test Ekarda

Dans cette section, un utilisateur appelé B.Simon est créé dans Ekarda. Ekarda prend en charge le provisionnement d’utilisateurs juste-à-temps, lequel est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Ekarda, il en est créé un après l’authentification.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Ekarda dans le volet d’accès, vous devez être connecté automatiquement à l’application Ekarda pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Ekarda avec Azure AD](https://aad.portal.azure.com/)

- Utilisez la [solution eCard d’entreprise d’Ekarda](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) pour permettre à un nombre quelconque d’employés d’envoyer des eCards personnalisées avec le logo de votre entreprise à leurs clients et collègues. Apprenez-en davantage sur le provisionnement d’[Ekarda en tant que solution d’authentification unique](https://support.ekarda.com/#SSO-Implementation).

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Guide pratique pour protéger Ekarda avec une visibilité et des contrôles avancés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
