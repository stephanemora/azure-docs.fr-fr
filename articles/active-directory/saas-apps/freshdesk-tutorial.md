---
title: 'Didacticiel : Intégration d’Azure Active Directory avec FreshDesk | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010811"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Didacticiel : Intégration d’Azure Active Directory à FreshDesk

Dans ce didacticiel, vous allez apprendre à intégrer FreshDesk à Azure Active Directory (Azure AD).

L’intégration de FreshDesk dans Azure AD vous offre les avantages suivants :

- Azure AD vous permet de contrôler qui a accès à FreshDesk.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à FreshDesk (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à FreshDesk, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement FreshDesk pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de FreshDesk depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-freshdesk-from-the-gallery"></a>Ajout de FreshDesk depuis la galerie

Pour configurer l’intégration de FreshDesk dans Azure AD, vous devez ajouter FreshDesk depuis la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter FreshDesk à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **FreshDesk**, sélectionnez **FreshDesk** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![FreshDesk dans la liste des résultats](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec FreshDesk avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur FreshDesk correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur FreshDesk associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec FreshDesk, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test FreshDesk](#creating-a-freshdesk-test-user)** pour avoir un équivalent de Britta Simon dans FreshDesk, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application FreshDesk.

**Procédez comme suit pour configurer l’authentification unique Azure AD avec FreshDesk :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **FreshDesk**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL FreshDesk](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant le modèle `https://<tenant-name>.freshdesk.com`, ou toute autre valeur suggérée par FreshDesk.

    b. Dans la zone de texte **Identificateur (ID d’entité)**, tapez une URL en utilisant le modèle `https://<tenant-name>.freshdesk.com`, ou toute autre valeur suggérée par FreshDesk.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez [l’équipe de support FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) pour obtenir ces valeurs.

5. L’application FreshDesk s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de votre jeton SAML. La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur** est **user.userprincipalname**, mais **FreshDesk** s’attend à ce qu’elle soit mappée sur l’adresse e-mail de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation.

    ![image](./media/freshdesk-tutorial/i4-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur et revendications**, configurez le jeton SAML comme sur l’image ci-dessus, puis effectuez les étapes suivantes :
    
    a. Cliquez sur **Modifier l’icône** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/freshdesk-tutorial/i2-attribute.png)

    ![image](./media/freshdesk-tutorial/i3-attribute.png)

    b. Dans la liste **Attribut source**, sélectionnez **user.mail**.

    c. Cliquez sur **Enregistrer**.

7. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. Ouvrez **Invite de commandes** et exécutez les commandes suivantes :

    a. Dans l’invite de commandes, entrez la valeur `certutil.exe -dump FreshDesk.cer`.

    > [!NOTE]
    > Ici, **FreshDesk.cer** est le certificat que vous avez téléchargé à partir du Portail Azure.

    b. Copiez la valeur **Cert Hash(sha256)** et collez-le dans le Bloc-notes. 

9. Dans la section **Configurer FreshDesk**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de FreshDesk](common/configuresection.png)

10. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Freshdesk en tant qu’administrateur.

11. Sélectionnez l’icône **Settings** (Paramètres) et, dans la section **Security** (Sécurité), procédez comme suit :

    ![Single Sign On](./media/freshdesk-tutorial/IC776770.png "Single Sign On")
  
    a. Pour **Single Sign On (SSO)**, sélectionnez **On**.

    b. Sélectionnez **SAML SSO**.

    c. Dans la zone de texte **SAML Login URL** (URL de connexion SAML), collez la valeur d’**URL de connexion** que vous avez copiée sur le portail Azure.

    d. Dans la zone de texte **Logout URL** (URL de déconnexion), collez l’**URL de déconnexion** que vous avez copiée sur le portail Azure.

    e. Dans la zone de texte **Security Certificate Fingerprint** (Empreinte digitale de certificat de sécurité), collez la valeur **Cert Hash(sha256)** que vous avez obtenue précédemment.
  
    f. Cliquez sur **Enregistrer**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-a-freshdesk-test-user"></a>Création d’un utilisateur de test FreshDesk

Pour permettre aux utilisateurs Azure AD de se connecter à FreshDesk, vous devez les approvisionner dans FreshDesk.  
Dans le cas de FreshDesk, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Freshdesk** .

2. Dans le menu situé en haut, cliquez sur **Admin**.

    ![Administrateur](./media/freshdesk-tutorial/IC776772.png "Administrateur")

3. Dans l’onglet **General Settings**, cliquez sur **Agents**.
  
    ![Agents](./media/freshdesk-tutorial/IC776773.png "Agents")

4. Cliquez sur **New Agent**.

    ![New Agent](./media/freshdesk-tutorial/IC776774.png "New Agent")

5. Dans la boîte de dialogue Agent Information, procédez comme suit :

    ![Agent Information](./media/freshdesk-tutorial/IC776775.png "Agent Information")

    a. Dans la zone de texte **Email** , tapez l’adresse de messagerie Azure AD du compte Azure AD que vous souhaitez approvisionner.

    b. Dans la zone de texte **Full Name** , tapez le nom du compte Azure AD que vous souhaitez approvisionner.

    c. Dans la zone de texte **Title** , tapez le titre du compte Azure AD que vous souhaitez approvisionner.

    d. Cliquez sur **Enregistrer**.

    >[!NOTE]
    >Le titulaire du compte Azure AD reçoit alors un message électronique qui contient un lien pour confirmer le compte avant qu’il soit activé.
    >
    >[!NOTE]
    >Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur Freshdesk pour approvisionner Freshdesk en comptes d’utilisateurs AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FreshDesk.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **FreshDesk**.

    ![Configurer l'authentification unique](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette FreshDesk dans le volet d’accès, vous devez être connecté automatiquement à votre application FreshDesk.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
