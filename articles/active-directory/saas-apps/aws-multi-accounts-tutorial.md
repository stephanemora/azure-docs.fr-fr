---
title: 'Didacticiel : Intégration d’Azure Active Directory à Amazon Web Services (AWS) pour connecter plusieurs comptes | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure AD et plusieurs comptes Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb528d71b94449b282947a487e4fc79b343df778
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195903"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Didacticiel : Intégration d’Azure Active Directory à plusieurs comptes Amazon Web Services (AWS)

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) à plusieurs comptes Amazon Web Services (AWS).

L’intégration de Amazon Web Services (AWS) dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Amazon Web Services (AWS).
- Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Amazon Web Services (AWS) (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) dans la liste des résultats](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Nous vous déconseillons de connecter une application AWS à tous vos comptes AWS. Nous vous recommandons plutôt d’utiliser [cette](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) approche pour configurer plusieurs instances du compte AWS à plusieurs instances d’applications AWS dans Azure AD.

**Nous vous déconseillons d’utiliser cette approche pour les raisons suivantes :**

* Vous devez utiliser l’approche de l’Afficheur Graph pour appliquer tous les rôles à l’application. Nous vous déconseillons d’utiliser l’approche du fichier manifeste.

* Des clients nous ont signalé qu’après l’ajout d’environ 1 200 rôles d’application pour une seule application AWS, toute opération sur l’application commençait par lever les erreurs liées à la taille. Il existe une limite inconditionnelle de taille sur l’objet d’application.

* Vous devez mettre à jour manuellement le rôle quand les rôles sont ajoutés aux comptes, ce qui est une approche de type remplacement et non de type ajout malheureusement. En outre, si vos comptes se multiplient, une relation n x n s’instaure entre les comptes et les rôles.

* Tous les comptes AWS utilisent le même fichier XML de métadonnées de fédération. Ainsi, au moment de la substitution de certificat, vous devez procéder à ce gros travail de mise à jour du certificat sur tous les comptes AWS en même temps.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Amazon Web Services (AWS), vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Amazon Web Services (AWS) avec authentification unique

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Amazon Web Services (AWS) prend en charge l’authentification unique démarrée par **le fournisseur de services et le fournisseur d’identité**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Ajout d’Amazon Web Services (AWS) à partir de la galerie

Pour configurer l’intégration d’Amazon Web Services (AWS) avec Azure AD, vous devez ajouter Amazon Web Services (AWS), disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Amazon Web Services (AWS) à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Amazon Web Services (AWS)** , sélectionnez **Amazon Web Services (AWS)** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Amazon Web Services (AWS) dans la liste des résultats](common/search-new-app.png)

