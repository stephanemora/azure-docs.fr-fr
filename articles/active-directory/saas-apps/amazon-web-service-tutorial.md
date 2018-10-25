---
title: 'Didacticiel : Intégration d’Azure Active Directory à Amazon Web Services (AWS) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 8e91fbf0befaef9088e9afaa6e69c0cb29ad4858
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363757"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Didacticiel : Intégration d’Azure Active Directory à Amazon Web Services (AWS)

Dans ce tutoriel, vous allez apprendre à intégrer Amazon Web Services à Azure Active Directory (Azure AD).

L’intégration de Amazon Web Services (AWS) dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Amazon Web Services (AWS).
- Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Amazon Web Services (AWS) (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Vous pouvez configurer plusieurs identificateurs pour plusieurs instances comme indiqué ci-après. 

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Avec ces valeurs, Azure AD supprime la valeur de **#** et envoie la valeur correcte `https://signin.aws.amazon.com/saml` en tant qu’URL d’audience dans le jeton SAML.

**Nous vous recommandons d’utiliser cette approche pour les raisons suivantes :**

a. Chaque application vous fournit le certificat X509 unique ; ainsi, chaque instance peut avoir une date d’expiration de certificat différente, que vous pouvez gérer en fonction de chaque compte AWS. La substitution de certificat globale est facilitée dans ce cas.

b. Vous pouvez activer l’attribution d’utilisateurs avec une application AWS dans Azure AD, puis notre service extrait tous les rôles de ce compte AWS. Vous n’êtes pas obligé d’ajouter ou de mettre à jour manuellement les rôles AWS sur l’application.

c. Vous pouvez assigner un propriétaire spécifique pour l’application ou gérer l’application directement dans Azure AD.

> [!Note]
> Vérifiez que vous utilisez uniquement une application de galerie.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Amazon Web Services (AWS), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Amazon Web Services (AWS) avec authentification unique

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Si vous souhaitez intégrer plusieurs comptes AWS à un compte Azure pour l’authentification unique, consultez [cet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) article.

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Amazon Web Services (AWS) à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Ajout d’Amazon Web Services (AWS) à partir de la galerie
Pour configurer l’intégration d’Amazon Web Services (AWS) avec Azure AD, vous devez ajouter Amazon Web Services (AWS), disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Amazon Web Services (AWS) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![image](./media/amazon-web-service-tutorial/selectazuread.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![image](./media/amazon-web-service-tutorial/a_select_app.png)
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![image](./media/amazon-web-service-tutorial/a_new_app.png)

4. Dans la zone de recherche, tapez **Amazon Web Services (AWS)**, sélectionnez **Amazon Web Services (AWS)** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir quelle est l’équivalence de l’utilisateur Amazon Web Services (AWS) dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Amazon Web Services (AWS) associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), vous avez besoin de suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)** pour avoir un équivalent de Britta Simon dans Amazon Web Services (AWS) lié à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application Amazon Web Services (AWS).

