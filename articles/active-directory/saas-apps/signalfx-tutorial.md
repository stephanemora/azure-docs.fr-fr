---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec SignalFx | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea81f0046d7f73d845ed49325a3d621e6b7735e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443281"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec SignalFx

Ce tutoriel explique comment intégrer SignalFx avec Azure Active Directory (Azure AD). Lorsque vous intégrez SignalFx avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SignalFx.
* Permettre à vos utilisateurs de se connecter automatiquement à SignalFx avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Conditions préalables requises

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement SignalFx pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* SignalFx prend en charge l’authentification unique lancée par le **fournisseur d’identité**
* SignalFx prend en charge le provisionnement d’utilisateurs **juste-à-temps**

## <a name="adding-signalfx-from-the-gallery"></a>Ajout de SignalFx à partir de la galerie

Pour configurer l’intégration de SignalFx à Azure AD, vous devez ajouter SignalFx à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SignalFx** dans la zone de recherche.
1. Sélectionnez **SignalFx** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-signalfx"></a>Configurer et tester l’authentification unique Azure AD pour SignalFx

Configurez et testez l’authentification unique Azure AD avec SignalFx à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SignalFx associé.

Pour configurer et tester l'authentification unique Azure AD avec SignalFx, suivez les indications des modules ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique SignalFx](#configure-signalfx-sso)** – pour configurer les paramètres d’authentification unique côté application.
    * **[Créer un utilisateur de test SignalFx](#create-signalfx-test-user)** – pour avoir un équivalent de B.Simon dans SignalFx qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **SignalFx**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL : `https://api.signalfx.com/v1/saml/metadata`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > La valeur ci-dessus n’est pas une valeur réelle. Vous mettez à jour la valeur avec l’URL de réponse réelle. La procédure est expliquée plus loin dans le didacticiel.

1. L’application SignalFx attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application SignalFx s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Name |  Attribut source|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer SignalFx**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SignalFx.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SignalFx**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-signalfx-sso"></a>Configurer l’authentification unique SignalFx

1. Connectez-vous à votre site d’entreprise SignalFx en tant qu’administrateur.

1. Dans SignalFx, en haut de l’écran, cliquez sur **Intégrations** pour ouvrir la page correspondante.

    ![Intégration de SignalFx](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Cliquez sur la vignette **Azure Active Directory** sous la section **Services de connexion**.

    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Cliquez sur **NOUVELLE INTÉGRATION** puis, sous l’onglet **INSTALLER**, procédez comme suit :

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Dans la zone de texte **Nom**, saisissez un nouveau nom d’intégration, par exemple **NomDeNotreOrg SAML SSO**.

    b. Copiez la valeur de l’**ID d’intégration** et ajoutez-la à l’**URL de réponse** à la place de `<integration ID>` dans la zone de texte **URL de réponse** de la section **Configuration SAML de base** du portail Azure.

    c. Cliquez sur **Charger le fichier** pour charger le **certificat encodé en Base64** qui a été téléchargé à partir du portail Azure dans la zone de texte **Certificat**.

    d. Dans la zone de texte **Issuer URL** (URL de l’émetteur), collez la valeur de l’**identifiant Azure AD** que vous avez copiée dans le portail Azure.

    e. Dans la zone de texte **Metadata URL** (URL des métadonnées), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    f. Cliquez sur **Enregistrer**.

### <a name="create-signalfx-test-user"></a>Créer un utilisateur de test SignalFx

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SignalFx. SignalFx prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à SignalFx s’il n’existe pas déjà.

Lorsqu’un utilisateur se connecte pour la première fois à SignalFx à partir de la SSO SAML, [l’équipe de support SignalFx](mailto:kmazzola@signalfx.com) lui envoie un e-mail contenant un lien sur lequel il doit cliquer pour s’authentifier. Cette procédure n’intervient que lors de la première connexion de l’utilisateur ; les tentatives de connexion suivantes ne nécessitent aucune validation par e-mail.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support SignalFx](mailto:kmazzola@signalfx.com).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SignalFx dans le volet d’accès, vous devez être connecté automatiquement à l’application SignalFx pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer SignalFx avec Azure AD](https://aad.portal.azure.com/)