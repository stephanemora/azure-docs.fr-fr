---
title: 'Didacticiel : Intégration d’Azure Active Directory à SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 97e77fdbb352e1fcf66044f48e17cb19a8aa2e1d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042144"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Didacticiel : Intégration d’Azure Active Directory à SAP Cloud Platform Identity Authentication

Dans ce didacticiel, vous allez apprendre à intégrer SAP Cloud Platform Identity Authentication à Azure Active Directory (Azure AD). SAP Cloud Platform Identity Authentication est utilisé comme IdP proxy pour accéder aux applications SAP qui utilisent Azure AD comme IdP principal.

Quand vous intégrez SAP Cloud Platform Identity Authentication avec Azure AD, vous bénéficiez des avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès aux applications SAP.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement aux applications SAP avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez l’article [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SAP Cloud Platform Identity Authentication, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement compatible avec l’authentification unique pour SAP Cloud Platform Identity Authentication

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, obtenez [un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

Avant de rentrer dans les détails techniques, il est essentiel de comprendre les concepts que vous allez voir. SAP Cloud Platform Identity Authentication et les services de fédération Active Directory (AD FS) vous permettent d’implémenter l’authentification unique entre les applications ou les services protégés par Azure AD (comme IdP) et les applications et services SAP protégés par SAP Cloud Platform Identity Authentication.

Actuellement, SAP Cloud Platform Identity Authentication remplit la fonction de fournisseur d’identité proxy pour les applications SAP. Azure Active Directory joue en retour le rôle de fournisseur d’identité principal dans cette configuration. 

Le schéma suivant illustre cette relation :

![Création d’un utilisateur de test Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Avec cette configuration, votre client SAP Cloud Platform Identity Authentication est configuré en tant qu’application approuvée dans Azure Active Directory. 

Toutes les applications et tous les services SAP que vous souhaitez protéger de cette façon sont ensuite configurés dans la console de gestion SAP Cloud Platform Identity Authentication.

Ainsi, l’autorisation d’accorder l’accès aux services et applications SAP doit avoir lieu dans SAP Cloud Platform Identity Authentication (par opposition à Azure Active Directory).

Quand vous configurez SAP Cloud Platform Identity Authentication en tant qu’application par le biais de la Place de Marché Azure Active Directory, vous n’avez pas besoin de configurer des revendications ou des assertions SAML.

>[!NOTE] 
>Actuellement, seule l’authentification unique web a été testée par les deux parties. Les flux nécessaires à la communication application-API ou API-API devraient fonctionner mais n’ont pas encore été testés. Ils seront testés durant les activités à venir.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Ajouter SAP Cloud Platform Identity Authentication à partir de la galerie
Pour configurer l’intégration de SAP Cloud Platform Identity Authentication dans Azure AD, vous devez ajouter SAP Cloud Platform Identity Authentication à votre liste d’applications SaaS managées à partir de la galerie.

**Pour ajouter SAP Cloud Platform Identity Authentication à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter la nouvelle application, sélectionnez le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SAP Cloud Platform Identity Authentication**. 

5. Sélectionnez **SAP Cloud Platform Identity Authentication** dans le panneau de résultats, puis sélectionnez le bouton **Ajouter**.

    ![SAP Cloud Platform Identity Authentication dans la liste de résultats](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Cloud Platform Identity Authentication. Vous allez effectuer la configuration et le test avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP Cloud Platform Identity Authentication équivalent. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SAP Cloud Platform Identity Authentication associé.

Dans SAP Cloud Platform Identity Authentication, affectez à **Username** la même valeur que **Nom d’utilisateur** dans Azure AD. Vous avez maintenant établi le lien entre les deux utilisateurs.

Pour configurer et tester l’authentification unique Azure AD avec SAP Cloud Platform Identity Authentication, vous devez effectuer les tâches suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test SAP Cloud Platform Identity Authentication](#create-an-sap-cloud-platform-identity-authentication-test-user) pour avoir un équivalent de Britta Simon dans SAP Cloud Platform Identity Authentication qui soit associé à la représentation de l’utilisateur Azure AD.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le Portail Azure et configurer l’authentification unique dans votre application SAP Cloud Platform Identity Authentication.

**Pour configurer l’authentification unique d’Azure AD avec SAP Cloud Platform Identity Authentication, suivez les étapes ci-dessous :**

1. Dans le portail Azure, dans la page d’intégration des applications **SAP Cloud Platform Identity Authentication**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Authentification basée sur SAML**, sélectionnez **Mode** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

3. Si vous souhaitez configurer l’application en mode initié par **IDP**, dans la section **Domaine et URL SAP Cloud Platform Identity Authentication**, procédez comme suit :  

    ![Informations d’authentification unique pour Domaine et URL SAP Cloud Platform Identity Authentication](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : `<IAS-tenant-id>.accounts.ondemand.com`

    b. Dans la zone **URL de réponse**, tapez une URL au format suivant : `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support technique de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Si vous ne comprenez pas la valeur d’identificateur, lisez la documentation SAP Cloud Platform Identity Authentication relative à la [configuration du locataire SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service**, sélectionnez **Afficher les paramètres d’URL avancés**.

    ![Informations d’authentification unique pour Domaine et URL SAP Cloud Platform Identity Authentication](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    Dans la zone **URL de connexion**, tapez une URL au format suivant : `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez-la à jour avec l’URL de connexion réelle. Utilisez l’URL de connexion spécifique de votre application métier. En cas de doute, contactez [l’équipe du support technique de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html).

5. Dans la section **Certificat de signature SAML**, sélectionnez **XML des métadonnées**. Ensuite, enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

6. L’application SAP Cloud Platform Identity Authentication attend les assertions SAML dans un format précis. Gérez les valeurs de ces attributs à partir de la section **Attributs utilisateur** de la page d’intégration des applications. La capture d’écran suivante présente un exemple du format. 

    ![Configurer l’authentification unique](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

7. Si votre application SAP attend un attribut tel que **firstName**, ajoutez l’attribut **firstName** dans la section **Attributs utilisateur**. Cette option est disponible dans la boîte de dialogue **Authentification unique** de la boîte de dialogue **Attributs du jeton SAML**.

    a. Pour ouvrir la boîte de dialogue **Ajouter un attribut**, cliquez sur **Ajouter un attribut**. 
    
    ![Configurer l’authentification unique](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Configurer l’authentification unique](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone **Nom**, tapez le nom de l’attribut **firstName**.
    
    c. Dans la liste **Valeur**, sélectionnez la valeur d’attribut **user.givenname**.
    
    d. Sélectionnez **OK**.

8. Sélectionnez le bouton **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

9. Dans la section **Configuration de SAP Cloud Platform Identity Authentication**, sélectionnez **Configurer SAP Cloud Platform Identity Authentication** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de SAP Cloud Platform Identity Authentication](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

10. Pour configurer l’authentification unique sur votre application, accédez à la console d’administration de SAP Cloud Platform Identity Authentication. L’URL suit le modèle suivant : `https://<tenant-id>.accounts.ondemand.com/admin`. Lisez ensuite la documentation relative à SAP Cloud Platform Identity Authentication à l’adresse [Integration with Microsoft Azure AD (Intégration à Microsoft Azure AD)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. Dans le portail Azure, sélectionnez le bouton **Enregistrer**.

12. Effectuez les étapes suivantes uniquement si vous souhaitez ajouter et activer l’authentification unique pour une autre application SAP. Répétez les étapes décrites dans la section **Ajout de SAP Cloud Platform Identity Authentication à partir de la galerie**.

13. Dans le portail Azure, dans la page d’intégration des applications **SAP Cloud Platform Identity Authentication**, sélectionnez **Authentification liée**.

    ![Configurer l’authentification liée](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

14. Enregistrez la configuration.

>[!NOTE] 
>La nouvelle application utilise la configuration de l’authentification unique de l’application SAP précédente. Veillez à utiliser les mêmes fournisseurs d’identité d’entreprise dans la console d’administration de SAP Cloud Platform Identity Authentication.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com) pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée en consultant la [documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :**

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Créer un utilisateur de test SAP Cloud Platform Identity Authentication

Vous n’avez pas besoin de créer un utilisateur dans SAP Cloud Platform Identity Authentication. Les utilisateurs qui se trouvent dans le magasin d’utilisateurs Azure AD peuvent utiliser la fonctionnalité d’authentification unique (SSO).

SAP Cloud Platform Identity Authentication prend en charge l’option de fédération des identités. Cette option permet à l’application de vérifier si les utilisateurs authentifiés par le fournisseur d’identité d’entreprise sont présents dans le magasin d’utilisateurs de SAP Cloud Platform Identity Authentication. 

L’option de fédération des identités est désactivée par défaut. Si la fédération des identités est activée, seuls les utilisateurs importés dans SAP Cloud Platform Identity Authentication peuvent accéder à l’application. 

Pour savoir comment activer ou désactiver la fédération des identités avec SAP Cloud Platform Identity Authentication, consultez « Activer la fédération des identités avec SAP Cloud Platform Identity Authentication » dans [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication (Configurer la fédération des identités avec le magasin d’utilisateurs de SAP Cloud Platform Identity Authentication)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à SAP Cloud Platform Identity Authentication.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à SAP Cloud Platform Identity Authentication, suivez les étapes ci-dessous :**

1. Sur le Portail Azure, ouvrez l’affichage des applications, puis accédez à l’affichage des répertoires. Ensuite, accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SAP Cloud Platform Identity Authentication**.

    ![Lien SAP Cloud Platform Identity Authentication dans la liste Applications](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste d’utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Attribuer** dans la boîte de dialogue **Ajouter une attribution**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette SAP Cloud Platform Identity Authentication dans le volet d’accès, vous êtes automatiquement connecté à votre application SAP Cloud Platform Identity Authentication.

Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png
