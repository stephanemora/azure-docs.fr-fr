---
title: 'Didacticiel : Intégration d’Azure Active Directory avec FreshDesk | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ce302db74f831e67b576e4c0001f21473fd7f2e0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037522"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Didacticiel : Intégration d’Azure Active Directory à FreshDesk

Dans ce didacticiel, vous allez apprendre à intégrer FreshDesk à Azure Active Directory (Azure AD).

L’intégration de FreshDesk dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à FreshDesk
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à FreshDesk (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à FreshDesk, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement FreshDesk pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de FreshDesk depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-freshdesk-from-the-gallery"></a>Ajout de FreshDesk depuis la galerie

Pour configurer l’intégration de FreshDesk dans Azure AD, vous devez ajouter FreshDesk depuis la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter FreshDesk à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]

3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **FreshDesk**. Sélectionnez **FreshDesk** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec FreshDesk avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur FreshDesk correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur FreshDesk associé doit être établie.

Pour ce faire, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur **d’adresse e-mail** dans FreshDesk.

Pour configurer et tester l’authentification unique Azure AD avec FreshDesk, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test FreshDesk](#creating-a-freshdesk-test-user)** pour avoir un équivalent de Britta Simon dans FreshDesk, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application FreshDesk.

**Procédez comme suit pour configurer l’authentification unique Azure AD avec FreshDesk :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **FreshDesk**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.

    ![Configurer l'authentification unique](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Dans la section **Domaine et URL FreshDesk**, procédez comme suit :

    ![Configurer l'authentification unique](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL en utilisant le modèle `https://<tenant-name>.freshdesk.com`, ou toute autre valeur suggérée par FreshDesk.

    > [!NOTE]
    > Notez qu’il ne s’agit pas de la valeur réelle. Vous devez mettre à jour la valeur avec l’URL de connexion réelle. Contactez [l’équipe de support FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) pour obtenir cette valeur.

4. Votre application s’attend à recevoir les assertions SAML dans un certain format, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de votre jeton SAML. La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur** est **user.userprincipalname**, mais **FreshDesk** s’attend à ce qu’elle soit mappée sur l’adresse e-mail de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation.

    ![Configurer l'authentification unique](./media/freshdesk-tutorial/tutorial_attribute.png)

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l'authentification unique](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

    > [!NOTE]
    > Si vous rencontrez des problèmes, consultez ce [lien](https://support.freshdesk.com/support/discussions/topics/317543).

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/freshdesk-tutorial/tutorial_general_400.png)

7. Installez **OpenSSL** dans votre système si vous ne l’avez pas encore fait.

8. Ouvrez **Invite de commandes** et exécutez les commandes suivantes :

    a. Dans l’invite de commandes, entrez la valeur `openssl x509 -inform DER -in FreshDesk.cer -out certificate.crt`.

    > [!NOTE]
    > Ici, **FreshDesk.cer** est le certificat que vous avez téléchargé à partir du Portail Azure.

    b. Dans l’invite de commandes, entrez la valeur `openssl x509 -noout -fingerprint -sha256 -inform pem -in certificate.crt`. 
    
    > [!NOTE]
    > Ici, **certificate.crt** est le certificat de sortie qui a été généré à l’étape précédente.

    c. Copiez la valeur **Empreinte** et collez-la dans le Bloc-notes. Supprimez les deux-points de l’empreinte et obtenez la valeur d’empreinte finale.

9. Dans la section **Configuration de FreshDesk** , cliquez sur **Configurer FreshDesk** pour ouvrir la fenêtre Configurer l’authentification. Copiez l’URL du service d’authentification unique SAML et l’URL de déconnexion à partir de la section **Référence rapide**.

    ![Configurer l'authentification unique](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

10. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Freshdesk en tant qu’administrateur.

11. Sélectionnez l’icône **Settings** (Paramètres) et, dans la section **Security** (Sécurité), procédez comme suit :

    ![Single Sign On](./media/freshdesk-tutorial/IC776770.png "Single Sign On")
  
    a. Pour **Single Sign On (SSO)**, sélectionnez **On**.

    b. Sélectionnez **SAML SSO**.

    c. Dans la zone de texte **SAML Login URL** (URL de connexion SAML), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du Portail Azure.

    d. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur du champ **URL de déconnexion** que vous avez copiée à partir du Portail Azure.

    e. Dans la zone de texte **Security Certificate Fingerprint** (Empreinte digitale de certificat de sécurité), collez la valeur du champ **Empreinte** que vous avez obtenue précédemment après en avoir supprimé les deux-points.
  
    f. Cliquez sur **Enregistrer**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.

    ![Création d’un utilisateur de test Azure AD](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Box.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à FreshDesk, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **FreshDesk**.

    ![Configurer l'authentification unique](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette FreshDesk dans le volet d’accès vous connecte automatiquement à votre application FreshDesk.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png