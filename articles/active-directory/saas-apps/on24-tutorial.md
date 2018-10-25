---
title: 'Didacticiel : intégration d’Azure Active Directory avec ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 1ec18f0013a7fa640395a8b8bedd9df8b0924c3a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49070942"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Didacticiel : intégration d’Azure Active Directory avec ON24 Virtual Environment SAML Connection

Dans ce didacticiel, vous allez apprendre à intégrer ON24 Virtual Environment SAML Connection à Azure Active Directory (Azure AD).

ON24 Virtual Environment SAML Connection dans Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à ON24 Virtual Environment SAML Connection.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à ON24 Virtual Environment SAML Connection (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ON24 Virtual Environment SAML Connection, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à ON24 Virtual Environment SAML Connection pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de la connexion SAML à l’environnement virtuel ON24 depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Ajout de la connexion SAML à l’environnement virtuel ON24 depuis la galerie
Pour configurer l’intégration de la connexion SAML à l’environnement virtuel ON24 à Azure AD, vous devez ajouter la connexion SAML à l’environnement virtuel ON24 à votre liste d’applications SaaS managées depuis la galerie.

**Pour ajouter la connexion SAML à l’environnement virtuel ON24 depuis la galerie, procédez aux étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/on24-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/on24-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/on24-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **connexion SAML à l’environnement virtuel ON24**, sélectionnez **connexion SAML à l’environnement virtuel ON24** dans le volet de résultats puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec la connexion SAML à l’environnement virtuel ON24, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur ON24 Virtual Environment SAML Connection correspondant à l’utilisateur Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur ON24 Virtual Environment SAML Connection associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec ON24 Virtual Environment SAML Connection, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test ON24 Virtual Environment SAML Connection](#create-an-on24-virtual-environment-saml-connection-test-user)** pour obtenir un équivalent de Britta Simon dans ON24 Virtual Environment SAML Connection lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ON24 Virtual Environment SAML Connection.

**Pour configurer Azure AD avec ON24 Virtual Environment SAML Connection, suivez les étapes :**

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **ON24 Virtual Environment SAML Connection**, cliquez sur **Authentification unique**.

    ![image](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. Cliquez sur **Modifier le mode d’authentification unique** en haut de l’écran pour sélectionner le mode **SAML**.

      ![image](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/on24-tutorial/b1_b2_saml_sso.png)

4. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. Dans la section **Configuration SAML de base**, suivez les étapes ci-dessous, si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![image](./media/on24-tutorial/tutorial_on24_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL :

     **URL d’environnement de production**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL d’environnement assurance qualité**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. Dans la zone de texte **URL de réponse**, tapez l’URL :
    
     **URL d’environnement de production**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL d’environnement assurance qualité**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Cliquez sur **Définir des URL supplémentaires**. 

    d. Dans la zone de texte **État de relais**, tapez une URL : `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

    e. Si vous souhaitez configurer l’application en mode initié par **IDP**, dans la zone de texte**URL d’authentification**, saisissez l’URL : `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le certificat correspondant à vos besoins et enregistrez-le sur votre ordinateur.

    ![image](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. Pour configurer l’authentification unique sur **ON24 Virtual Environment SAML Connection**, vous devez envoyer un certificat/les métadonnées téléchargés à partir du portail Azure à [l’équipe du support technique ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/on24-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/on24-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/on24-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>Créer un utilisateur de test ON24 Virtual Environment SAML Connection

Dans cette section, vous créez un utilisateur appelé Britta Simon dans ON24 Virtual Environment SAML Connection. Travaillez avec l’[équipe du support technique ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) pour ajouter des utilisateurs à la plateforme ON24 Virtual Environment SAML Connection. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ON24 Virtual Environment SAML Connection.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/on24-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **ON24 Virtual Environment SAML Connection**.

    ![image](./media/on24-tutorial/tutorial_on24_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/on24-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/on24-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette ON24 Virtual Environment SAML Connection dans le volet d’accès, vous devriez être automatiquement connecté à votre application ON24 Virtual Environment SAML Connection.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

