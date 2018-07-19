---
title: 'Tutoriel : Intégration d’Azure Active Directory à Supermood | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Supermood.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: ebd8e28e8adc357f0e6a0582f422085ddf50749f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041022"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>Tutoriel : Intégration d’Azure Active Directory à Supermood

Dans ce didacticiel, vous allez apprendre à intégrer Supermood à Azure Active Directory (Azure AD).

L’intégration de Supermood à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Supermood.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Supermood (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Supermood, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Supermood pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Supermood à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-supermood-from-the-gallery"></a>Ajout de Supermood à partir de la galerie
Pour configurer l’intégration de Supermood à Azure AD, vous devez ajouter Supermood à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Supermood à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Supermood**, sélectionnez **Supermood** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Supermood dans la liste des résultats](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Supermood avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Supermood équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Supermood associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Supermood, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Supermood](#create-a-supermood-test-user)** pour avoir un équivalent de Britta Simon dans Supermood qui soit associé à la représentation de l’utilisateur Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Supermood.

**Pour configurer l’authentification unique Azure AD avec Supermood, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Supermood**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

3. Dans la section  **Domaine et URL Supermood**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Supermood](./media/supermood-tutorial/tutorial_supermood_url.png)

    a. Cliquez sur **Afficher les paramètres d’URL avancés**.

    b. Si vous souhaitez configurer l’application en mode initié par **IDP**, dans la zone de texte**État de relais**, suivez les étapes ci-dessous :`https://supermood.co/auth/sso/saml20`

    c. Si vous souhaitez configurer l’application en mode initié par **IDP**, dans la zone de texte**URL d’authentification**, suivez les étapes ci-dessous :`https://supermood.co/app/#!/loginv2`

4. L’application Supermood attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configurer l'authentification unique](./media/supermood-tutorial/tutorial_supermood_attribute.png)

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l'authentification unique](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Configurer l'authentification unique](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    d. Cliquez sur **OK**.

6. Dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/supermood-tutorial/tutorial_general_400.png)

8. Accédez à votre volet d’administration Supermood.co en tant qu’administrateur de sécurité.

9. Cliquez sur **Mon compte** (en bas à gauche) et sur **Authentification unique (SSO)**.

    ![Le certificat unique](./media/supermood-tutorial/tutorial_supermood_single.png)
10. Sur **Vos configurations SAML 2.0**, cliquez sur **Ajouter une configuration SAML 2.0 pour un domaine de messagerie**.

    ![Le certificat Ajouter](./media/supermood-tutorial/tutorial_supermood_add.png)

11. Sur **ajouter une configuration de SAML 2.0 pour un domaine de messagerie**. section, procédez comme suit :

    ![Le certificat saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Dans la zone de texte du **domaine de messagerie pour ce fournisseur d’identité**, tapez votre domaine.

    b. Dans la zone de texte **Utiliser une URL des métadonnées**, collez **l’URL des métadonnées de fédération de l’application** que vous avez copiée sur le portail Azure.

    c. Cliquez sur **Add**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/supermood-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/supermood-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/supermood-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/supermood-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-supermood-test-user"></a>Créer un utilisateur de test Supermood

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Supermood. Supermood prend en charge l’approvisionnement juste-à-temps, qui est activée par défaut pour les utilisateurs dont les e-mails appartiennent aux domaines qui sont ajoutés lors de la configuration à la fin de Supermood. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Supermood s’il n’existe pas déjà.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Supermood](mailto:hello@supermood.fr).


### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Supermood.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Supermood, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Supermood**.

    ![Lien Supermood dans la liste des applications](./media/supermood-tutorial/tutorial_supermood_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Supermood dans le volet d’accès, vous devez être connecté automatiquement à votre application Supermood.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png