**Pour configurer l’authentification unique Azure AD avec Amazon Web Services (AWS), procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Amazon Web Services (AWS)**, sélectionnez **Authentification unique**.

    ![image](./media/amazon-web-service-tutorial/B1_B2_Select_SSO.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** afin d’activer l’authentification unique.

    ![image](./media/amazon-web-service-tutorial/b1_b2_saml_sso.png)

3. Sur la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![image](./media/amazon-web-service-tutorial/b1-domains_and_urlsedit.png)

4. Dans la section **Configuration SAML de base**, l’utilisateur n’aura à effectuer aucune étape, car l’application est déjà intégrée à Azure.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Quand vous configurez plusieurs instances, indiquez la valeur d’identificateur. À partir de la deuxième instance, indiquez la valeur d’identificateur dans le format suivant. Utilisez un signe **#** pour spécifier une valeur de SPN unique. 

    `https://signin.aws.amazon.com/saml#2`

    ![Informations d’authentification unique dans Domaine et URL Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. L’application Amazon Web Services (AWS) attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur et revendications** dans la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur et revendications**.

    ![image](./media/amazon-web-service-tutorial/i4-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur et revendications**, configurez le jeton SAML comme sur l’image ci-dessus, puis effectuez les étapes suivantes :
    
    | NOM  | Attribut source  | Espace de noms |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rôle            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | [Indiquez une valeur comprise entre 900 secondes (15 minutes) et 43 200 secondes (12 heures)] |  https://aws.amazon.com/SAML/Attributes |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](./media/amazon-web-service-tutorial/i2-attribute.png)

    ![image](./media/amazon-web-service-tutorial/i3-attribute.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Entrez la valeur **Espace de noms**.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **Enregistrer**.

8. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **XML de métadonnées de fédération**, puis enregistrez-le sur votre ordinateur.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

9. Dans une autre fenêtre de navigateur, connectez-vous au site de votre entreprise Amazon Web Services (AWS) en tant qu’administrateur.

10. Cliquez sur **AWS Home** (Accueil AWS).

    ![Démarrage dans la configuration de l’authentification unique][11]

11. Cliquez sur **Gestion de l’identité et de l’accès**.

    ![Identité dans la configuration de l’authentification unique][12]

12. Cliquez sur **Identity Providers**, puis sur **Create Provider**.

    ![Fournisseur dans la configuration de l’authentification unique][13]

13. Dans la page **Configure Provider** , procédez comme suit :

    ![Boîte de dialogue de la configuration de l’authentification unique][14]

    a. Pour **Provider Type**, sélectionnez **SAML**.

    b. Dans la zone de texte **Provider Name** (Nom de fournisseur), tapez un nom de fournisseur (par exemple : *WAAD*).

    c. Pour charger le **fichier de métadonnées** téléchargé à partir du portail Azure, cliquez sur **Choose File** (Choisir un fichier).

    d. Cliquez sur **Next Step**.

14. Dans la page **Verify Provider Information**, cliquez sur **Create**.

    ![Vérification dans la configuration de l’authentification unique][15]

15. Cliquez sur **Roles** (Rôles), puis sur **Create role** (Créer un rôle).

    ![Rôles dans la configuration de l’authentification unique][16]

16. Dans la page **Create role** (Créer un rôle), effectuez les étapes suivantes :  

    ![Approbation dans la configuration de l’authentification unique][19]

    a. Sélectionnez **SAML 2.0 federation** (Fédération SAML 2.0) dans **Select type of trusted entity** (Sélectionnez un type d’entité approuvé).

    b. Dans la section **Choose a SAML 2.0 Provider** (Choisir un fournisseur SAML 2.0), sélectionnez le **fournisseur SAML** que vous avez déjà créé (par exemple : *WAAD*).

    c. Sélectionnez **Allow programmatic and AWS Management Console access** (Autoriser l’accès par programme à AWS Management Console).
  
    d. Cliquez sur **Next: Permissions** (Suivant : Autorisations).

17. Dans la boîte de dialogue **Joindre des stratégies d’autorisation**, vous n’avez pas besoin de joindre une stratégie. Cliquez sur **Suivant : Revue**.  

    ![Stratégie dans la configuration de l’authentification unique][33]

18. Dans la boîte de dialogue **Review** , procédez comme suit :

    ![Révision dans la configuration de l’authentification unique][34]

    a. Dans la zone de texte **Role name** (Nom de rôle), entrez votre nom de rôle.

    b. Dans la zone de texte **Role description** (Description du rôle), entrez la description.

    c. Cliquez sur **Create Role**.

    d. Créez autant de rôles que nécessaire et mappez-les vers le fournisseur d’identité.

19. Utilisez les informations d’identification du compte de service AWS pour récupérer les rôles depuis le compte AWS dans Attribution d’utilisateurs d’Azure AD. Pour ce faire, ouvrez la page d’accueil de la console AWS.

20. Cliquez sur **Services** (Services) -> **Security,Identity& Compliance** (Sécurité, identité et conformité) -> **IAM**.

    ![Extraction des rôles à partir du compte AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

21. Sélectionnez l’onglet **Policies** (Stratégies) dans la section IAM.

    ![Extraction des rôles à partir du compte AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

22. Créez une stratégie en cliquant sur **Create policy** (Créer une stratégie) pour récupérer les rôles du compte AWS dans Attribution d’utilisateurs Azure AD.

    ![Création d’une stratégie](./media/amazon-web-service-tutorial/fetchingrole3.png)

23. Créez votre propre stratégie pour extraire tous les rôles à partir de comptes AWS en procédant comme suit :

    ![Création d’une stratégie](./media/amazon-web-service-tutorial/policy1.png)

    a. Dans la section **Créer une stratégie**, cliquez sur l’onglet **JSON**.

    b. Dans le document de stratégie, ajoutez l’extrait au format JSON ci-dessous.

    ```

    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",

    "Action": [

    "iam:ListRoles"

    ],

    "Resource": "*"

    }

    ]

    }

    ```

    c. Cliquez sur le bouton **Vérifier la stratégie** pour valider la stratégie.

    ![Définition de la nouvelle stratégie](./media/amazon-web-service-tutorial/policy5.png)

24. Définissez la **nouvelle stratégie** en suivant ces étapes :

    ![Définition de la nouvelle stratégie](./media/amazon-web-service-tutorial/policy2.png)

    a. Indiquez **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD) comme **Policy Name** (Nom de stratégie).

    b. Vous pouvez fournir une **Description** (Description) pour la stratégie, par exemple **This policy will allow to fetch the roles from AWS accounts** (Cette stratégie permettra de récupérer les rôles à partir de comptes AWS).

    c. Cliquez sur le bouton **Créer une stratégie**.

25. Créez un compte d’utilisateur dans AWS IAM Service en suivant ces étapes :

    a. Cliquez sur **Users** (Utilisateurs) dans la console AWS IAM.

    ![Définition de la nouvelle stratégie](./media/amazon-web-service-tutorial/policy3.png)

    b. Cliquez sur le bouton **Add user** (Ajouter un utilisateur) permettant de créer un utilisateur.

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/policy4.png)

    c. Dans la section **Add User** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/adduser1.png)

    * Entrez **AzureADRoleManager** (GestionnaireDeRôlesAzureAD) comme nom d’utilisateur.

    * Pour le type d’accès, sélectionnez l’option **Programmatic access** (Accès par programmation). Ainsi, l’utilisateur peut appeler les API et extraire les rôles du compte AWS.

    * Cliquez sur le bouton **Next Permissions** (Autorisations suivantes) dans le coin inférieur droit.

26. À présent, créez une stratégie pour cet utilisateur en effectuant les étapes suivantes :

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/adduser2.png)

    a. Cliquez sur le bouton **Attach existing policies directly** (Joindre les stratégies existantes directement).

    b. Recherchez la stratégie nouvellement créée dans la section du filtre **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD).

    c. Sélectionnez la **stratégie**, puis cliquez sur le bouton **Next: Review** (Suivant : Vérification).

27. Passez en revue la stratégie pour l’utilisateur joint en suivant ces étapes :

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/adduser3.png)

    a. Vérifiez le nom d’utilisateur, le type d’accès et la stratégie mappée à l’utilisateur.

    b. Cliquez sur le bouton **Create user** (Créer un utilisateur) dans le coin inférieur droit pour créer l’utilisateur.

