---
title: 'Didacticiel : Intégration d’Azure Active Directory à G Suite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 6974350c6abbc6c5f5a8e10b22e91796e2564b08
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268057"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Didacticiel : Intégration d’Azure Active Directory à G Suite

Dans ce didacticiel, vous allez apprendre à intégrer G Suite à Azure Active Directory (Azure AD).

L’intégration de G Suite à Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à G Suite.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à G Suite (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à G Suite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement G Suite pour lequel l’authentification unique est activée
- Un abonnement Google Apps ou Google Cloud Platform.

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

1.  **Q : Cette intégration prend-elle en charge l’intégration SSO Google Cloud Platform à Azure AD ?**
    
    R. : Oui. Google Cloud Platform et Google Apps partagent la même plateforme d’authentification. Pour l’intégration Google Cloud Platform, vous avez donc besoin de configurer la SSO avec Google Apps.


1. **Q : Les Chromebooks et les autres appareils Chrome sont-ils compatibles avec l’authentification unique Azure AD ?**
   
    R : Oui, les utilisateurs pourront se connecter à leurs périphériques Chromebook en saisissant leurs informations d’identification Azure AD. Consultez cet [article du support technique G Suite](https://support.google.com/chrome/a/answer/6060880) pour en savoir plus sur les raisons de la double demande de saisie des informations d’identification.

1. **Q : Si j’ai activé l’authentification unique, les utilisateurs pourront-ils utiliser leurs informations d’identification Azure AD pour se connecter à un produit Google, comme Google Classroom, Gmail, Google Drive, YouTube, etc. ?**
   
    R : Oui, en fonction du produit [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) que vous choisissez d’activer et de désactiver pour votre organisation.

1. **Q : Puis-je activer l’authentification unique pour uniquement un sous-ensemble de mes utilisateurs G Suite ?**
   
    R : Non, si vous activez l’authentification unique, l’ensemble des utilisateurs G Suite devront s’authentifier avec leurs informations d’identification Azure AD. G Suite ne prenant pas en charge plusieurs fournisseurs d’identité, le fournisseur associé à votre environnement G Suite peut être Azure AD ou Google, mais pas les deux.

1. **Q : Si un utilisateur est connecté via Windows, est-il automatiquement authentifié sur G Suite sans qu’il ne lui soit demandé de saisir un mot de passe ?**
   
    R : Ce scénario peut être activé par le biais de deux options. Tout d’abord, les utilisateurs peuvent se connecter aux appareils Windows 10 via [Azure Active Directory Join](../device-management-introduction.md). Sinon, les utilisateurs peuvent se connecter aux appareils Windows joints à un domaine au sein d’un répertoire Active Directory sur lequel est activée l’authentification unique à Azure AD via un déploiement [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) . Quelle que soit l’option choisie, vous devez suivre le didacticiel ci-dessous pour activer l’authentification unique entre Azure AD et G Suite.

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de G Suite à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-g-suite-from-the-gallery"></a>Ajout de G Suite à partir de la galerie

Pour configurer l’intégration de G Suite à Azure AD, vous devez ajouter G Suite, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter G Suite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/google-apps-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/google-apps-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/google-apps-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **G Suite**, sélectionnez **G Suite** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/google-apps-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec G Suite à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur G Suite équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur G Suite associé doit être établie.

Dans G Suite, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec G Suite, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test G Suite](#create-a-g-suite-test-user)** pour avoir dans G Suite un équivalent de Britta Simon lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application G Suite.

**Pour configurer l’authentification unique Azure AD avec G Suite, procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **G Suite**, sélectionnez **Authentification unique**.

    ![image](./media/google-apps-tutorial/b1_b2_select_sso.png)

2. Cliquez sur **Modifier le mode d’authentification unique** au-dessus de l’écran pour sélectionner le mode **SAML**.

      ![image](./media/google-apps-tutorial/b1_b2_saml_ssso.png)

3. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/google-apps-tutorial/b1_b2_saml_sso.png)

4. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/google-apps-tutorial/b1-domains_and_urlsedit.png)

5. Dans la section **Configuration SAML de base**, procédez comme suit :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    ![image](./media/google-apps-tutorial/b1-domains_and_urls.png)
 
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique G Suite](https://www.google.com/contact/).

6. L’application G Suite attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](./media/google-apps-tutorial/i3-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
    
    a. Cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. Dans la liste **Attribut de la source**, sélectionnez la valeur de l’attribut.

    c. Cliquez sur **Enregistrer**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le certificat approprié en fonction de vos besoins, puis enregistrez-le sur votre ordinateur.

    ![image](./media/google-apps-tutorial/certificatebase64.png)

9. Dans la section **Configurer G Suite**, copiez l’URL appropriée en fonction de vos besoins.

    Notez que l’URL peut indiquer les éléments suivants :

    a. URL de connexion

    b. Identificateur Azure Active Directory

    c. URL de déconnexion

    ![image](./media/google-apps-tutorial/d1_saml.png) 

10. Ouvrez un nouvel onglet dans votre navigateur et utilisez votre compte d’administrateur pour vous connecter à la [Console d’administration de G Suite](http://admin.google.com/).

11. Cliquez sur **Sécurité**. Si le lien ne s'affiche pas, il est peut-être masqué par le menu **Autres contrôles** situé en bas de l'écran.
   
    ![Cliquez sur Sécurité.][10]

12. Sur la page **Sécurité**, cliquez sur **Configurer l'authentification unique (SSO).**
   
    ![Cliquez sur Authentification unique.][11]

13. Modifiez la configuration comme suit :
   
    ![Configurer l’authentification unique][12]
   
    a. Sélectionnez **Configurer l'authentification unique avec un fournisseur d'identité tiers**.

    b. Dans le champ **URL de la page de connexion** de G Suite, collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée dans le portail Azure.

    c. Dans le champ **URL de la page de déconnexion** de G Suite, collez la valeur de **l’URL de déconnexion** que vous avez copiée dans le portail Azure. 

    d. Dans le champ **Modifier l’URL du mot de passe** de G Suite, collez la valeur de **Modifier l’URL du mot de passe** que vous avez copiée dans le portail Azure. 

    e. Dans G Suite, chargez le certificat que vous avez téléchargé dans le portail Azure pour le **Certificat de vérification**.

    f. Sélectionnez **Use a domain specific issuer** (Utiliser un émetteur de domaine spécifique).

    g. Cliquez sur **Enregistrer les modifications**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/google-apps-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/google-apps-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, procédez comme suit.

    ![image](./media/google-apps-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-g-suite-test-user"></a>Créer un utilisateur de test G Suite

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans les logiciels G Suite. G Suite prend en charge l’approvisionnement automatique, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas déjà dans G Suite, un nouveau est créé lorsque vous tentez d’accéder aux logiciels G Suite.

>[!NOTE]
>Assurez-vous que votre utilisateur existe déjà dans G Suite si l’approvisionnement dans Azure AD n’a pas été activé avant de tester l’authentification unique.

>[!NOTE] 
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Google](https://www.google.com/contact/).



### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à G Suite.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/google-apps-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **G Suite**.

    ![image](./media/google-apps-tutorial/d_all_proapplications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/google-apps-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/google-apps-tutorial/d_assign_user.png)

4. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

5. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette G Suite dans le volet d’accès, vous devez être connecté automatiquement à votre application G Suite.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png

