---
title: 'Tutoriel : Intégration d’Azure Active Directory avec Jamf Pro | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: d28e28a2c4f8144da16c4838f07c9b8bb5ce67f0
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268155"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Tutoriel : Intégration d’Azure Active Directory à Jamf Pro

Dans ce tutoriel, vous allez apprendre à intégrer Jamf Pro dans Azure Active Directory (Azure AD).

L’intégration de Jamf Pro à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Jamf Pro.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Jamf Pro (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Jamf Pro, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Jamf Pro pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Jamf Pro à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-jamf-pro-from-the-gallery"></a>Ajout de Jamf Pro à partir de la galerie

Pour configurer l’intégration de Jamf Pro à Azure AD, vous devez ajouter Jamf Pro à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Jamf Pro à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/jamfprosamlconnector-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/jamfprosamlconnector-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/jamfprosamlconnector-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Jamf Pro**, sélectionnez **Jamf Pro** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/jamfprosamlconnector-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Jamf Pro avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Jamf Pro équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Jamf Pro associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Jamf Pro, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Jamf Pro](#create-a-jamf-pro-test-user)** pour avoir dans Jamf Pro un équivalent de Britta Simon lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Jamf Pro.

**Pour configurer l'authentification unique Azure AD avec Jamf Pro, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Jamf Pro**, sélectionnez **Authentification unique**.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_select_sso.png)

2. Cliquez sur **Modifier le mode d’authentification unique** au-dessus de l’écran pour sélectionner le mode **SAML**.

      ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_ssso.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_sso.png)

4. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/jamfprosamlconnector-tutorial/b1-domains_and_urlsedit.png)

