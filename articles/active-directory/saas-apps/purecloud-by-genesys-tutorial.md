---
title: 'Didacticiel : Intégration de l’authentification unique (SSO) Azure Active Directory à PureCloud by Genesys | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfaa5a4ebb8bc633dc49db08698aec31de9436d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373149"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Didacticiel : Intégration de l’authentification unique (SSO) Azure Active Directory à PureCloud by Genesys

Dans ce tutoriel, vous allez apprendre à intégrer PureCloud by Genesys à Azure Active Directory (Azure AD). Quand vous intégrez PureCloud à Azure AD, vous pouvez :

* Contrôler qui a accès à PureCloud by Genesys dans Azure AD.
* Autoriser les utilisateurs à se connecter automatiquement à PureCloud by Genesys avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement PureCloud by Genesys pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* PureCloud by Genesys prend en charge l’authentification unique lancée par **le fournisseur de services et le fournisseur d’identité**

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Ajout de PureCloud by Genesys à partir de la galerie

Pour configurer l’intégration de PureCloud by Genesys dans Azure AD, vous devez ajouter PureCloud by Genesys à partir de la galerie à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **PureCloud by Genesys** dans la zone de recherche.
1. Sélectionnez **PureCloud by Genesys** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Configurer et tester l’authentification unique Azure AD pour PureCloud by Genesys

Configurez et testez l’authentification unique Azure AD avec PureCloud by Genesys pour un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans PureCloud by Genesys.

Pour configurer et tester l’authentification unique (SSO) Azure AD avec PureCloud by Genesys, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique PureCloud by Genesys](#configure-purecloud-by-genesys-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** pour avoir un équivalent de B.Simon dans PureCloud by Genesys lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Sur le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **PureCloud by Genesys**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL appropriée pour votre région :

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Dans la zone de texte **URL de réponse**, tapez une URL appropriée pour votre région :

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL appropriée pour votre région :
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. Votre application PureCloud by Genesys attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. Outre ceux mentionnés ci-dessus, l’application PureCloud by Genesys s’attend également à ce que quelques attributs de plus, indiqués ci-après, soient transmis dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom | Attribut source|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer PureCloud by Genesys**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à PureCloud by Genesys.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **PureCloud by Genesys**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-purecloud-by-genesys-sso"></a>Configurer l’authentification unique PureCloud by Genesys

1. Dans une autre fenêtre de navigateur web, connectez-vous à PureCloud by Genesys en tant qu’administrateur.

1. Cliquez sur **Admin** (Administrateur) en haut de la fenêtre, puis accédez à **Single Sign-on** (Authentification unique) sous **Integrations** (Intégrations).

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Accédez à l’onglet **ADFS/Azure AD (Premium)** et effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Dans **ADFS Certificate** (Certificat ADFS), cliquez sur **Browse** (Parcourir) pour charger le certificat codé en base 64 que vous avez téléchargé du portail Azure.

    b. Dans la zone de texte **ADFS Issuer URI** (URI de l’émetteur SAML), collez la valeur **Identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Target URI** (URI cible), collez la valeur **URL de connexion** que vous avez copiée du portail Azure.

    d. Pour la valeur **Relying Party Identifier** (Identificateur de partie de confiance), accédez au portail Azure et, dans la page d’intégration de l’application **PureCloud by Genesys**, cliquez sur l’onglet **Propriétés** et copiez la valeur **ID de l’application**. Collez ensuite cette valeur dans la zone de texte **Relying Party Identifier**. 

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Cliquez sur **Enregistrer**.

### <a name="create-purecloud-by-genesys-test-user"></a>Créer un utilisateur de test PureCloud by Genesys

Pour permettre aux utilisateurs Azure AD de se connecter à PureCloud by Genesys, vous devez les provisionner dans PureCloud by Genesys. Dans PureCloud by Genesys, le provisionnement se fait manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à PureCloud by Genesys en tant qu’administrateur.

1. Cliquez sur **Admin** (Administrateur) en haut de la fenêtre, puis accédez à la section **People** (Personnes) sous **People & Permissions** (Personnes & Autorisations).

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Dans la page People, cliquez sur **Add Person** (Ajouter une personne).

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Dans la fenêtre contextuelle **Add People to the Organization** (Ajouter des personnes à l’organisation), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Dans la zone de texte **Full Name** (Nom complet), entrez le nom d’un utilisateur, par exemple **B.simon**.

    b. Dans la zone de texte **E-mail**, tapez l’adresse e-mail de l’utilisateur, par exemple **b.simon\@contoso.com**.

    c. Cliquez sur **Créer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette PureCloud by Genesys dans le volet d’accès, vous devez être connecté automatiquement à l’application PureCloud by Genesys pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer PureCloud by Genesys avec Azure AD](https://aad.portal.azure.com/)