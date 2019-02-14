---
title: "Tutoriel : Intégration d'Azure Active Directory à Zscaler Two | Microsoft Docs"
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler Two.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25b69f13cbf742c2bb98367a3181e45162715d67
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192623"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Tutoriel : Intégration d'Azure Active Directory à Zscaler Two

L’objectif de ce didacticiel est de montrer comment intégrer Zscaler Two à Azure Active Directory (Azure AD).

Intégrer Zscaler Two à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler l'accès à Zscaler Two.
- Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Zscaler Two (via l'authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Zscaler Two, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Zscaler Two pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajouter Zscaler Two à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-zscaler-two-from-the-gallery"></a>Ajouter Zscaler Two à partir de la galerie

Pour configurer l’intégration de Zscaler Two à Azure AD, vous devez ajouter Zscaler Two, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Zscaler Two à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **Zscaler Two**, sélectionnez **Zscaler Two** dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l'application.

    ![Zscaler Two dans la liste des résultats](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zscaler Two grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur Zscaler Two correspondant à l’utilisateur Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Zscaler Two qui lui est associé.

Pour configurer et tester l’authentification unique Azure AD avec Zscaler Two, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Zscaler Two](#creating-a-zscaler-two-test-user)** pour avoir un équivalent de Britta Simon dans Zscaler Two qui est lié à la représentation de l’utilisateur Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Zscaler Two.

**Pour configurer l’authentification unique Azure AD avec Zscaler Two, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Zscaler Two**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d'authentification unique dans Domaine et URL Zscaler Two](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

    Dans la zone de texte URL de connexion, entrez l’URL utilisée par vos utilisateurs pour se connecter à l’application Zscaler Two.

    > [!NOTE] 
    > Vous devez mettre à jour cette valeur avec l’URL de connexion réelle. Pour obtenir ces valeurs, contactez [l’équipe du support Zscaler Two](https://www.zscaler.com/company/contact).

5. L'application Zscaler Two attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur et revendications** dans la page d’intégration des applications. Sur la page **Configurer l'authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur et revendications**.

    ![Le lien Attribut](./media/zscaler-two-tutorial/tutorial_zscalertwo_attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom  | Attribut source  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Dans la liste **Attribut de la source**, sélectionnez la valeur de l’attribut.

    c. Cliquez sur **OK**.

    d. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Cliquez [ici](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) pour savoir comment configurer un rôle dans Azure AD.

7. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

8. Dans la section **Configurer Zscaler Two**, copiez l'URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de Zscaler Two](common/configuresection.png)

9. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d'entreprise Zscaler Two en tant qu'administrateur.

10. Sélectionnez **Administration > Authentification > Paramètres d'authentification** et procédez comme suit :
   
    ![Administration](./media/zscaler-two-tutorial/ic800206.png "Administration")

    a. Sous Type d'authentification, choisissez **SAML**.

    b. Cliquez sur **Configure SAML**.

11. Dans la fenêtre **Modifier les paramètres SAML**, procédez comme suit, puis cliquez sur Enregistrer.  
            
    ![Gérer les utilisateurs et l’authentification](./media/zscaler-two-tutorial/ic800208.png "Gérer les utilisateurs et l’authentification")
    
    a. Dans la zone de texte **URL du portail SAML**, collez l'**URL de connexion** que vous avez copiée sur le portail Azure.

    b. Dans la zone de texte **Attribut de l'ID de connexion**, entrez **NameID**.

    c. Cliquez sur **Charger** pour charger le certificat de signature SAML Azure que vous avez téléchargé depuis le portail Azure dans le **Certificat SSL public**.

    d. Activez/désactivez **Activer l'approvisionnement automatique SAML**.

    e. Dans la zone de texte **Attribut du nom d'affichage de l'utilisateur**, entrez **displayName** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs displayName.

    f. Dans la zone de texte **Attribut du nom de groupe**, entrez **memberOf** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs memberOf.

    g. Dans **Attribut du nom du service**, entrez **department** si vous souhaitez activer l'approvisionnement automatique SAML pour les attributs department.

    i. Cliquez sur **Enregistrer**.

12. Dans la page **Configure User Authentication** , procédez comme suit :

    ![Administration](./media/zscaler-two-tutorial/ic800207.png)

    a. Passez la souris sur le menu **Activation** en bas à gauche.

    b. Cliquez sur **Activer**.

## <a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer

1. Démarrez **Internet Explorer**.

1. Pour ouvrir la boîte de dialogue **Options Internet**, sélectionnez **Options Internet** dans le menu **Outils**.   
    
     ![Options Internet](./media/zscaler-two-tutorial/ic769492.png "Options Internet")

1. Cliquez sur l’onglet **Connexions** .   
  
     ![Connexions](./media/zscaler-two-tutorial/ic769493.png "Connexions")

1. Cliquez sur **Paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres réseau**.

1. Dans la section Serveur proxy, procédez comme suit :   
   
    ![Serveur proxy](./media/zscaler-two-tutorial/ic769494.png "Serveur proxy")

    a. Sélectionnez **Utiliser un serveur proxy pour le réseau local**.

    b. Dans la zone de texte Adresse, entrez **gateway.Zscaler Two.net**.

    c. Dans la zone de texte Port, tapez **80**.

    d. Sélectionnez **Ne pas utiliser de serveur proxy pour les adresses locales**.

    e. Cliquez sur **OK** pour fermer la boîte de dialogue **Paramètres du réseau local**.

1. Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet**.

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

### <a name="creating-a-zscaler-two-test-user"></a>Création d’un utilisateur de test Zscaler Two

L'objectif de cette section est de créer un utilisateur appelé Britta Simon dans Zscaler Two. Zscaler Two prend en charge l'approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S'il n'existe pas encore, un utilisateur est créé lors d'une tentative d'accès à Zscaler Two.
>[!Note]
>Si vous avez besoin de créer un utilisateur manuellement, contactez l' [équipe de support technique de Zscaler Two](https://www.zscaler.com/company/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zscaler Two.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Zscaler Two**.

    ![Configurer l'authentification unique](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png)

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez l’utilisateur tel que **Britta Simon** dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. Dans la boîte de dialogue **Sélectionner un rôle**, choisissez le rôle d’utilisateur approprié dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

En cliquant sur la vignette Zscaler Two dans le Panneau d’accès, vous allez en principe être connecté automatiquement à votre application Zscaler Two.
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
