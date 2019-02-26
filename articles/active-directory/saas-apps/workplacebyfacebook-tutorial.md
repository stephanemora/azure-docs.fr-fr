---
title: 'Tutoriel : Intégration d’Azure Active Directory à Workplace by Facebook | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e233de6f0909477e5033d2a0104f9165e86a9077
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311033"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutoriel : Intégration d’Azure Active Directory à Workplace by Facebook | Microsoft Docs

Dans ce didacticiel, vous allez apprendre à intégrer Workplace by Facebook à Azure Active Directory (Azure AD).
L’intégration de Workplace by Facebook à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Workplace by Facebook.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Workplace by Facebook (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Workplace by Facebook, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Workplace by Facebook pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook a deux produits, Espace de travail Standard (gratuit) et Espace de travail Premium (payant). N’importe quel abonné de l’Espace de travail Premium peut configurer l’intégration SCIM et SSO sans autre implication sur les coûts ou les licences requises. L’authentification unique et SCIM ne sont pas disponibles dans les instances de l’Espace de travail Standard.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Workplace by Facebook prend en charge l’authentification unique initiée par **SP**
* Workplace by Facebook prend en charge **l’approvisionnement juste-à-temps**
* Workplace by Facebook prend en charge **[l’approvisionnement automatique d’utilisateurs](workplacebyfacebook-provisioning-tutorial.md)**

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Ajout de Workplace by Facebook à partir de la galerie

Pour configurer l’intégration de Workplace by Facebook à Azure AD, vous devez ajouter Workplace by Facebook à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Workplace by Facebook à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Workplace by Facebook**, sélectionnez **Workplace by Facebook** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Workplace by Facebook dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Workplace by Facebook, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, vous devez établir une relation entre l’utilisateur Azure AD et l’utilisateur Workplace by Facebook associé.

Pour configurer et tester l’authentification unique Azure AD avec Workplace by Facebook, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Workplace by Facebook](#configure-workplace-by-facebook-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Workplace by Facebook](#create-workplace-by-facebook-test-user)** pour avoir un équivalent de Britta Simon dans Workplace by Facebook, associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Workplace by Facebook, procédez comme suit :

1. Sur le [portail Azure](https://portal.azure.com/), à la page d’intégration de l’application **Workplace by Facebook**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Workplace by Facebook](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<instancename>.facebook.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, entrez une URL au format suivant : `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour connaître les valeurs correctes pour votre communauté Workplace, consultez la page d’authentification du tableau de bord Entreprise de Workplace.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer Workplace by Facebook**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Configurer l’authentification unique Workplace by Facebook

1. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Workplace by Facebook en tant qu’administrateur.
  
    > [!NOTE]
    > Dans le cadre du processus d’authentification SAML, Workplace peut utiliser des chaînes de requête d’une taille pouvant aller jusqu’à 2,5 Ko pour transmettre des paramètres à Azure AD.

2. Dans **Admin Panel** (Panneau d’administration), accédez à l’onglet **Security**.

    ![Panneau d’administration](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. Sous l’onglet **Authentication**, sélectionnez **Single-Sign On (SSO)** (Authentification unique) et procédez comme suit :

    ![Onglet d’authentification](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Dans la zone de texte **URL SAML**, collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    b. Dans la zone de texte **SAML Issuer URI** (URI de l’émetteur SAML), collez la valeur de l’**identificateur Azure Ad** que vous avez copiée dans le portail Azure.

    c. Dans **SAML Logout Redirect** (Redirection de déconnexion SAML) (facultatif), collez la valeur de **l’URL de déconnexion**, que vous avez copiée à partir du portail Azure.

    d. Ouvrez dans le Bloc-notes votre **certificat codé en base 64**, téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **SAML Certificate** (Certificat SAML).

    e. Copiez **Audience URL** pour votre instance et collez-la dans la zone de texte  **Identificateur (ID d’entité)** de la section  **Configuration SAML de base** du portail Azure.

    f. Copiez **Recipient URL** (URL de destinataire) pour votre instance et collez-la dans la zone de texte  **URL d’authentification** de la section  **Configuration SAML de base** du portail Azure.

    g. Faites défiler l’affichage jusqu’au bas de la section et cliquez sur le bouton **Test SSO**. Une fenêtre contextuelle apparaît, avec la page de connexion Azure AD. Entrez normalement vos informations d’identification pour vous authentifier.

    **Résolution des problèmes :** Vérifiez que l’adresse e-mail retournée par Azure AD est identique au compte Workplace avec lequel vous êtes connecté.

    h. Une fois le test terminé, faites défiler jusqu’au bas de la page et cliquez sur le bouton **Save** (Enregistrer).

    i. La page de connexion à Azure AD sera désormais présentée à tous les utilisateurs de Workplace pour qu’ils s’authentifient.

4. **Redirection de déconnexion SAML (facultatif)** -

    Vous pouvez choisir de configurer une URL de déconnexion SAML, qui peut être utilisée pour pointer vers la page de déconnexion d’Azure AD. Quand ce paramètre est activé et configuré, l’utilisateur n’est plus dirigé vers la page de déconnexion de Workplace. Au lieu de cela, il est redirigé vers l’URL qui a été ajoutée dans le paramètre SAML Logout Redirect.

### <a name="configuring-reauthentication-frequency"></a>Configuration de la fréquence de réauthentification

Vous pouvez configurer Workplace pour demander une vérification SAML chaque jour, tous les trois jours, toutes les deux semaines, tous les mois ou jamais.

> [!NOTE]
> La valeur minimale pour la vérification SAML sur les applications mobiles est toutes les semaines.

Vous pouvez également forcer une réinitialisation SAML pour tous les utilisateurs en utilisant ce bouton : Require SAML authentication for all users now (Demander une authentification SAML pour tous les utilisateurs).

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Workplace by Facebook.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Workplace by Facebook**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **Workplace by Facebook**.

    ![Lien Workplace by Facebook dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-workplace-by-facebook-test-user"></a>Créer un utilisateur de test Workplace by Facebook

Dans cette section, un utilisateur appelé Britta Simon est créé dans Workplace by Facebook. Workplace by Facebook prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut.

Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas d’utilisateur dans Workplace by Facebook, un nouvel utilisateur est créé quand vous tentez d’accéder à Workplace by Facebook.

>[!Note]
>Si vous avez besoin de créer un utilisateur manuellement, contactez l’[équipe de prise en charge des clients Workplace by Facebook](https://workplace.fb.com/faq/).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Workplace by Facebook dans le panneau d’accès doit vous connecter automatiquement à l’application Workplace by Facebook pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurer l’approvisionnement de l’utilisateur](workplacebyfacebook-provisioning-tutorial.md)
