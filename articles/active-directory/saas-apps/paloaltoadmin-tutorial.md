---
title: 'Tutoriel : Intégration d’Azure Active Directory à Palo Alto Networks - Admin UI | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Palo Alto Networks - Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9804a44a29f4540c28ec4e1eb6927e65af70218c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682949"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Tutoriel : Intégration d’Azure Active Directory à Palo Alto Networks - Admin UI

Dans ce didacticiel, vous découvrez comment intégrer Palo Alto Networks - Admin UI avec Azure Active Directory (Azure AD).
L’intégration de Palo Alto Networks - Admin UI avec Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Palo Alto Networks - Admin UI.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à Palo Alto Networks - Admin UI (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Palo Alto Networks - Admin UI, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Palo Alto Networks - Admin UI pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Palo Alto Networks - Admin UI prend en charge l’authentification unique initiée par le **fournisseur de services**.
* Palo Alto Networks - Admin UI prend en charge l’attribution d’utilisateurs **Juste-à-temps**.

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Ajout de Palo Alto Networks - Admin UI depuis la galerie

Pour configurer l’intégration de Palo Alto Networks - Admin UI à Azure AD, vous devez ajouter Palo Alto Networks - Admin UI à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Palo Alto Networks - Admin UI** dans la zone de recherche.
1. Sélectionnez **Palo Alto Networks - Admin UI** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Palo Alto Networks - Admin UI pour un utilisateur de test nommé **B.Simon**.
Pour que l’authentification unique fonctionne, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Palo Alto Networks - Admin UI associé.

Pour configurer et tester l’authentification unique Azure AD avec Palo Alto Networks - Admin UI, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Palo Alto Networks - Admin UI](#configure-palo-alto-networks---admin-ui-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Palo Alto Networks - Admin UI](#create-palo-alto-networks---admin-ui-test-user)** pour obtenir un équivalent de B.Simon dans Palo Alto Networks - Admin UI lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Palo Alto Networks - Admin UI, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Palo Alto Networks - Admin UI**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

1. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<Customer Firewall FQDN>/php/login.php`.

    b. Dans la zone de texte **Identificateur**, tapez une URL en utilisant le format suivant : `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Dans la zone de texte **URL de réponse**, tapez l’URL Assertion Consumer Service (ACS) dans le format suivant : `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Contactez [l’équipe de support client de Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.
    >
    > Le port 443 doit être utilisé dans les valeurs **Identificateur** et **URL de réponse**, car ces deux valeurs sont codées en dur dans le pare-feu Palo Alto. La suppression de ce numéro de port provoquera une erreur lors de la connexion.

    > Le port 443 doit être utilisé dans les valeurs **Identificateur** et **URL de réponse**, car ces deux valeurs sont codées en dur dans le pare-feu Palo Alto. La suppression de ce numéro de port provoquera une erreur lors de la connexion.

1. Votre application PureCloud by Genesys attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Étant donné que les valeurs d’attributs ne sont que des exemples, mappez les valeurs appropriées pour *nom d’utilisateur* et *adminrole*. Il existe un autre attribut facultatif, *accessdomain*, qui est utilisé pour restreindre l’accès administrateur à des systèmes virtuels spécifiques sur le pare-feu.

1. Outre ceux mentionnés ci-dessus, l’application PureCloud by Genesys s’attend également à ce que quelques attributs de plus, indiqués ci-après, soient transmis dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom |  Attribut source|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > Pour plus d'informations sur les attributs, consultez les articles suivants :
    > * [Profil de rôle d’administrateur pour Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domaine d’accès de périphérique pour Admin UI (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Palo Alto Networks - Admin UI**, copiez les URL appropriées selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

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

Dans cette section, vous autorisez B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Palo Alto Networks - Admin UI.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Palo Alto Networks - Admin UI**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="configure-palo-alto-networks---admin-ui-sso"></a>Configurer l’authentification unique Palo Alto Networks - Admin UI

1. Ouvrez l’interface utilisateur de l’administration du pare-feu Palo Alto Networks en tant qu’administrateur dans une nouvelle fenêtre.

2. Sélectionnez l’onglet **Appareil**.

    ![L’onglet de l’appareil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Dans le volet gauche, sélectionnez **Fournisseur d’identité SAML**, puis sélectionnez **Importer** pour importer le fichier de métadonnées.

    ![Le bouton Importer un fichier de métadonnées](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Dans la fenêtre **Importer le profil du serveur du fournisseur d’identité SAML**, procédez comme suit :

    ![La fenêtre « Importer le profil du serveur du fournisseur d’identité SAML »](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Dans la zone **Nom du profil**, spécifiez un nom (par exemple **AzureAD Admin UI**).

    b. Cliquez sur **Nouveau fournisseur d’identité**, sélectionnez **Parcourir** et le fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure.

    c. Décochez **Valider le certificat de fournisseur d’identité**.

    d. Sélectionnez **OK**.

    e. Pour valider les configurations sur le pare-feu, sélectionnez **Valider**.

5. Dans le volet gauche, sélectionnez **Fournisseur d’identité SAML**, puis le profil de fournisseur d’identité SAML (par exemple, **AzureAD Admin UI**) créé à l’étape précédente.

    ![Le profil du fournisseur d’identité SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Dans la fenêtre **Profil du serveur du fournisseur d’identité SAML**, procédez comme suit :

    ![La fenêtre « Profil du serveur du fournisseur d’identité SAML »](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Dans la zone **Identity Provider SLO URL** (URL SLO du fournisseur d’identité), remplacez l’URL SLO précédemment importée par l’URL suivante : `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Sélectionnez **OK**.

7. Dans Admin UI du pare-feu Palo Alto Networks, sélectionnez **Appareil**, puis **Rôles d’administrateur**.

8. Sélectionnez le bouton **Ajouter**.

9. Dans la fenêtre **Profil de rôle d’administrateur**, dans la zone **Nom**, indiquez un nom pour le rôle d’administrateur (par exemple, **fwadmin**). Le nom du rôle d’administrateur doit correspondre au nom de l’attribut Rôle d’administrateur SAML envoyé par le fournisseur d’identité. Le nom du rôle d’administrateur et sa valeur ont été créés dans la section **Attributs d’utilisateur** dans le portail Azure.

    ![Configurer le rôle d’administrateur Palo Alto Networks](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Dans l’interface Admin UI du pare-feu, sélectionnez **Appareil**, puis **Profil d’authentification**.

11. Sélectionnez le bouton **Ajouter**.

12. Dans la fenêtre **Profil d’authentication**, procédez comme suit : 

    ![La fenêtre « Profil d’authentification »](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Dans la zone **Nom**, indiquez un nom (par exemple, **AzureSAML_Admin_AuthProfile**).

    b. Dans la liste déroulante **Type**, sélectionnez **SAML**. 

    c. Dans la liste déroulante **Profil du serveur IdP**, sélectionnez le profil du serveur du fournisseur d’identité SAML approprié (par exemple, **AzureAD Admin UI**).

    c. Cochez la case **Activer Single Logout**.

    d. Dans la zone **Attribut du rôle administrateur**, entrez le nom d’attribut (par exemple, **adminrole**).

    e. Sélectionnez l’onglet **Avancé**, puis, sous **Liste verte**, sélectionnez **Ajouter**.

    ![Le bouton Ajouter dans l’onglet Avancé](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Cochez la case **Tous**, ou sélectionnez les utilisateurs et groupes qui peuvent s’authentifier avec ce profil.  
    Lorsqu’un utilisateur s’authentifie, le pare-feu fait correspondre le nom d’utilisateur ou de groupe associé aux entrées figurant dans cette liste. Si vous n’ajoutez pas d’entrées, aucun utilisateur ne peut s’authentifier.

    g. Sélectionnez **OK**.

13. Pour permettre aux administrateurs d’utiliser SAML SSO à l’aide d’Azure, sélectionnez **Appareil** > **Installation**. Dans le volet **Installation**, sélectionnez l’onglet **Gestion** puis, sous **Paramètres d’authentification**, sélectionnez le bouton **Paramètres** (« engrenage »).

    ![Le bouton Paramètres](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Sélectionnez le profil de l’authentification SAML créé dans la fenêtre Authentication Profile (Profil d’authentification) (par exemple, **AzureSAML_Admin_AuthProfile**).

    ![Le champ Profil d’authentification](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Sélectionnez **OK**.

16. Pour valider la configuration, sélectionnez **Valider**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Créer un utilisateur de test Palo Alto Networks - Admin UI

Palo Alto réseaux - Admin UI prend en charge l’approvisionnement de l’utilisateur juste-à-temps. Si un utilisateur n’existe pas encore, il est automatiquement créé dans le système après une authentification réussie. Aucune action n’est nécessaire pour créer l’utilisateur.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Palo Alto Networks - Admin UI dans le panneau d’accès, vous devez être connecté automatiquement à l’application Palo Alto Networks - Admin UI pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Palo Alto Networks - Admin UI avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Guide pratique pour protéger Palo Alto Networks - Admin UI avec une visibilité et des contrôles avancés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)