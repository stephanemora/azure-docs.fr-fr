---
title: 'Didacticiel : Intégration d’Azure Active Directory à Amazon Web Services (AWS) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f58c8c3ea76cf44730a54897e6b77ef6e648a49
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901659"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutoriel : Intégration d’Azure Active Directory à Amazon Web Services (AWS)

Dans ce tutoriel, vous allez apprendre à intégrer Amazon Web Services à Azure Active Directory (Azure AD).
L’intégration de Amazon Web Services (AWS) dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Amazon Web Services (AWS).
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Amazon Web Services (AWS) (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

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

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Amazon Web Services (AWS) avec authentification unique

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Si vous souhaitez intégrer plusieurs comptes AWS à un compte Azure pour l’authentification unique, consultez [cet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) article.

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Amazon Web Services (AWS) prend en charge l’authentification unique démarrée par **le fournisseur de services et le fournisseur d’identité**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Ajout d’Amazon Web Services (AWS) à partir de la galerie

Pour configurer l’intégration d’Amazon Web Services (AWS) avec Azure AD, vous devez ajouter Amazon Web Services (AWS), disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Amazon Web Services (AWS) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Amazon Web Services (AWS)**, sélectionnez **Amazon Web Services (AWS)** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Amazon Web Services (AWS) dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), en tirant parti d’un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur Amazon Web Services (AWS) associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), vous avez besoin de suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** pour avoir un équivalent de Britta Simon dans Amazon Web Services (AWS) lié à sa représentation dans Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Amazon Web Services (AWS), effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Amazon Web Services (AWS)**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, l’application est préconfigurée et les URL nécessaires sont déjà préremplies avec Azure. L’utilisateur doit enregistrer la configuration en cliquant sur le bouton **Enregistrer**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Quand vous configurez plusieurs instances, indiquez la valeur d’identificateur. À partir de la deuxième instance, indiquez la valeur d’identificateur dans le format suivant. Utilisez un signe **#** pour spécifier une valeur de SPN unique.

    `https://signin.aws.amazon.com/saml#2`

    ![Informations d’authentification unique dans Domaine et URL Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. L’application Amazon Web Services (AWS) attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![image](common/edit-attribute.png)

7. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    | Nom  | Attribut source  | Espace de noms |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rôle            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | [Indiquez une valeur comprise entre 900 secondes (15 minutes) et 43 200 secondes (12 heures)] |  https://aws.amazon.com/SAML/Attributes |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la zone de texte **Espace de noms**, indiquez la valeur d’espace de noms pour cette ligne.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

8. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

9. Dans la section **Configurer Amazon Web Services (AWS)**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Configurer l’authentification unique Amazon Web Services (AWS)

1. Dans une autre fenêtre de navigateur, connectez-vous au site de votre entreprise Amazon Web Services (AWS) en tant qu’administrateur.

2. Cliquez sur **AWS Home** (Accueil AWS).

    ![Démarrage dans la configuration de l’authentification unique][11]

3. Cliquez sur **Gestion de l’identité et de l’accès**.

    ![Identité dans la configuration de l’authentification unique][12]

4. Cliquez sur **Identity Providers**, puis sur **Create Provider**.

    ![Fournisseur dans la configuration de l’authentification unique][13]

5. Dans la page **Configure Provider** , procédez comme suit :

    ![Boîte de dialogue de la configuration de l’authentification unique][14]

    a. Pour **Provider Type**, sélectionnez **SAML**.

    b. Dans la zone de texte **Provider Name** (Nom de fournisseur), tapez un nom de fournisseur (par exemple : *WAAD*).

    c. Pour charger le **fichier de métadonnées** téléchargé à partir du portail Azure, cliquez sur **Choose File** (Choisir un fichier).

    d. Cliquez sur **Next Step**.

6. Dans la page **Verify Provider Information**, cliquez sur **Create**.

    ![Vérification dans la configuration de l’authentification unique][15]

7. Cliquez sur **Roles** (Rôles), puis sur **Create role** (Créer un rôle).

    ![Rôles dans la configuration de l’authentification unique][16]

8. Dans la page **Create role** (Créer un rôle), effectuez les étapes suivantes :  

    ![Approbation dans la configuration de l’authentification unique][19]

    a. Sélectionnez **SAML 2.0 federation** (Fédération SAML 2.0) dans **Select type of trusted entity** (Sélectionnez un type d’entité approuvé).

    b. Dans la section **Choose a SAML 2.0 Provider** (Choisir un fournisseur SAML 2.0), sélectionnez le **fournisseur SAML** que vous avez déjà créé (par exemple : *WAAD*)

    c. Sélectionnez **Allow programmatic and AWS Management Console access** (Autoriser l’accès par programme à AWS Management Console).
  
    d. Cliquez sur **Suivant : Autorisations**.

9. Dans la boîte de dialogue **Attach Permissions Policies** (Attacher des stratégies d’autorisation), attachez une stratégie appropriée pour votre organisation. Cliquez sur **Suivant : Vérification**).  

    ![Stratégie dans la configuration de l’authentification unique][33]

