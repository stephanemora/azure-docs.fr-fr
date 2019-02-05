---
title: 'Tutoriel : Intégration d’Azure Active Directory à G Suite | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 4705bb8c93381a2487ba94f9dfe3a7e8820f2fd9
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902463"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Tutoriel : Intégration d’Azure Active Directory à G Suite

Dans ce didacticiel, vous allez apprendre à intégrer G Suite à Azure Active Directory (Azure AD).
L’intégration de G Suite à Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à G Suite.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à G Suite (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à G Suite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement G Suite pour lequel l’authentification unique est activée
- Un abonnement Google Apps ou Google Cloud Platform.

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. Ce document a été créé à l’aide de la nouvelle expérience utilisateur Authentification unique. Si vous utilisez toujours l’ancienne version, l’installation n’aura pas la même apparence. Vous pouvez activer la nouvelle expérience dans les paramètres d’authentification unique de l’application G Suite. Accédez à **Azure AD, Applications d’entreprise**, sélectionnez **G Suite** et **Authentification unique**, puis cliquez sur **Essayer la nouvelle expérience**.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

1. **Q : Cette intégration prend-elle en charge l’intégration SSO Google Cloud Platform à Azure AD ?**

    R : Oui. Google Cloud Platform et Google Apps partagent la même plateforme d’authentification. Pour l’intégration Google Cloud Platform, vous avez donc besoin de configurer la SSO avec Google Apps.

2. **Q : Les Chromebooks et autres appareils Chrome sont-ils compatibles avec l’authentification unique Azure AD ?**
  
    R : Oui, les utilisateurs peuvent se connecter à leurs appareils Chromebook en entrant leurs informations d’identification Azure AD. Consultez cet [article du support technique G Suite](https://support.google.com/chrome/a/answer/6060880) pour en savoir plus sur les raisons de la double demande de saisie des informations d’identification.

3. **Q : Si j’ai activé l’authentification unique, les utilisateurs peuvent-ils utiliser leurs informations d’identification Azure AD pour se connecter à un produit Google, comme Google Classroom, Gmail, Google Drive, YouTube, etc. ?**

    R : Oui, en fonction du produit [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) que vous choisissez d’activer et de désactiver pour votre organisation.

4. **Q : Puis-je activer l’authentification unique pour uniquement une partie de mes utilisateurs G Suite ?**

    R : Non, si vous activez l’authentification unique, l’ensemble des utilisateurs G Suite doivent s’authentifier avec leurs informations d’identification Azure AD. G Suite ne prenant pas en charge plusieurs fournisseurs d’identité, le fournisseur associé à votre environnement G Suite peut être Azure AD ou Google, mais pas les deux.

5. **Q : Si un utilisateur est connecté par le biais de Windows, est-il automatiquement authentifié sur G Suite sans qu’il ne lui soit demandé d’entrer un mot de passe ?**

    R : Deux options permettent de prendre en charge ce scénario. Tout d’abord, les utilisateurs peuvent se connecter aux appareils Windows 10 via [Azure Active Directory Join](../device-management-introduction.md). Sinon, les utilisateurs peuvent se connecter aux appareils Windows joints à un domaine au sein d’un répertoire Active Directory sur lequel est activée l’authentification unique à Azure AD via un déploiement [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) . Quelle que soit l’option choisie, vous devez suivre le didacticiel ci-dessous pour activer l’authentification unique entre Azure AD et G Suite.

6. **Q : Que dois-je faire si j’obtiens un message d’erreur « adresse e-mail non valide » ?**

    R : Pour cette configuration, l’attribut d’adresse e-mail est obligatoire pour permettre aux utilisateurs de se connecter. Cet attribut ne peut pas être défini manuellement.

    L’attribut d’adresse e-mail est rempli automatiquement pour tout utilisateur disposant d’une licence Exchange valide. Si l’utilisateur n’est pas associé à un attribut d’adresse e-mail, l’erreur est générée dans la mesure où l’application a besoin d’obtenir cet attribut pour accorder l’accès.

    Pour attribuer une licence Exchange, accédez à portal.office.com avec un compte d’administrateur, puis cliquez sur Centre d’administration, Facturation, Abonnements. Sélectionnez ensuite votre abonnement Office 365, cliquez sur Attribuer à des utilisateurs, sélectionnez les utilisateurs dont vous souhaitez vérifier l’abonnement, puis cliquez sur Modifier les licences dans le volet droit.

    Une fois la licence Exchange attribuée, vous devrez peut-être patienter quelques minutes avant qu’elle ne soit appliquée. Après cela, l’attribut user.mail est rempli automatiquement et le problème doit être résolu.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* G Suite prend en charge l’authentification unique initiée par le **fournisseur de services**
* G Suite prend en charge **[l’approvisionnement automatique d’utilisateurs](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>Ajout de G Suite à partir de la galerie

Pour configurer l’intégration de G Suite à Azure AD, vous devez ajouter G Suite, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter G Suite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **G Suite**, sélectionnez **G Suite** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![G Suite dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec G Suite à l’aide d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur G Suite associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec G Suite, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique G Suite](#configure-g-suite-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test G Suite](#create-g-suite-test-user)** pour avoir dans G Suite un équivalent de Britta Simon lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec G Suite, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **G Suite**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans G Suite Domain and URLs (Domaine et URL G Suite)](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)**, saisissez une URL au format suivant :
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique G Suite](https://www.google.com/contact/).

5. Votre application G Suite attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration d’attributs de jeton SAML. La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur unique** est **user.userprincipalname**, mais G Suite s’attend à ce qu’elle soit mappée sur l’adresse e-mail de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, modifiez les revendications en utilisant l’icône **Modifier** ou ajoutez des revendications en utilisant l’option **Ajouter une nouvelle revendication** pour configurer l’attribut de jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | NOM | Attribut source |
    | ---------------| --------------- |
    | Identificateur d’utilisateur unique | User.mail |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

8. Dans la section **Configurer G Suite**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-g-suite-single-sign-on"></a>Configurer l’authentification unique G Suite

1. Ouvrez un nouvel onglet dans votre navigateur et utilisez votre compte d’administrateur pour vous connecter à la [Console d’administration de G Suite](https://admin.google.com/).

2. Cliquez sur **Sécurité**. Si le lien ne s'affiche pas, il est peut-être masqué par le menu **Autres contrôles** situé en bas de l'écran.

    ![Cliquez sur Sécurité.][10]

3. Sur la page **Sécurité**, cliquez sur **Configurer l'authentification unique (SSO).**

    ![Cliquez sur Authentification unique.][11]

4. Modifiez la configuration comme suit :

    ![Configurer l’authentification unique][12]

    a. Sélectionnez **Configurer l'authentification unique avec un fournisseur d'identité tiers**.

    b. Dans le champ **URL de la page de connexion** de G Suite, collez la valeur de  **l’URL de connexion**  que vous avez copiée dans le portail Azure.

    c. Dans le champ **URL de la page de déconnexion** de G Suite, collez la valeur de  **l’URL de déconnexion**  que vous avez copiée dans le portail Azure.

    d. Dans le champ **Modifier l’URL du mot de passe** de G Suite, collez la valeur de  **Modifier l’URL du mot de passe**  que vous avez copiée dans le portail Azure.

    e. Dans G Suite, chargez le certificat que vous avez téléchargé dans le portail Azure pour le **Certificat de vérification**.

    f. Sélectionnez **Use a domain specific issuer** (Utiliser un émetteur de domaine spécifique).

    g. Cliquez sur **Enregistrer les modifications**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à G Suite.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **G Suite**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, saisissez et sélectionnez **G Suite**.

    ![Lien G Suite dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-g-suite-test-user"></a>Créer un utilisateur de test G Suite

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans les logiciels G Suite. G Suite prend en charge l’approvisionnement automatique, qui est activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas déjà dans G Suite, un nouveau est créé lorsque vous tentez d’accéder aux logiciels G Suite.

> [!NOTE]
> Assurez-vous que votre utilisateur existe déjà dans G Suite si l’approvisionnement dans Azure AD n’a pas été activé avant de tester l’authentification unique.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support Google](https://www.google.com/contact/).

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette G Suite dans le volet d’accès, vous devez être connecté automatiquement à l’application G Suite pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Configurer l’approvisionnement de l’utilisateur](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
