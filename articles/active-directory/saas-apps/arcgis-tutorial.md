---
title: 'Didacticiel : Intégration d’Azure Active Directory à ArcGIS Online | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 12ab224481c519db36ae21dd11916649ff0bfbe3
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269032"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Didacticiel : Intégration d’Azure Active Directory à ArcGIS Online

Dans ce didacticiel, vous allez apprendre à intégrer ArcGIS Online à Azure Active Directory (Azure AD).

L’intégration d’ArcGIS Online à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous contrôlez qui a accès à ArcGIS Online.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ArcGIS Online (authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ArcGIS Online, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ArcGIS Online pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’ArcGIS Online à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-arcgis-online-from-the-gallery"></a>Ajout d’ArcGIS Online à partir de la galerie

Pour configurer l’intégration d’ArcGIS Online à Azure AD, vous devez ajouter ArcGIS Online à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ArcGIS Online à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/arcgis-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/arcgis-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/arcgis-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **ArcGIS Online**, sélectionnez **ArcGIS Online** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ArcGIS Online sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur ArcGIS Online correspondant à l’utilisateur Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur ArcGIS Online associé.

Dans ArcGIS Online, affectez la valeur du **nom d’utilisateur** indiquée dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec ArcGIS Online, vous devez suivre les instructions des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test ArcGIS Online](#create-an-arcgis-online-test-user)** pour avoir dans ArcGIS Online un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application ArcGIS Online.

**Pour configurer l’authentification unique Azure AD avec ArcGIS Online, effectuez les étapes suivantes :**

1. Dans le [portail Microsoft Azure](https://portal.azure.com/), dans la page d’intégration de l’application **ArcGIS Online**, sélectionnez **Authentification unique**.

    ![image](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. Cliquez sur **Modifier le mode d’authentification unique** au-dessus de l’écran pour sélectionner le mode **SAML**.

      ![image](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. Dans la section **Configuration de SAML de base**, procédez comme suit :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.maps.arcgis.com`.

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `<companyname>.maps.arcgis.com`.

    ![image](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support ArcGIS Online](http://support.esri.com/en/).

6. Dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger **XML de métadonnées de fédération**, puis enregistrez le fichier XML sur votre ordinateur.

    ![image](./media/arcgis-tutorial/federationxml.png)

7. Pour automatiser la configuration dans **ArcGIS Online**, vous devez installer **l’extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension** (Installer l’extension).

    ![image](./media/arcgis-tutorial/install_extension.png)

8. Après l’ajout de l’extension au navigateur, cliquez sur **Setup ArcGIS Online** (Configurer ArcGIS Online) pour être orienté vers l’application ArcGIS Online. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à ArcGIS Online. Cette extension de navigateur configurera automatiquement l’application pour vous et automatisera les étapes 9 à 13.

9. Si vous souhaitez configurer manuellement ArcGIS Online, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise ArcGIS en tant qu’administrateur et procédez comme suit :

10. Cliquez sur **Edit Settings** (Modifier les paramètres).

    ![Modifier les paramètres](./media/arcgis-tutorial/ic784742.png "Modifier les paramètres")

11. Cliquez sur **Sécurité**.

    ![Sécurité](./media/arcgis-tutorial/ic784743.png "Sécurité")

12. Sous **Enterprise Logins** (Informations de connexion entreprise), cliquez sur **Set Identity Provider** (Définir un fournisseur d’identité).

    ![Connexions de l’entreprise](./media/arcgis-tutorial/ic784744.png "Connexions de l’entreprise")

13. Dans la page de configuration **Set Identity Provider** , procédez comme suit :

    ![Définir le fournisseur d’identité](./media/arcgis-tutorial/ic784745.png "Définir le fournisseur d’identité")

    a. Dans la zone de texte **Name** (Nom), tapez le nom de votre organisation.

    b. Pour **Metadata for the Enterprise Identity Provider will be supplied using**, sélectionnez **A File**.

    c. Pour télécharger votre fichier de métadonnées, cliquez sur **Choose file**.

    d. Cliquez sur **Set Identity Provider** (Définir un fournisseur d’identité).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Microsoft Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.

    ![image](./media/arcgis-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/arcgis-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, procédez comme suit.

    ![image](./media/arcgis-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="create-an-arcgis-online-test-user"></a>Créer un utilisateur de test ArcGIS Online

Pour permettre aux utilisateurs Azure AD de se connecter à ArcGIS Online, vous devez les attribuer dans ArcGIS Online.  
Pour ArcGIS Online, cette attribution s’effectue manuellement.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **ArcGIS** .

2. Cliquez sur **Invite Members** (Inviter des membres).
   
    ![Inviter des membres](./media/arcgis-tutorial/ic784747.png "Inviter des membres")

3. Sélectionnez **Add members automatically without sending an email** (Ajouter des membres automatiquement sans envoyer d’e-mail), puis cliquez sur **Next** (Suivant).
   
    ![Ajouter des membres automatiquement](./media/arcgis-tutorial/ic784748.png "Ajouter des membres automatiquement")

4. Dans la page de boîte de dialogue **Members** , procédez comme suit :
   
     ![Ajouter et vérifier](./media/arcgis-tutorial/ic784749.png "Ajouter et vérifier")
    
     a. Entrez **l’adresse e-mail**, le **prénom** et le **nom** d’un compte Azure AD valide que vous voulez attribuer.
  
     b. Cliquez sur **Add And Review** (Ajouter et vérifier).
5. Passez en revue les données que vous avez entrées, puis cliquez sur **Add Members** (Ajouter des membres).
   
    ![Ajouter un membre](./media/arcgis-tutorial/ic784750.png "Ajouter un membre")
        
    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à ArcGIS Online.

1. Dans le portail Microsoft Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/arcgis-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **ArcGIS Online**.

    ![image](./media/arcgis-tutorial/d_all_application.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/arcgis-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/arcgis-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton**Sélectionner** au bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette ArcGIS Online dans le volet d’accès, vous devez être authentifié automatiquement auprès de votre application ArcGIS Online.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



