---
title: 'Didacticiel : Intégration d’Azure Active Directory dans Tableau Server | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 84ea1d999a26ce0ce1d548da92549c6a718d5978
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850361"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Didacticiel : Intégration d’Azure Active Directory dans Tableau Server

Dans ce didacticiel, vous allez apprendre à intégrer Tableau Server avec Azure Active Directory (Azure AD).

L’intégration de Tableau Server à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Tableau Server.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Tableau Server (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Tableau Server, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Tableau Server pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Tableau Server à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-tableau-server-from-the-gallery"></a>Ajout de Tableau Server à partir de la galerie

Pour configurer l’intégration de Tableau Server à Azure AD, vous devez ajouter Tableau Server, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Tableau Server à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Tableau Server**, sélectionnez **Tableau Server** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Tableau Server dans la liste des résultats](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Tableau Server grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Tableau Server équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Tableau Server associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Tableau Server, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Tableau Server](#creating-a-tableau-server-test-user)** pour avoir un équivalent de Britta Simon dans Tableau Server qui est lié à la représentation d’utilisateur Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Tableau Server.

**Pour configurer l’authentification unique Azure AD avec Tableau Server, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Tableau Server**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial-general-301.png)

3. L’application Tableau Server attend une revendication personnalisée **nom d’utilisateur** qui doit être définie comme suit. Cela est utilisé comme identifiant utilisateur au lieu de la revendication Identifiant unique de l’utilisateur. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur et revendications** dans la page d’intégration des applications. Cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs et revendications de l’utilisateur**.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur et revendications**, configurez le jeton SAML comme sur l’image ci-dessus, puis effectuez les étapes suivantes :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |    
    | username | user.userprincipalname |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Entrez la valeur **Espace de noms**.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **Enregistrer**.

5. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

6. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://azure.<domain name>.link`
    
    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://azure.<domain name>.link`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > Les valeurs ci-dessus ne sont pas des valeurs réelles. Mettez à jour les valeurs avec l’URL et l’identificateur réels à partir de la page de configuration de Tableau Server, conformément aux explications données plus loin dans le tutoriel.

7. Sur la page **Certificat de signature SAML**, accédez à la section **Certificat de signature SAML** et cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png) 

8. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire Tableau Server en tant qu’administrateur.

9. Sur la page **Configuration de Tableau Server**, procédez comme suit :
   
    ![Configurer l'authentification unique](./media/tableauserver-tutorial/tutorial-tableauserver-001.png)

    a. Dans Tableau Server configuration (Configuration de Tableau Server), cliquez sur l’onglet **SAML** . 
  
    b. Cochez la case **Use SAML for single sign-on**(Utiliser SAML pour l’authentification unique).
   
    c. Tableau Server return URL (URL de retour Tableau Server) : URL à laquelle accèdent les utilisateurs Tableau Server, telle que http://tableau_server. L’utilisation de http://localhost n’est pas recommandée. L’utilisation d’une URL avec une barre oblique finale (par exemple, http://tableau_server/) n’est pas prise en charge. Copiez l’**URL de renvoi Tableau Server** et collez-la dans la zone de texte **URL de connexion** d’Azure AD, dans la section **Domaine et URL Tableau Server**.
   
    d. SAML entity ID (ID d’entité SAML) : l’ID d’entité identifie de façon unique votre installation Tableau Server auprès du fournisseur d’identité. Vous pouvez à nouveau entrer l’URL Tableau Server ici, si vous le souhaitez, mais ce n’est pas obligatoire. Copiez l’**ID d’entité SAML** et collez-la dans la zone de texte **Identificateur** d’Azure AD, dans la section **Domaine et URL Tableau Server**.
     
    e. Cliquez sur **Exporter le fichier de métadonnées** et ouvrez-le dans l’application de l’éditeur de texte. Recherchez l’URL Assertion Consumer Service avec HTTP POST et Index 0, puis copiez l’URL. Collez maintenant cette URL dans la zone de texte **URL de réponse** d’Azure AD, dans la section **Domaine et URL Tableau Server**.
   
    f. Localisez votre fichier de métadonnées de fédération téléchargé à partir du portail Azure, puis chargez-le dans le **fichier de métadonnées du fournisseur d’identité SAML**.
   
    g. Sur la page Configuration de Tableau Server, cliquez sur le bouton **OK**.
   
    >[!NOTE] 
    >Le client doit charger l’ensemble des certificats dans la configuration SAML SSO de Tableau Server. Les certificats seront ignorés dans le flux SSO.
    >Si vous avez besoin d’aide pour la configuration de SAML dans Tableau Server, consultez l’article [Configurer SAML](https://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](common/create-aaduser-01.png) 

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](common/create-aaduser-02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
  
### <a name="creating-a-tableau-server-test-user"></a>Création d’un utilisateur de test Tableau Server

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Tableau Server. Vous devez approvisionner tous les utilisateurs dans Tableau Server. 

Le nom de l’utilisateur doit correspondre à la valeur que vous avez configurée dans l’attribut personnalisé Azure AD **username**. Avec le mappage correct, l’intégration doit fonctionner. [Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Si vous avez besoin de créer un utilisateur manuellement, vous devez contacter l’administrateur Tableau Server de votre organisation.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Tableau Server.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Tableau Server**.

    ![Configurer l'authentification unique](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Tableau Server dans le volet d’accès, vous devez être connecté automatiquement à votre application Tableau Server.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
