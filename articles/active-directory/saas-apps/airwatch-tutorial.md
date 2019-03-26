---
title: 'Didacticiel : Intégration d’Azure Active Directory dans AirWatch | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e20a298c34a7b2723963396f8ccaafaa9472e19a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888329"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Didacticiel : Intégration d’Azure Active Directory dans AirWatch

Dans ce didacticiel, vous allez apprendre à intégrer AirWatch dans Azure Active Directory (Azure AD).
L’intégration d’AirWatch dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à AirWatch.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à AirWatch (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD dans AirWatch, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement AirWatch pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* AirWatch prend en charge l’authentification unique (SSO) initiée par **SP**

## <a name="adding-airwatch-from-the-gallery"></a>Ajout d’AirWatch à partir de la galerie

Pour configurer l’intégration d’AirWatch dans Azure AD, vous devez ajouter AirWatch à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter AirWatch à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **AirWatch**, sélectionnez **AirWatch** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![AirWatch dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec AirWatch, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur AirWatch associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec AirWatch, vous devez vous conformer aux indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique AirWatch](#configure-airwatch-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Créer un utilisateur de test AirWatch](#create-airwatch-test-user)** pour avoir un équivalent de Britta Simon dans AirWatch lié à la représentation Azure AD de l’utilisateur.
5. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec AirWatch, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **AirWatch**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL AirWatch](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez la valeur comme suit : `AirWatch`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Contactez l’[équipe de support technique AirWatch](https://www.air-watch.com/company/contact-us/) pour obtenir cette valeur. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application AirWatch s’attend à ce que les assertions SAML soient dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom |  Attribut source|
    |---------------|----------------|
    | Identificateur d’utilisateur | user.userprincipalname |
    | | |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

8. Dans la section **Configurer AirWatch**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-airwatch-single-sign-on"></a>Configurer l’authentification unique AirWatch

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AirWatch en tant qu’administrateur.

2. Dans le volet de navigation gauche, cliquez sur **Accounts**, puis sur **Administrators**.

   ![Administrateurs](./media/airwatch-tutorial/ic791920.png "Administrateurs")

3. Développez le menu **Settings**, puis cliquez sur **Directory Services**.

   ![Paramètres](./media/airwatch-tutorial/ic791921.png "Paramètres")

4. Cliquez sur l’onglet **User** (Utilisateur) puis, dans la zone de texte **Base DN** (Nom unique de base), tapez votre nom de domaine et cliquez sur **Save** (Enregistrer).

   ![Utilisateur](./media/airwatch-tutorial/ic791922.png "Utilisateur")

5. Cliquez sur l’onglet **Server** .

   ![Serveur](./media/airwatch-tutorial/ic791923.png "Serveur")

6. Procédez comme suit :

    ![Télécharger](./media/airwatch-tutorial/ic791924.png "Télécharger")   

    a. Pour **Directory Type**, sélectionnez **None**.

    b. Sélectionnez **Use SAML For Authentication**.

    c. Pour charger le certificat téléchargé, cliquez sur **Upload**.

7. Dans la section **Request** , procédez comme suit :

    ![Demande](./media/airwatch-tutorial/ic791925.png "Demande")  

    a. Pour **Request Binding Type**, sélectionnez **POST**.

    b. Dans la boîte de dialogue **Configurer l’authentification unique sur AirWatch** du portail Azure, copiez la valeur **URL de connexion**, puis collez-la dans la zone de texte **Identity Provider Single Sign On URL** (URL d’authentification unique du fournisseur d’identité).

    c. Pour **NameID Format**, sélectionnez **Email Address**.

    d. Cliquez sur **Enregistrer**.

8. Cliquez à nouveau sur l’onglet **User** .

    ![Utilisateur](./media/airwatch-tutorial/ic791926.png "Utilisateur")

9. Dans la section **Attribute** , procédez comme suit :

    ![Attribut](./media/airwatch-tutorial/ic791927.png "Attribut")

    a. Dans la zone de texte **Object Identifier** (Identificateur de l’objet), tapez `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Dans la zone de texte **Username** (Nom d’utilisateur), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Dans la zone de texte **Display Name** (Nom d’affichage), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Dans la zone de texte **First Name** (Prénom), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Dans la zone de texte **Last Name** (Nom), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Dans la zone de texte **Email** (E-mail), tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à AirWatch.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **AirWatch**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **AirWatch**.

    ![Lien AirWatch dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-airwatch-test-user"></a>Créer un utilisateur de test AirWatch

Pour se connecter à AirWatch, les utilisateurs d’Azure AD doivent être approvisionnés dans AirWatch. Dans le cas d’AirWatch, cet approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **AirWatch** en tant qu’administrateur.

2. Dans le volet de navigation situé sur le côté gauche, cliquez sur **Accounts**, puis sur **Users**.
  
   ![Utilisateurs](./media/airwatch-tutorial/ic791929.png "Utilisateurs")

3. Dans le menu **Users**, cliquez sur **List View**, puis sur **Add \> Add User**.
  
   ![Ajouter un utilisateur](./media/airwatch-tutorial/ic791930.png "Ajouter un utilisateur")

4. Dans la boîte de dialogue **Add / Edit User** , procédez comme suit :

   ![Ajouter un utilisateur](./media/airwatch-tutorial/ic791931.png "Ajouter un utilisateur")

   a. Tapez le nom d’utilisateur, le mot de passe, la confirmation du mot de passe, le prénom, le nom et l’adresse e-mail du compte Azure Active Directory valide que vous souhaitez approvisionner dans les champs correspondants, à savoir **Username**, **Password**, **Confirm Password**, **First Name**, **Last Name** et **Email Address**.

   b. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur AirWatch fourni par ce service pour approvisionner des comptes d’utilisateurs Azure Active Directory.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette AirWatch dans le panneau d’accès doit vous connecter automatiquement à l’application AirWatch pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