5. Une fois l’application ajoutée, allez à la page **Propriétés** et copiez **l’ID de l’objet**.

    ![Amazon Web Services (AWS) dans la liste des résultats](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir quelle est l’équivalence de l’utilisateur Amazon Web Services (AWS) dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Amazon Web Services (AWS) associé doit être établie.

Dans Amazon Web Services (AWS), affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Amazon Web Services (AWS), vous avez besoin de suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application Amazon Web Services (AWS).

**Pour configurer l’authentification unique Azure AD avec Amazon Web Services (AWS), procédez comme suit :**

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Amazon Web Services (AWS)** , sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, l’utilisateur n’a rien à faire, car l’application est déjà intégrée à Azure.

    ![image](common/preintegrated.png)

5. L’application Amazon Web Services (AWS) attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur et revendications** dans la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur et revendications**.

    ![image](common/edit-attribute.png)

6. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

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

7. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **XML de métadonnées de fédération**, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

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

11. Déconnectez-vous du compte AWS actuel et connectez-vous avec un autre compte pour lequel vous souhaitez configurer l’authentification unique avec Azure AD.

12. Exécutez les étapes 2 à 10 pour créer plusieurs rôles à configurer pour ce compte. Si vous avez plus de deux comptes, exécutez les mêmes étapes pour tous les comptes afin de créer des rôles pour ceux-ci.

13. Une fois tous les rôles créés dans les comptes, ils apparaissent dans la liste **Rôles** pour ces comptes.

    ![Configuration des rôles](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Vous devez capturer l’ensemble des valeurs Role ARN et Trusted Entities pour tous les rôles dans tous les comptes, que nous devons mapper manuellement avec l’application Azure AD.

15. Cliquez sur les rôles pour copier les valeurs **Role ARN** et **Trusted Entities**. Ces valeurs sont requises pour tous les rôles à créer dans Azure AD.

    ![Configuration des rôles](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Exécutez l’étape ci-dessus pour tous les rôles dans tous les comptes, et stockez-les au format **Role ARN,Trusted entities** dans un bloc-notes.

17. Ouvrez [l’Explorateur graphique Azure AD](https://developer.microsoft.com/graph/graph-explorer) dans une autre fenêtre.

    a. Connectez-vous au site Graph Explorer en utilisant les informations d’identification d’administrateur global/coadministrateur de votre locataire.

    b. Vous devez disposer des autorisations suffisantes pour créer les rôles. Cliquez sur **modifier les autorisations** pour obtenir les autorisations requises.

    ![Boîte de dialogue de l’Explorateur graphique](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Sélectionnez les autorisations suivantes dans la liste (si vous ne l’avez pas déjà fait) et cliquez sur « Modifier les autorisations » 

    ![Boîte de dialogue de l’Explorateur graphique](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Vous serez alors invité à vous connecter à nouveau et à accepter le consentement. Après avoir accepté le consentement, vous êtes connecté à Graph Explorer à nouveau.

    e. Modifiez la liste déroulante de version sur **bêta**. Pour extraire tous les principaux du service à partir de votre locataire, utilisez la requête suivante :

    `https://graph.microsoft.com/beta/servicePrincipals`

    Si vous utilisez plusieurs répertoires, alors vous pouvez utiliser le modèle suivant, qui contient votre domaine principal `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Boîte de dialogue de l’Explorateur graphique](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Dans la liste des principaux du service extraits, obtenez celui que vous souhaitez modifier. Vous pouvez également utiliser les touches Ctrl + F pour rechercher l’application dans la liste les principaux du service. Vous pouvez utiliser la requête suivante avec **l’ID de l’objet** que vous avez copié à partir de la page Propriétés d’Azure AD pour accéder au principal du service respectif.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Boîte de dialogue de l’Explorateur graphique](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrayez la propriété appRoles à partir de l’objet du principal du service.

    ![Boîte de dialogue de l’Explorateur graphique](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. À présent, vous devez générer de nouveaux rôles pour votre application. 

    i. Vous trouverez en-dessous de JSON un exemple d’objet appRoles. Créez un objet similaire pour ajouter les rôles que vous voulez pour votre application.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Vous pouvez uniquement ajouter de nouveaux rôles après le **msiam_access** de l’opération de correction. En outre, vous pouvez ajouter autant de rôles que vous le souhaitez selon les besoins de votre organisation. Azure AD envoie la **valeur** de ces rôles conformément à la valeur de revendication dans la réponse SAML.

    j. Revenez à Graph Explorer et modifiez la méthode de **GET** à **PATCH**. Corrigez l’objet du principal du service pour obtenir les rôles souhaités en mettant à jour la propriété appRoles similaire à celle affichée dans l’exemple ci-dessus. Cliquez sur **Exécuter la requête** pour exécuter l’opération de correction. Un message de réussite confirme la création du rôle pour votre application Amazon Web Services.

    ![Boîte de dialogue de l’Explorateur graphique](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Une fois le principal du service corrigé avec d’autres rôles, vous pouvez assigner des utilisateurs/groupes aux rôles respectifs. Pour ce faire, accédez au portail et à l’application Amazon Web Services. Cliquez sur l’onglet **Utilisateurs et groupes** dans la partie supérieure.

19. Nous vous recommandons de créer de nouveaux groupes pour chaque rôle AWS afin d’attribuer ce rôle spécifique dans ce groupe. Notez qu’il s’agit de mappage un-à-un pour un groupe à un rôle. Vous pouvez ensuite ajouter des membres qui appartiennent à ce groupe.

20. Une fois que les groupes sont créés, sélectionnez le groupe et affectez-le à l’application.

    ![Configurer l’authentification unique Add](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Les groupes imbriqués ne sont pas pris en charge lors de l’assignation de groupes.

21. Pour assigner le rôle au groupe, sélectionnez le rôle et cliquez sur le bouton **Assigner** dans la partie inférieure de la page.

    ![Configurer l’authentification unique Add](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Notez que vous devez actualiser votre session dans le portail Azure pour afficher les nouveaux rôles.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Amazon Web Services (AWS) dans le volet d’accès, vous devez obtenir la page de l’application Amazon Web Services (AWS) avec l’option de sélectionner le rôle.

![Configurer l’authentification unique Add](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Vous pouvez également vérifier la réponse SAML pour afficher les rôles transmis en tant que revendications.

![Configurer l’authentification unique Add](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Procédure de configuration de l’approvisionnement à l’aide des API Microsoft Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
