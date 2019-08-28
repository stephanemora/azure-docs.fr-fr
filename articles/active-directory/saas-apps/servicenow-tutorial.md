---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à ServiceNow | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 431d20c4c5ae5355d456ca3453b832e590cbb199
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558961"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à ServiceNow

Dans ce tutoriel, vous allez apprendre à intégrer ServiceNow à Azure Active Directory (Azure AD). Quand vous intégrez ServiceNow à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à ServiceNow.
* Permettre à vos utilisateurs de se connecter automatiquement à ServiceNow avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement ServiceNow pour lequel l’authentification unique est activée.
* Pour ServiceNow, une instance ou un locataire ServiceNow, version Calgary ou supérieure
* Pour ServiceNow Express, une instance ServiceNow Express, version Helsinki ou supérieure
* Le locataire ServiceNow doit avoir le [plug-in d’authentification unique à plusieurs fournisseurs](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) activé. Cette opération est possible en [envoyant une demande de service](https://hi.service-now.com).
* Pour la configuration automatique, activez le plug-in multifournisseur pour ServiceNow.
* Pour installer l’application ServiceNow Classic (Mobile), vous devez accéder au magasin approprié et rechercher l’application ServiceNow Classic, puis cliquer sur Télécharger.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. ServiceNow prend en charge l’authentification unique lancée par le **fournisseur de services** et le [provisionnement d’utilisateurs **automatisé**](servicenow-provisioning-tutorial.md).

L’application ServiceNow Classic (Mobile) peut maintenant être configurée avec Azure AD pour activer l’authentification unique. Elle prend en charge les utilisateurs **Android** et **IOS**. Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

## <a name="adding-servicenow-from-the-gallery"></a>Ajout de ServiceNow à partir de la galerie

Pour configurer l’intégration de ServiceNow à Azure AD, vous devez ajouter ServiceNow depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **ServiceNow** dans la zone de recherche.
1. Sélectionnez **ServiceNow** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Configurer et tester l’authentification unique Azure AD pour ServiceNow

Configurez et testez l’authentification unique Azure AD avec ServiceNow pour un utilisateur de test nommé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur ServiceNow associé.

Pour configurer et tester l’authentification unique Azure AD avec ServiceNow, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
    1. **[Configurer l’authentification unique Azure AD pour ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer ServiceNow](#configure-servicenow)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test ServiceNow](#create-servicenow-test-user)** pour avoir un équivalent de B. Simon dans ServiceNow lié à la représentation d’utilisateur dans Azure AD.
    1. **[Configurer l’authentification unique ServiceNow Express](#configure-servicenow-express-sso)** pour configurer les paramètres de l’authentification unique côté application.    
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.
4. **[Tester l’authentification unique pour ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **ServiceNow**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<instance-name>.service-now.com/navpage.do`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous devez mettre à jour ces valeur avec l’URL de connexion et l’identificateur réels. La procédure est expliquée plus loin dans le tutoriel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

   a. Cliquez sur le bouton de copie pour copier **l’URL des métadonnées de fédération de l’application**, puis collez-la dans le Bloc-notes, car elle sera utilisée plus loin dans le didacticiel.

    b. Cliquez sur **Télécharger** pour télécharger le **certificat (Base64)** , puis enregistrez le fichier du certificat sur votre ordinateur.

1. Dans la section **Configurer ServiceNow**, copiez la ou les URL appropriées en fonction de vos besoins.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ServiceNow.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ServiceNow**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configurer l’authentification unique Azure AD pour ServiceNow Express

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **ServiceNow**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** pour activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<instance-name>.service-now.com/navpage.do`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. La procédure est expliquée plus loin dans le tutoriel. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

6. Un service de configuration en un seul clic est fourni pour ServiceNow, autrement dit, pour qu’Azure AD configure automatiquement ServiceNow pour l’authentification basée sur SAML. Pour activer ce service, accédez à la section **Configurer ServiceNow**, cliquez sur **Afficher les instructions détaillées** pour ouvrir la fenêtre Configurer l’authentification.

    ![Configurer l’authentification unique](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Entrez votre nom d’instance ServiceNow, votre nom d’utilisateur administrateur et votre mot de passe d’administrateur dans le formulaire **Configurer l’authentification** et cliquez sur **Configurer maintenant**. Vérifiez que le nom d’utilisateur administrateur fourni a le rôle **security_admin** attribué dans ServiceNow pour que cela fonctionne. Sinon, pour configurer manuellement ServiceNow afin d’utiliser Azure AD comme fournisseur d’identité SAML, cliquez sur **Configurer manuellement l’authentification unique** et copiez **l’URL de déconnexion, l’Identificateur Azure AD et l’URL de connexion** à partir de la section Référence rapide.

    ![Configurer l’URL de l’application](./media/servicenow-tutorial/configure.png "Configurer l’URL de l’application")

## <a name="configure-servicenow"></a>Configurer ServiceNow

1. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

2. Activez le plug-in **Integration - Multiple Provider Single Sign-On Installer** (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs) en suivant la procédure ci-dessous :

    a. Dans le volet de navigation sur le côté gauche, recherchez la section **System Definition** (Définition du système) à partir de la barre de recherche, puis cliquez sur **Plug-ins**.

    ![Activer le plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "Activer le plug-in")

    b. Recherchez **Integration - Multiple Provider Single Sign-On Installer** (Intégration - Programme d’installation de l’authentification unique à plusieurs fournisseurs).

     ![Activer le plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "Activer le plug-in")

    c. Sélectionnez le plug-in. Cliquez avec le bouton droit et sélectionnez **Activate/Upgrade** (Activer/Mettre à niveau).

     ![Activer le plug-in](./media/servicenow-tutorial/tutorial_activate.png "Activer le plug-in")

    d. Cliquez sur le bouton **Activate** (Activer).

     ![Activer le plug-in](./media/servicenow-tutorial/tutorial_activate1.png "Activer le plug-in")

3. Dans le volet de navigation sur le côté gauche, recherchez la section **Multi-Provider SSO** (SSO multifournisseur) à partir de la barre de recherche, puis cliquez sur **Propriétés**.

    ![Configurer l’URL de l’application](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configurer l’URL de l’application")

4. Dans la boîte de dialogue **Multiple Provider SSO Properties** , effectuez les opérations suivantes :

    ![Configurer l’URL de l’application](./media/servicenow-tutorial/ic7694981.png "Configurer l’URL de l’application")

    * Pour **Enable multiple provider SSO**, sélectionnez **Yes**.
  
    * Pour **Enable Auto Importing of users from all identity providers into the user table** (Activer l’importation automatique des utilisateurs à partir de tous les fournisseurs d’identité dans la table utilisateur), sélectionnez **Yes** (Oui).

    * Pour **Enable debug logging got the multiple provider SSO integration** (Activer l’enregistrement du débogage pour l’intégration de l’authentification unique auprès de plusieurs fournisseurs), sélectionnez **Yes** (Oui).

    * Dans la zone de texte **The field on the user table that...** , entrez **user_name**.
  
    * Cliquez sur **Enregistrer**.

5. Il existe deux modes de configuration de **ServiceNow** : Automatique et Manuel.

6. Pour configurer **ServiceNow** automatiquement, effectuez les étapes suivantes :

    * Revenez à la page d’authentification unique **ServiceNow** dans le portail Azure.

    * Un service de configuration en un seul clic est fourni pour ServiceNow, autrement dit, pour qu’Azure AD configure automatiquement ServiceNow pour l’authentification basée sur SAML. Pour activer ce service, accédez à la section **Configuration de ServiceNow**, cliquez sur **Configurer ServiceNow** pour ouvrir la fenêtre Configurer l’authentification.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Entrez votre nom d’instance ServiceNow, votre nom d’utilisateur administrateur et votre mot de passe d’administrateur dans le formulaire **Configurer l’authentification** et cliquez sur **Configurer maintenant**. Vérifiez que le nom d’utilisateur administrateur fourni a le rôle **security_admin** attribué dans ServiceNow pour que cela fonctionne. Sinon, pour configurer manuellement ServiceNow afin d’utiliser Azure AD comme fournisseur d’identité SAML, cliquez sur **Configurer manuellement l’authentification unique** et copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la section Référence rapide.

        ![Configurer l’URL de l’application](./media/servicenow-tutorial/configure.png "Configurer l’URL de l’application")

    * Connectez-vous à votre application ServiceNow en tant qu’administrateur.

    * Dans la configuration automatique, tous les paramètres nécessaires sont configurés du côté de **ServiceNow**, mais le **Certificat X.509** n’est pas activé par défaut. Vous devrez le mapper manuellement à votre fournisseur d’identité dans ServiceNow. Effectuez les étapes suivantes :

    * Dans le volet de navigation sur le côté gauche, recherchez la section **Multi-Provider SSO** (SSO multifournisseur) à partir de la barre de recherche, puis cliquez sur **Fournisseurs d’identité**.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurer l’authentification unique")

    * Cliquez sur le fournisseur d’identité généré automatiquement

        ![Configurer l’authentification unique](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurer l’authentification unique")

    *  Dans la section **Fournisseur d’identité**, procédez comme suit :

        ![Configurer l’authentification unique](./media/servicenow-tutorial/automatic_config.png "Configurer l’authentification unique")

        * Dans la zone de texte **Nom**, indiquez le nom de votre configuration (par exemple, **Microsoft Azure Federated Single Sign-On**).

        * Supprimez la valeur **SingleLogoutRequest du fournisseur d’identité** indiquée de la zone de texte.

        * Copiez la valeur **ServiceNow Homepage**, collez-la dans la zone de texte **URL de connexion** dans la section **Configuration SAML de base ServiceNow** du portail Azure.

            > [!NOTE]
            > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (par exemple, `https://fabrikam.service-now.com/navpage.do`).

        * Copiez la valeur **Entity ID / Issuer**, collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base ServiceNow** du portail Azure.

        * Assurez-vous que **NameID Policy** (Stratégie NameID) est défini sur la valeur `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

    * Faites défiler jusqu’à la section **Certificat X.509** et sélectionnez **Modifier**.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurer l’authentification unique")

    * Sélectionnez le certificat et cliquez sur l’icône de flèche droite pour ajouter le certificat.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurer l’authentification unique")

    * Cliquez sur **Enregistrer**.

    * Cliquez sur **Tester la connexion** en haut à droite de la page.

        ![Activer le plug-in](./media/servicenow-tutorial/tutorial_activate2.png "Activer le plug-in")

    * Lorsque vous cliquez sur **Tester la connexion**, la fenêtre contextuelle dans laquelle vous devez entrer les informations d’identification apparaît et la page de résultats ci-dessous s’affiche. L’erreur **SSO Logout Test Results** (Résultats des tests de déconnexion SSO) est attendue. Ignorez l’erreur et cliquez sur le bouton **Activer**.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/servicenowactivate.png "Configurer l’authentification unique")
  
7. Pour configurer **ServiceNow** manuellement, effectuez les étapes suivantes :

    * Connectez-vous à votre application ServiceNow en tant qu’administrateur.

    * À gauche du volet de navigation, cliquez sur **Identity Providers**.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurer l’authentification unique")

    * Dans la boîte de dialogue **Fournisseurs d’identité**, cliquez sur **Nouveau**.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694977.png "Configurer l’authentification unique")

    * Dans la boîte de dialogue **Fournisseurs d’identité**, cliquez sur **SAML**.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694978.png "Configurer l’authentification unique")

    * Dans la fenêtre contextuelle **Import Identity Provider Metadata** (Importer les métadonnées du fournisseur d’identité), effectuez les étapes suivantes :

        ![Configurer l’authentification unique](./media/servicenow-tutorial/idp.png "Configurer l’authentification unique")

        * Entrez **l’URL des métadonnées de fédération de l’application** que vous avez copiée sur le portail Azure.

        * Cliquez sur **Importer**.

    * L’URL des métadonnées IdP est alors lue et tous les champs sont renseignés.

        ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694982.png "Configurer l’authentification unique")

        * Dans la zone de texte **Nom**, indiquez le nom de votre configuration (par exemple, **Microsoft Azure Federated Single Sign-On**).

        * Supprimez la valeur **SingleLogoutRequest du fournisseur d’identité** indiquée de la zone de texte.

        * Copiez la valeur **ServiceNow Homepage**, collez-la dans la zone de texte **URL de connexion** dans la section **Configuration SAML de base ServiceNow** du portail Azure.

            > [!NOTE]
            > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (par exemple, `https://fabrikam.service-now.com/navpage.do`).

        * Copiez la valeur **Entity ID / Issuer**, collez-la dans la zone de texte **Identificateur** de la section **Configuration SAML de base ServiceNow** du portail Azure.

        * Assurez-vous que **NameID Policy** (Stratégie NameID) est défini sur la valeur `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Cliquez sur **Avancé**. Dans la zone de texte **Champ utilisateur**, tapez **email** ou **user_name**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.

            > [!NOTE]
            > Vous pouvez configurer Azure AD afin d’émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse e-mail comme identificateur unique dans le jeton SAML en accédant à la section **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attributs > Authentification unique) du portail Azure et en mappant le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l’attribut sélectionné dans Azure AD (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ renseigné (par exemple, user_name).

        * Cliquez sur **Tester la connexion** en haut à droite de la page.

        * Lorsque vous cliquez sur **Tester la connexion**, la fenêtre contextuelle dans laquelle vous devez entrer les informations d’identification apparaît et la page de résultats ci-dessous s’affiche. L’erreur **SSO Logout Test Results** (Résultats des tests de déconnexion SSO) est attendue. Ignorez l’erreur et cliquez sur le bouton **Activer**.

          ![Configurer l’authentification unique](./media/servicenow-tutorial/servicenowactivate.png "Configurer l’authentification unique")

### <a name="create-servicenow-test-user"></a>Créer un utilisateur de test ServiceNow

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ServiceNow. ServiceNow prend en charge l’approvisionnement automatique d’utilisateurs, qui est activé par défaut. Vous trouverez plus d’informations [ici](servicenow-provisioning-tutorial.md) sur la façon de configurer l’approvisionnement automatique d’utilisateurs.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="configure-servicenow-express-sso"></a>Configurer l’authentification unique ServiceNow Express

1. Connectez-vous à votre application ServiceNow Express en tant qu’administrateur.

2. Dans le volet de navigation à gauche, cliquez sur **Authentification unique**.

    ![Configurer l’URL de l’application](./media/servicenow-tutorial/ic7694980ex.png "Configurer l’URL de l’application")

3. Dans la boîte de dialogue **Authentification unique**, cliquez sur l’icône de configuration en haut à droite et définissez les propriétés suivantes :

    ![Configurer l’URL de l’application](./media/servicenow-tutorial/ic7694981ex.png "Configurer l’URL de l’application")

    a. Activez **Enable multiple provider SSO** (Activer l’authentification unique à plusieurs fournisseurs) à droite.

    b. Activez **Enable debug logging for the multiple provider SSO integration** (Activer l’enregistrement du débogage pour l’intégration de l’authentification unique à plusieurs fournisseurs) à droite.

    c. Dans la zone de texte **The field on the user table that...** , entrez **user_name**.

4. Dans la boîte de dialogue **Authentification unique**, cliquez sur **Add New Certificate** (Ajouter un nouveau certificat).

    ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694973ex.png "Configurer l’authentification unique")

5. Dans la boîte de dialogue **X.509 Certificates** , procédez comme suit :

    ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694975.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Name** (Nom), indiquez le nom de votre configuration (par exemple, **TestSAML2.0**).

    b. Sélectionnez **Active**.

    c. Pour **Format**, sélectionnez **PEM**.

    d. Pour **Type**, sélectionnez **Trust Store Cert**.

    e. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat PEM** .

    f. Cliquez sur **Mettre à jour**

6. Dans la boîte de dialogue **Authentification unique**, cliquez sur **Add New IdP** (Ajouter un nouveau fournisseur d’identité).

    ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694976ex.png "Configurer l’authentification unique")

7. Dans la boîte de dialogue **Add New Identity Provider** (Ajouter un nouveau fournisseur d’identité), sous **Configure Identity Provider** (Configurer un fournisseur d’identité), procédez comme suit :

    ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694982ex.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Name** (Nom), indiquez le nom de votre configuration (par exemple, **SAML 2.0**).

    b. Dans le champ **URL du fournisseur d’identité**, collez la valeur **ID du fournisseur d’identité** copiée sur le Portail Azure.

    c. Dans le champ **AuthnRequest du fournisseur d’identité**, collez la valeur **URL de la demande d’authentification** copiée sur le Portail Azure.

    d. Dans le champ **Identity Provider's SingleLogoutRequest** (Demande de déconnexion unique du fournisseur d’identité), collez la valeur **URL de déconnexion** copiée depuis le portail Azure.

    e. Pour **Identity Provider Certificate** (Certificat du fournisseur d’identité), sélectionnez le certificat que vous avez créé à l’étape précédente.

8. Cliquez sur **Advanced Settings** (Paramètres avancés), et sous **Additional Identity Provider Properties** (Autres propriétés du fournisseur d’identité), procédez comme suit :

    ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694983ex.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Liaison du protocole pour la demande de déconnexion unique du fournisseur d’identité**, entrez **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Dans la zone de texte **Stratégie d’ID de nom**, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. Dans **AuthnContextClassRef Method**, entrez `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Désélectionnez **Créer une classe de contexte d’authentification**.

9. Sous **Additional Service Provider Properties** (Autres propriétés du fournisseur d’identité), procédez comme suit :

    ![Configurer l’authentification unique](./media/servicenow-tutorial/ic7694984ex.png "Configurer l’authentification unique")

    a. Dans la zone de texte **Page d’accueil ServiceNow** , entrez l’URL de la page d’accueil de votre instance ServiceNow.

    > [!NOTE]
    > La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (par exemple, `https://fabrikam.service-now.com/navpage.do`).

    b. Dans la zone de texte **ID de l’entité / Émetteur** , entrez l’URL de votre locataire ServiceNow.

    c. Dans la zone de texte **URI d’audience** , entrez l’URL de votre locataire ServiceNow.

    d. Dans la zone de texte **Variation d’horloge**, entrez **60**.

    e. Dans la zone de texte **Champ utilisateur**, tapez **email** ou **user_name**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.

    > [!NOTE]
    > Vous pouvez configurer Azure AD afin d’émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse e-mail comme identificateur unique dans le jeton SAML en accédant à la section **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attributs > Authentification unique) du portail Azure et en mappant le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l’attribut sélectionné dans Azure AD (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ renseigné (par exemple, user_name).

    f. Cliquez sur **Enregistrer**.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous sélectionnez la vignette ServiceNow dans le volet d’accès, vous devez être connecté automatiquement à l’application ServiceNow pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Tester l’authentification unique pour ServiceNow Classic (Mobile)

1. Ouvrez votre application **ServiceNow Classic (Mobile)** et effectuez les étapes suivantes :

    a. Cliquez sur le symbole **Ajouter** sous l’écran.

    ![Connexion](./media/servicenow-tutorial/test03.png)

    b. Tapez le nom de votre instance ServiceNow, puis cliquez sur **Continue** (Continuer).

    ![Connexion](./media/servicenow-tutorial/test04.png)

    c. Dans l’écran **Log in** (Connexion), effectuez les étapes suivantes :

    ![Connexion](./media/servicenow-tutorial/test01.png)

    *  Sous **Username**, tapez un nom d’utilisateur tel que B.simon@contoso.com.

    *  Cliquez sur **USE EXTERNAL LOGIN** (Utiliser la connexion externe) pour être redirigé vers la page Azure AD pour la connexion.
    
    *  Entrez vos informations d’identification. Si une authentification tierce ou toute autre fonctionnalité de sécurité est activée, l’utilisateur devra fournir les réponses appropriées et la **page d’accueil** de l’application apparaîtra comme ceci :

        ![Page d’accueil](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurer l’approvisionnement de l’utilisateur](servicenow-provisioning-tutorial.md)

- [Essayer ServiceNow avec Azure AD](https://aad.portal.azure.com)