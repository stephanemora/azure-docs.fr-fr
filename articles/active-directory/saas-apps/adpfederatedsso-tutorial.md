---
title: 'Tutoriel : Intégration d’Azure Active Directory à ADP | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.openlocfilehash: 487c9cb145000b92a4aa664ea2bd159026104b6b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065152"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Tutoriel : Intégration d’Azure Active Directory à ADP

Dans ce didacticiel, vous allez apprendre à intégrer ADP à Azure Active Directory (Azure AD).
L’intégration d’ADP à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à ADP.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à ADP (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ADP, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement ADP pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* ADP prend en charge l’authentification unique initiée par le **fournisseur d’identité**

## <a name="adding-adp-from-the-gallery"></a>Ajout d’ADP depuis la galerie

Pour configurer l’intégration d’ADP à Azure AD, vous devez ajouter ADP disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter ADP à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **ADP**, sélectionnez **ADP** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![ADP dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ADP, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur ADP associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec ADP, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique ADP](#configure-adp-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test ADP](#create-adp-test-user)** pour avoir un équivalent de Britta Simon dans ADP, associé à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec ADP, effectuez les étapes suivantes :

1. Dans le portail Azure, dans la page d’intégration de l’application **ADP**, cliquez sur **l’onglet Propriétés** et effectuez les étapes suivantes : 

    ![Propriétés de l’authentification unique](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Définissez le champ **Activé pour que les utilisateurs se connectent** sur **Oui**.

    b. Copiez **l’URL d’accès utilisateur** et collez-la dans la **section Configurer l’URL d’authentification**, comme expliqué plus loin dans le didacticiel.

    c. Définissez le champs **Affectation de l’utilisateur obligatoire** sur **Oui**.

    d. Définissez le champ **Visible par les utilisateurs** sur **Non**.

2. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **ADP**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

4. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

5. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL ADP](common/idp-identifier.png)

    Dans la zone de texte **Identificateur (ID d’entité)**, tapez une URL : `https://fed.adp.com`

6. L’application ADP attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**. Le nom de la revendication sera toujours **« PersonImmutableID »** et sa valeur mappée à **employeeid**. 

    Le mappage utilisateur d’Azure AD à ADP est effectué avec **employeeid**, mais vous pouvez le mapper à une valeur différente basée sur les paramètres de votre application. Ainsi, contactez d’abord [l’équipe du support ADP](https://www.adp.com/contact-us/overview.aspx) pour utiliser le bon identificateur d’utilisateur et mapper cette valeur à la revendication **« PersonImmutableID »**.

    ![image](common/edit-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | NOM | Attribut source | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

    > [!NOTE] 
    > Avant de pouvoir configurer votre assertion SAML, vous devez contacter l’[équipe du support technique ADP](https://www.adp.com/contact-us/overview.aspx) et lui demander d’affecter la valeur d’attribut d’identificateur d’utilisateur unique à votre locataire. Vous avez besoin de cette valeur pour configurer la revendication personnalisée pour votre application. 

8. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>Configurer l’authentification unique ADP

Pour configurer l’authentification unique côté **ADP**, vous devez charger le **XML de métadonnées** téléchargé sur le [site web ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ce processus peut prendre quelques jours.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurer vos services ADP pour un accès fédéré

>[!Important]
> Vous devez assigner à l’application de service ADP les employés qui ont besoin d’un accès fédéré à vos services ADP, puis réassigner les utilisateurs à un service ADP spécifique.
Une fois la confirmation reçue de votre représentant ADP, configurez vos services ADP et assignez/gérez les utilisateurs pour contrôler l’accès utilisateur à un service ADP spécifique.

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **ADP**, sélectionnez **ADP** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![ADP dans la liste des résultats](common/search-new-app.png)

5. Dans le portail Azure, dans la page d’intégration de votre application **ADP**, cliquez sur **l’onglet Propriétés** et effectuez les étapes suivantes :  

    ![Propriétés de l’authentification unique liée](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Définissez le champ **Activé pour que les utilisateurs se connectent** sur **Oui**.

    b.  Définissez le champs **Affectation de l’utilisateur obligatoire** sur **Oui**.

    c.  Définissez le champ **Visible par les utilisateurs** sur **Oui**.

6. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **ADP**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

7. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le **Mode** **Lié**. pour lier votre application à **ADP**.

    ![Authentification unique liée](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Accédez à la section **Configurer l’URL d’authentification**, puis effectuez les étapes suivantes :

    ![Propriétés de l’authentification unique](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Collez l’**URL d’accès utilisateur**, que vous avez copiée à partir de l’**onglet Propriétés** plus haut (à partir de l’application ADP principale).
                                                             
    b. Voici les 5 applications qui prennent en charge différentes **URL d’état de relais**. Vous devez ajouter la valeur **d’URL d’état de relais** appropriée pour une application particulière manuellement à **l’URL d’accès utilisateur**.
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Enregistrez** vos modifications.

10. Une fois la confirmation reçue de votre représentant ADP, commencez à effectuer un test avec un ou deux utilisateurs.

    a. Assignez quelques utilisateurs à l’application de service ADP pour tester l’accès fédéré.

    b. Le test est réussi quand les utilisateurs accèdent à l’application de service ADP dans la galerie et qu’ils peuvent accéder à leur service ADP.
 
11. Une fois confirmée la réussite d’un test, assignez le service ADP fédéré à des utilisateurs ou à des groupes d’utilisateurs, comme expliqué plus loin dans le didacticiel, et déployez-le pour vos employés.
 
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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ADP.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **ADP**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **ADP**.

    ![Lien ADP dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-adp-test-user"></a>Créer un utilisateur de test ADP

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ADP. Pour ajouter les utilisateurs au compte ADP, contactez l’[équipe du support ADP](https://www.adp.com/contact-us/overview.aspx). 

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette ADP dans le panneau d’accès doit vous connecter automatiquement à l’application ADP pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