28. Téléchargez les informations d’identification d’un utilisateur en procédant comme suit :

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copiez les **Access key ID** (ID de la clé d’accès) et **Secret access key** (Clé d’accès secrète) de l’utilisateur.

    b. Dans la section d’attribution d’utilisateurs d’Azure AD, entrez ces informations d’identification pour extraire les rôles à partir de la console AWS.

    c. Cliquez sur le bouton **Close** (Fermer) au bas de l’écran.

29. Accédez à la section **User Provisioning** (Attribution d’utilisateurs) de l’application Amazon Web Services dans le portail de gestion Azure AD.

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/provisioning.png)

30. Entrez la **Clé d’accès** et le **Secret** dans les champs **Clé secrète client** et **Jeton secret** respectivement.

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Entrez la clé d’accès utilisateur AWS dans le champ **clientsecret**.

    b. Entrez le secret d’utilisateur AWS dans le champ **Jeton secret**.

    c. Cliquez sur le bouton **Tester la connexion** pour vérifier que cette connexion est correctement établie.

    d. Enregistrez le paramètre en cliquant sur le bouton **Enregistrer** en haut.

31. À présent, assurez-vous de régler sur **Actif** l’état de mise en service dans la section Paramètres, en agissant sur le commutateur, puis en cliquant sur le bouton **Enregistrer** en haut.

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le portail Azure, dans le volet gauche, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![image](./media/amazon-web-service-tutorial/d_users_and_groups.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![image](./media/amazon-web-service-tutorial/d_adduser.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![image](./media/amazon-web-service-tutorial/d_userproperties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Créer un utilisateur de test Amazon Web Services (AWS)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Amazon Web Services (AWS). Comme Amazon Web Services (AWS) ne nécessite pas de création d’utilisateur dans son système pour l’authentification unique, vous n’avez aucune action à accomplir ici.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Amazon Web Services (AWS).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![image](./media/amazon-web-service-tutorial/d_all_applications.png)

2. Dans la liste des applications, sélectionnez **Amazon Web Services (AWS)**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![image](./media/amazon-web-service-tutorial/d_leftpaneusers.png)

4. Sélectionnez le bouton **Ajouter**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![image](./media/amazon-web-service-tutorial/d_assign_user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_users.png)

6. Dans la boîte de dialogue **Sélectionner un rôle**, choisissez le rôle d’utilisateur approprié dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_roles.png)

    >[!NOTE]
    >Une fois l’attribution d’utilisateurs activée avec l’application, l’obtention de tous les rôles à partir d’AWS (Amazon Web Services) devrait prendre 30 minutes. Vous devez ensuite actualiser la page ; les rôles destinés à l’utilisateur apparaissent alors pendant l’affectation de l’application aux utilisateurs et groupes.

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_assign.png)
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Amazon Web Services (AWS) dans le volet d’accès, vous devez être connecté automatiquement à votre application Amazon Web Services (AWS).
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
