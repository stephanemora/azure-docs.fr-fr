---
title: "Tutoriel : Intégration d'Azure Active Directory à TargetProcess | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/7/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 405a5c12def3bfa25226037788fe55d4d5cb7dd9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204880"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Tutoriel : Intégration d'Azure Active Directory à TargetProcess

Dans ce didacticiel, vous allez apprendre à intégrer TargetProcess à Azure Active Directory (Azure AD).
L’intégration de TargetProcess à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à TargetProcess.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à TargetProcess (par le biais de l'authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec TargetProcess, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement TargetProcess pour lequel l'authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* TargetProcess prend en charge l'authentification unique initiée par **SP**
* TargetProcess prend en charge l'attribution d'utilisateurs **Juste-à-temps**

## <a name="adding-targetprocess-from-the-gallery"></a>Ajout de TargetProcess à partir de la galerie

Pour configurer l’intégration de TargetProcess avec Azure AD, vous devez ajouter TargetProcess à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter TargetProcess à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **TargetProcess**, sélectionnez **TargetProcess** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l'application.

     ![TargetProcess dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l'authentification unique Azure AD auprès de TargetProcess avec un utilisateur de test appelé **Britta Simon**.
Pour que l'authentification unique fonctionne, une relation entre l'utilisateur Azure AD et l'utilisateur TargetProcess associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec TargetProcess, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test TargetProcess](#create-targetprocess-test-user)** pour avoir dans TargetProcess un équivalent de Britta Simon lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l'authentification unique Azure AD avec TargetProcess, procédez comme suit :

1. Sur le [portail Azure](https://portal.azure.com/), accédez à la page d'intégration de l'application **TargetProcess** et sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d'authentification unique dans Domaine et URL TargetProcess](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.tpondemand.com/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels.  Pour obtenir ces valeurs, contactez l’[équipe de support client TargetProcess](mailto:support@targetprocess.com).

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Dans la section **Configurer TargetProcess**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

7. Pour automatiser la configuration dans **TargetProcess**, vous devez installer l'**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension** (Installer l'extension).

    ![image](./media/target-process-tutorial/install_extension.png)

8. Après l'ajout de l'extension au navigateur, cliquez sur **Setup TargetProcess** (Configurer TargetProcess) pour être orienté vers l'application TargetProcess. Fournissez ensuite les informations d'identification de l'administrateur pour vous connecter à TargetProcess. Cette extension de navigateur configurera automatiquement l’application pour vous et automatisera les étapes 9 à 13.

    **Si vous souhaitez configurer l'application manuellement, procédez comme suit :**

9. Connectez-vous à votre application TargetProcess en tant qu’administrateur.

10. Dans le menu situé en haut, cliquez sur **Setup**.

    ![Paramétrage](./media/target-process-tutorial/tutorial_target_process_05.png)

11. Cliquez sur **Paramètres**.

    ![Paramètres](./media/target-process-tutorial/tutorial_target_process_06.png)

12. Cliquez sur **Single Sign-on**.

    ![Cliquer sur Authentification unique](./media/target-process-tutorial/tutorial_target_process_07.png)

13. Dans la boîte de dialogue Paramètres d’authentification unique, procédez comme suit :

    ![Configurer l'authentification unique](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Cliquez sur **Enable Single Sign-on**.

    b. Dans la zone de texte **URL de connexion**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    c. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat**.

    d. Cliquez sur **Activer la configuration JIT**.

    e. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TargetProcess.

1. Sur le portail Azure, sélectionnez **Applications d'entreprise**, **Toutes les applications**, puis **TargetProcess**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, entrez et sélectionnez **TargetProcess**.

    ![Lien TargetProcess dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-targetprocess-test-user"></a>Créer un utilisateur de test TargetProcess

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans TargetProcess. TargetProcess prend en charge l'approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S'il n'existe pas déjà, un utilisateur est créé lors d'une tentative d'accès à TargetProcess.

> [!Note]
> Si vous devez créer un utilisateur manuellement, contactez l' [équipe de support technique TargetProcess](mailto:support@targetprocess.com).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette TargetProcess du panneau d'accès doit vous connecter automatiquement à l'application TargetProcess pour laquelle vous avez configuré l'authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
