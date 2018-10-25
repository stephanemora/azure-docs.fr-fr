---
title: 'Didacticiel : Intégration d’Azure Active Directory à Zendesk | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268172"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Didacticiel : Intégration d’Azure Active Directory à Zendesk

Dans ce didacticiel, vous allez apprendre à intégrer Zendesk à Azure Active Directory (Azure AD).

L’intégration de Zendesk à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Zendesk.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Zendesk (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Zendesk, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement pour lequel l’authentification unique à Zendesk est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Zendesk depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-zendesk-from-the-gallery"></a>Ajout de Zendesk depuis la galerie

Pour configurer l’intégration de Zendesk à Azure AD, vous devez ajouter Zendesk, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Zendesk à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/zendesk-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/zendesk-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/zendesk-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Zendesk**, sélectionnez **Zendesk** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zendesk, sur un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Zendesk correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur dans Zendesk associé doit être établie.

Dans Zendesk, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Zendesk, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Zendesk](#create-a-zendesk-test-user)** : pour avoir un équivalent de Britta Simon dans Zendesk lié à la représentation de l’utilisateur Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Zendesk.

**Pour configurer l’authentification unique Azure AD avec Zendesk, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Zendesk**, cliquez sur **Authentification unique**.

    ![image](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. Cliquez sur **Modifier le mode d’authentification unique** au-dessus de l’écran pour sélectionner le mode **SAML**.

      ![image](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. Dans la section **Configuration SAML de base**, procédez comme suit :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.zendesk.com`.

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `<subdomain>.zendesk.com`.

    ![image](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise).

6. Zendesk attend les assertions SAML dans un format spécifique. Il n’y a aucun attribut SAML obligatoire, mais si vous le souhaitez, vous pouvez ajouter un attribut à partir de la section **Attributs utilisateur** sur la page d’intégration de l’application. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](./media/zendesk-tutorial/i4-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/zendesk-tutorial/i2-attribute.png)

    ![image](./media/zendesk-tutorial/i3-attribute.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.
    
    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.
    
    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Utilisez des attributs d’extension pour ajouter des attributs qui ne sont pas dans Azure AD par défaut. Cliquez sur [les attributs d’utilisateur qui peuvent être définis dans SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) pour obtenir la liste complète des attributs SAML que **Zendesk** accepte.

8. Dans la section du certificat de signature SAML, dans le **Certificat de signature SAML**, copiez l’**empreinte** et enregistrez-la sur votre ordinateur.

    ![image](./media/zendesk-tutorial/C3_certificate.png)

    a. Sélectionnez l’option appropriée pour **Option de signature** si nécessaire.

    b. Sélectionnez l’option appropriée pour **Algorithme de signature** si nécessaire.

    c. Cliquez sur **Enregistrer**.

9. Dans la section **Configurer Zendesk**, cliquez sur **Afficher les instructions détaillées** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez les URL ci-dessus à partir de la **section Référence rapide.**

    Notez que l’URL peut indiquer les éléments suivants :

    a. URL du service d’authentification unique SAML

    b. L’ID d’entité

    c. URL de déconnexion

    ![image](./media/zendesk-tutorial/d1_saml.png) 

10. Il existe deux modes de configuration de Zendesk : Automatique et Manuel.
  
11. Pour automatiser la configuration dans Zendesk, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension** (Installer l’extension).

    ![image](./media/zendesk-tutorial/install_extension.png)

12. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Zendesk**, vous êtes alors orienté vers l’application Zendesk. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Zendesk. Cette extension de navigateur configure automatiquement l’application pour vous et automatise l’étape 13.

     ![image](./media/zendesk-tutorial/d2_saml.png) 

13. Si vous souhaitez configurer manuellement Zendesk, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zendesk en tant qu’administrateur et procédez comme suit :

    * Cliquez sur **Admin**.

    * Dans le volet de navigation gauche, cliquez sur **Settings**, puis sur **Security**.

    * Sur la page **Sécurité**, procédez comme suit :

      ![Sécurité](././media/zendesk-tutorial/ic773089.png "Sécurité")

      ![Authentification unique](././media/zendesk-tutorial/ic773090.png "Authentification unique")

      a. Cliquez sur l’onglet **Administrateurs et Agents**.

      b. Sélectionnez **Single sign-on (SSO) and SAML**, puis **SAML**.

      c. Dans la zone de texte **URL SAML SSO**, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

      d. Dans la zone de texte **URL de déconnexion à distance**, collez la valeur de **l’URL de déconnexion** que vous avez copiée sur le portail Azure.

      e. Dans la zone de texte **Empreinte du certificat**, collez la valeur du certificat **Empreinte** que vous avez copiée à partir du portail Azure.

      f. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/zendesk-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/zendesk-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, procédez comme suit.

    ![image](./media/zendesk-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="create-a-zendesk-test-user"></a>Créer un utilisateur de test Zendesk

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Zendesk. Zendesk prend en charge l’approvisionnement automatique d’utilisateurs, qui est activé par défaut. Des informations supplémentaires sur la configuration de l’approvisionnement automatique d’utilisateurs sont disponibles [ici](Zendesk-provisioning-tutorial.md).

**Si vous avez besoin de créer un utilisateur manuellement, procédez comme suit :**

> [!NOTE]
> Les comptes de **l’utilisateur final** sont automatiquement approvisionnés lors de la connexion. Les comptes **Agent** et **Admin** doivent être approvisionnés manuellement dans **Zendesk** avant de vous connecter.

1. Connectez-vous à votre client **Zendesk** .

2. Sélectionnez l’onglet **Customer List** .

3. Sélectionnez l’onglet **User**, puis cliquez sur **Add**.

    ![Ajouter un utilisateur](././media/zendesk-tutorial/ic773632.png "Ajouter un utilisateur")
4. Tapez le **Nom** et **l’e-mail** d’un compte Azure AD que vous souhaitez approvisionner, puis cliquez sur **Save**.

    ![Nouvel utilisateur](././media/zendesk-tutorial/ic773633.png "Nouvel utilisateur")

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zendesk pour approvisionner des comptes d’utilisateurs Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zendesk.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/zendesk-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Zendesk**.

    ![image](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/zendesk-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/zendesk-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Zendesk dans le volet d’accès, vous devez être connecté automatiquement à votre application Zendesk.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’approvisionnement de l’utilisateur](zendesk-provisioning-tutorial.md)