10. Dans la boîte de dialogue **Review** , procédez comme suit :

    ![Révision dans la configuration de l’authentification unique][34]

    a. Dans la zone de texte **Role name** (Nom de rôle), entrez votre nom de rôle.

    b. Dans la zone de texte **Role description** (Description du rôle), entrez la description.

    c. Cliquez sur **Create Role**.

    d. Créez autant de rôles que nécessaire et mappez-les vers le fournisseur d’identité.

11. Utilisez les informations d’identification du compte de service AWS pour récupérer les rôles depuis le compte AWS dans Attribution d’utilisateurs d’Azure AD. Pour ce faire, ouvrez la page d’accueil de la console AWS.

12. Cliquez sur **Services** (Services) -> **Security,Identity& Compliance** (Sécurité, identité et conformité) -> **IAM**.

    ![Extraction des rôles à partir du compte AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Sélectionnez l’onglet **Policies** (Stratégies) dans la section IAM.

    ![Extraction des rôles à partir du compte AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Créez une stratégie en cliquant sur **Create policy** (Créer une stratégie) pour récupérer les rôles du compte AWS dans Attribution d’utilisateurs Azure AD.

    ![Création d’une stratégie](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Créez votre propre stratégie pour extraire tous les rôles à partir de comptes AWS en procédant comme suit :

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

16. Définissez la **nouvelle stratégie** en suivant ces étapes :

    ![Définition de la nouvelle stratégie](./media/amazon-web-service-tutorial/policy2.png)

    a. Indiquez **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD) comme **Policy Name** (Nom de stratégie).

    b. Vous pouvez fournir une **Description** (Description) pour la stratégie, par exemple **This policy will allow to fetch the roles from AWS accounts** (Cette stratégie permettra de récupérer les rôles à partir de comptes AWS).

    c. Cliquez sur le bouton **Créer une stratégie**.

17. Créez un compte d’utilisateur dans AWS IAM Service en suivant ces étapes :

    a. Cliquez sur **Users** (Utilisateurs) dans la console AWS IAM.

    ![Définition de la nouvelle stratégie](./media/amazon-web-service-tutorial/policy3.png)

    b. Cliquez sur le bouton **Add user** (Ajouter un utilisateur) permettant de créer un utilisateur.

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/policy4.png)

    c. Dans la section **Add User** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/adduser1.png)

    * Entrez **AzureADRoleManager** (GestionnaireDeRôlesAzureAD) comme nom d’utilisateur.

    * Pour le type d’accès, sélectionnez l’option **Programmatic access** (Accès par programmation). Ainsi, l’utilisateur peut appeler les API et extraire les rôles du compte AWS.

    * Cliquez sur le bouton **Next Permissions** (Autorisations suivantes) dans le coin inférieur droit.

18. À présent, créez une stratégie pour cet utilisateur en effectuant les étapes suivantes :

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/adduser2.png)

    a. Cliquez sur le bouton **Attach existing policies directly** (Joindre les stratégies existantes directement).

    b. Recherchez la stratégie nouvellement créée dans la section du filtre **AzureAD_SSOUserRole_Policy** (Stratégie_RôleUtilisateurAuthentificationUnique_AzureAD).

    c. Sélectionnez la **stratégie**, puis cliquez sur le bouton **Next: Review** (Suivant : Vérification).

