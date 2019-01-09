---
title: 'Didacticiel : Intégration d’Azure Active Directory à Adobe Creative Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 8000208dfe15744c891bbf27ad8f9e69f44ef4dc
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807786"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Didacticiel : Intégration d’Azure Active Directory à Adobe Creative Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Adobe Creative Cloud à Azure Active Directory (Azure AD).
L’intégration de Adobe Creative Cloud dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Adobe Creative Cloud.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Adobe Creative Cloud (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Adobe Creative Cloud, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Adobe Creative Cloud pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Adobe Creative Cloud prend en charge l’authentification unique initiée par **SP et IDP**

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Ajout de Adobe Creative Cloud à partir de la galerie

Pour configurer l’intégration de Adobe Creative Cloud à Azure AD, vous devez ajouter Adobe Creative Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Adobe Creative Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Adobe Creative Cloud**, sélectionnez **Adobe Creative Cloud** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Adobe Creative Cloud dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Adobe Creative Cloud, avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Adobe Creative Cloud associé doit être établie.

Pour configurer et tester l’authentification unique avec Azure AD avec Adobe Creative Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Adobe Creative Cloud](#configure-adobe-creative-cloud-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Adobe Creative Cloud lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Adobe Creative Cloud, procédez comme suit :

1. Dans le [Portail Azure](https://portal.azure.com/), sur la page d’intégration de l’application **Adobe Creative Cloud**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Adobe Creative Cloud](common/idp-intiated.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.okta.com/saml2/service-provider/<token>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique d’Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez la valeur `https://adobe.com`.

    ![Informations d’authentification unique dans Domaine et URL Adobe Creative Cloud](common/metadata-upload-additional-signon.png)

6. L’application Adobe Creative Cloud attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :
 
    | NOM | Attribut source|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail

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
    > Les utilisateurs doivent posséder une licence Office 365 ExO pour que la valeur de demande par message soit renseignée dans la réponse SAML.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

9. Dans la section **Configurer Adobe Creative Cloud**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Configurer l’authentification unique Adobe Creative Cloud

1. Dans une fenêtre différente de navigateur, connectez-vous à [Adobe Admin Console](https://adminconsole.adobe.com) en tant qu’administrateur.

2. Accédez à **Paramètres** sur la barre de navigation supérieure, puis sélectionnez **Identité**. La liste des domaines s’ouvre. Cliquez sur le lien **Configurer** en regard de votre domaine. Suivez ensuite la procédure de la section **Configuration de l’authentification unique requise**. Pour plus d’informations, consultez la section relative à la [configuration d’un domaine](https://helpx.adobe.com/enterprise/using/set-up-domain.html).

    ![Paramètres](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Paramètres")

    a. Cliquez sur **Parcourir** pour télécharger le certificat obtenu à partir d’Azure AD dans **Certificat IDP**.

    b. Dans la zone de texte **IDP issuer**, placez la valeur de **l’ID d’entité SAML** que vous avez copiée depuis la section **Configurer l’authentification** du portail Azure.

    c. Dans la zone de texte **IDP Login URL**, placez la valeur de **l’URL du service SSO SAML** que vous avez copiée depuis la section **Configurer l’authentification** du portail Azure.

    d. Sélectionnez **HTTP - Redirection** en tant que **Liaison IDP**.

    e. Sélectionnez **Adresse de messagerie** en tant que **Paramètre de connexion utilisateur**.

    f. Cliquez sur le bouton **Enregistrer** .

3. Le tableau de bord présente maintenant le fichier XML **« Télécharger les métadonnées »**. Il contient les URL EntityDescriptor et AssertionConsumerService d’Adobe. Ouvrez le fichier et configurez-les dans l’application Azure AD.

    ![Configurer l’authentification unique côté application](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Utilisez la valeur EntityDescriptor que Adobe vous a fournie pour **Identificateur** dans la boîte de dialogue **Configurer les paramètres d’application**.

    b. Utilisez la valeur AssertionConsumerService que Adobe vous a fournie pour **URL de réponse** dans la boîte de dialogue **Configurer les paramètres d’application**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Creative Cloud.

1. Dans le Portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Adobe Creative Cloud**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Adobe Creative Cloud**.

    ![Lien Adobe Creative Cloud dans la liste Applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-adobe-creative-cloud-test-user"></a>Créer un utilisateur de test Adobe Creative Cloud

Pour permettre aux utilisateurs Azure AD de se connecter à Adobe Creative Cloud, vous devez les approvisionner dans Adobe Creative Cloud. Dans le cas d’Adobe Creative Cloud, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous au site [Adobe Admin Console](https://adminconsole.adobe.com) en tant qu’administrateur.

2. Ajoutez l’utilisateur au sein de la console Adobe en tant qu’ID fédéré et attribuez-le à un profil de produits. Pour plus d’informations sur l’ajout des utilisateurs, consultez la section [Ajouter des utilisateurs dans Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers). 

3. À ce stade, tapez votre adresse de messagerie/nom d’utilisateur principal dans le formulaire de connexion Adobe, appuyez sur la touche de tabulation, et vous devez être fédéré à Azure AD :
    * Accès Web : www.adobe.com > connexion
    * Dans l’utilitaire de l’application de bureau > connexion
    * Dans l’application > aide > connexion

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Adobe Creative Cloud dans le volet d’accès, vous devez être connecté automatiquement à l’application Adobe Creative Cloud pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Configurer un domaine (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Configurer Azure pour une utilisation avec l’authentification unique Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)