5. Dans la section **Configuration SAML de base**, procédez comme suit :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com/saml/metadata`.

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com/saml/SSO`.

    ![image](./media/jamfprosamlconnector-tutorial//b2-domains_and_urls.png)

    c. Cliquez sur **Définir des URL supplémentaires**.

    d. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.jamfcloud.com`.

    ![image](./media/jamfprosamlconnector-tutorial//b4-domains_and_urls.png)

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Vous obtenez la valeur avec l’identificateur réel à partir de la section **Authentification unique** dans le portail Jamf Pro. La procédure est expliquée plus loin dans le tutoriel. Vous pouvez extraire la valeur de **sous-domaine** réelle à partir de la valeur d’identificateur et utiliser ces informations de **sous-domaine** dans l’URL de connexion et l’URL de réponse.

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![image](./media/jamfprosamlconnector-tutorial/C2_certificate.png)

7. Pour automatiser la configuration dans Jamf Pro, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension** (Installer l’extension).

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)
 
8. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Jamf Pro**, vous êtes alors orienté vers l’application Jamf Pro. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Jamf Pro. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 9 à 12.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

9. Si vous souhaitez configurer manuellement Jamf Pro, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Jamf Pro en tant qu’administrateur et procédez comme suit :

10. Cliquez sur l’**icône de paramètres** en haut à droite de la page.

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

11. Cliquez sur **Authentification unique**.

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

12. Sur la page **Authentification unique**, effectuez les étapes suivantes :

    ![Authentification unique Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Sélectionnez **Serveur Jamf Pro** pour activer l’accès avec authentification unique.

    b. En sélectionnant **Autoriser le contournement pour tous les utilisateurs**, les utilisateurs ne seront pas redirigés vers la page de connexion du fournisseur d’identité pour l’authentification, mais pourront se connecter à Jamf Pro directement. Lorsqu’un utilisateur tente d’accéder à Jamf Pro via le fournisseur d’identité, l’autorisation et l’authentification SSO lancée par le fournisseur d’identité se produisent.

    c. Sélectionnez l’option **NameID** pour **MAPPAGE D’UTILISATEUR : SAML**. Par défaut, ce paramètre est défini sur **NameID**, mais vous pouvez définir un attribut personnalisé.

    d. Sélectionnez **E-mail** pour **MAPPAGE UTILISATEUR : JAMF PRO**. Jamf Pro mappe les attributs SAML envoyés par le fournisseur d’identité de différentes manières : par utilisateurs et par groupes. Lorsqu’un utilisateur tente d’accéder à Jamf Pro, Jamf Pro obtient par défaut des informations sur l’utilisateur de la part du fournisseur d’identité, et les compare avec les comptes d’utilisateur Jamf Pro. Si le compte d’utilisateur entrant n’existe pas dans Jamf Pro, alors une correspondance de nom du groupe se produit.

    e. Collez la valeur `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` dans la zone de texte **NOM DE L’ATTRIBUT DE GROUPE**.

13. Sur la même page, faites défiler jusqu’à **IDENTITY PROVIDER** (Fournisseur d’identité) sous la section **Authentification unique** et effectuez les étapes suivantes :

    ![Configuration Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Sélectionnez **Autres** comme option dans le menu déroulant **Fournisseur d’identité**.

    b. Dans la zone de texte **OTHER PROVIDER (Autre fournisseur)**, entrez **Azure AD**.

    c. Sélectionnez **Metadata URL** (URL des métadonnées) comme option dans la liste déroulante **IDENTITY PROVIDER METADATA SOURCE** (Source des métadonnées du fournisseur d’identité), puis dans la zone de texte suivante, collez la valeur d’**URL des métadonnées de fédération de l’application** que vous avez copiée sur le portail Azure.

    d. Copiez la valeur de l’**ID d’entité** et collez-la dans la zone de texte **Identificateur (ID d’entité)**  de la section **Domaine et URL Jamf Pro** du portail Azure.

    >[!NOTE]
    > Ici, la valeur floutée est la partie du sous-domaine. Utilisez cette valeur pour remplir l’URL de connexion et l’URL de réponse dans la section **Domaine et URL Jamf Pro** du portail Azure.

    e. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet de gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/jamfprosamlconnector-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/jamfprosamlconnector-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/jamfprosamlconnector-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="create-a-jamf-pro-test-user"></a>Créer un utilisateur de test Jamf Pro

Pour se connecter à Jamf Pro, les utilisateurs d’Azure AD doivent être provisionnés dans Jamf Pro. Dans le cas de Jamf Pro, le provisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Jamf Pro en tant qu’administrateur.

2. Cliquez sur l’**icône de paramètres** en haut à droite de la page.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Cliquez sur **Jamf Pro User Accounts & Groups (Groupes et comptes d’utilisateur Jamf Pro)**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user1.png)

4. Cliquez sur **Nouveau**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user2.png)

5. Sélectionnez **Create Standard Account (Créer un compte standard)**.

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user3.png)

6. Dans la boîte de dialogue **New Account (Nouveau compte)**, procédez comme suit :

    ![Ajouter un employé](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Dans la zone de texte **USERNAME (Nom d’utilisateur)**, tapez le nom complet BrittaSimon.

    b. Sélectionnez les options adaptées à votre organisation pour **ACCESS LEVEL** (Niveau d’accès), **PRIVILEGE SET (Ensemble de privilèges)** et **ACCESS STATUS (État de l’accès)**.

    c. Dans la zone de texte **FULL NAME (Nom complet)**, tapez Britta Simon.

    d. Dans la zone de texte **EMAIL ADDRESS (Adresse e-mail)**, tapez l’adresse e-mail du compte de Britta Simon.

    e. Dans la zone de texte **PASSWORD (Mot de passe)**, tapez un mot de passe pour l’utilisateur.

    f. Dans la zone de texte **VERIFY PASSWORD (Vérifier le mot de passe)**, tapez le mot de passe de l’utilisateur.

    g. Cliquez sur **Enregistrer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Jamf Pro.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_proapplications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/jamfprosamlconnector-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/jamfprosamlconnector-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque Jamf Pro dans le volet d’accès, vous devez être connecté automatiquement à votre application Jamf Pro.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