19. Passez en revue la stratégie pour l’utilisateur joint en suivant ces étapes :

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/adduser3.png)

    a. Vérifiez le nom d’utilisateur, le type d’accès et la stratégie mappée à l’utilisateur.

    b. Cliquez sur le bouton **Create user** (Créer un utilisateur) dans le coin inférieur droit pour créer l’utilisateur.

20. Téléchargez les informations d’identification d’un utilisateur en procédant comme suit :

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copiez les **Access key ID** (ID de la clé d’accès) et **Secret access key** (Clé d’accès secrète) de l’utilisateur.

    b. Dans la section d’attribution d’utilisateurs d’Azure AD, entrez ces informations d’identification pour extraire les rôles à partir de la console AWS.

    c. Cliquez sur le bouton **Close** (Fermer) au bas de l’écran.

21. Accédez à la section **User Provisioning** (Attribution d’utilisateurs) de l’application Amazon Web Services dans le portail de gestion Azure AD.

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/provisioning.png)

22. Entrez la **Clé d’accès** et le **Secret** dans les champs **Clé secrète client** et **Jeton secret** respectivement.

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Entrez la clé d’accès utilisateur AWS dans le champ **clientsecret**.

    b. Entrez le secret d’utilisateur AWS dans le champ **Jeton secret**.

    c. Cliquez sur le bouton **Tester la connexion** pour vérifier que cette connexion est correctement établie.

    d. Enregistrez le paramètre en cliquant sur le bouton **Enregistrer** en haut.

23. À présent, assurez-vous de régler sur **Actif** l’état de mise en service dans la section Paramètres, en agissant sur le commutateur, puis en cliquant sur le bouton **Enregistrer** en haut.

    ![Ajouter un utilisateur](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@votredomaineentreprise.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autoriserz Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Amazon Web Services (AWS).

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Amazon Web Services (AWS)**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, tapez et sélectionnez **Amazon Web Services (AWS)**.

    ![Lien Amazon Web Services (AWS) dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-amazon-web-services-aws-test-user"></a>Créer un utilisateur de test Amazon Web Services (AWS)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Amazon Web Services (AWS). Comme Amazon Web Services (AWS) ne nécessite pas de création d’utilisateur dans son système pour l’authentification unique, vous n’avez aucune action à accomplir ici.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Amazon Web Services (AWS) dans le volet d’accès, vous devez être connecté automatiquement à l’application Amazon Web Services (AWS) pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problèmes connus

 * Dans la section **Provisionnement**, la sous-section **Mappages** affiche un message « Chargement en cours... » et n’affiche jamais les mappages d’attributs. Le seul workflow de provisionnement pris en charge aujourd’hui est l’importation des rôles à partir d’AWS dans Azure AD pour la sélection lors de l’affectation de groupe/utilisateur. Pour cela, les mappages d’attributs sont prédéterminés et non configurables.
 
 * La section **Provisionnement** prend uniquement en charge l’entrée d’un ensemble d’informations d’identification pour un locataire AWS à la fois. Tous les rôles importés sont écrits dans la propriété appRoles de l’[objet servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) d’Azure AD pour le locataire AWS. Vous pouvez ajouter plusieurs locataires AWS (représentés par des objets servicePrincipals) à Azure AD à partir de la galerie pour le provisionnement, mais il existe un problème connu : il est impossible d’écrire automatiquement tous les rôles importés à partir des objets servicePrincipals AWS utilisés pour le provisionnement dans le servicePrincipal unique utilisé pour l’authentification unique. En guise de solution de contournement, vous pouvez utiliser l’[API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) pour extraire tous les appRoles importés dans chaque servicePrincipal AWS où le provisionnement est configuré. Ces chaînes de rôle peuvent être ajoutées par la suite au servicePrincipal AWS où l’authentification unique est configurée.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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
