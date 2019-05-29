---
title: 'Didacticiel : Intégration d’Azure Active Directory à SAP Fiori | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 9e7993ee1cb439ebeaa9f64bee55429aa54f9cee
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903949"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Didacticiel : Intégration d’Azure Active Directory à SAP Fiori

Dans ce tutoriel, vous allez apprendre à intégrer SAP Fiori à Azure Active Directory (Azure AD).

L’intégration de SAP Fiori à Azure AD vous offre les avantages suivants :

* Vous pouvez utiliser Azure AD pour contrôler qui a accès à SAP Fiori.
* Les utilisateurs peuvent être automatiquement connectés à SAP Fiori avec leur compte Azure AD (par le biais de l’authentification unique).
* Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour plus d’informations sur l’intégration d’applications software as a service (SaaS) à Azure AD, consultez l’article [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à SAP Fiori, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’abonnement Azure AD, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un abonnement SAP Fiori pour lequel l’authentification unique est activée.
* SAP Fiori 7.20 ou version ultérieure est nécessaire.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test et intégrer SAP Fiori à Azure AD.

SAP Fiori prend en charge les fonctionnalités suivantes :

* **Authentification unique lancée par le fournisseur de service**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Ajouter SAP Fiori dans le portail Azure

Pour intégrer SAP Fiori à Azure AD, vous devez ajouter SAP Fiori à votre liste d’applications SaaS gérées.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Dans le menu gauche, sélectionnez **Azure Active Directory**.

    ![Option Azure Active Directory](common/select-azuread.png)

1. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

1. Pour ajouter une application, sélectionnez **Nouvelle application**.

    ![Option Nouvelle application](common/add-new-app.png)

1. Dans la zone de recherche, entrez **SAP Fiori**. Dans les résultats de la recherche, sélectionnez **SAP Fiori**, puis **Ajouter**.

    ![SAP Fiori dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Fiori à l’aide d’un utilisateur de test nommé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur SAP Fiori associé.

Pour configurer et tester l’authentification unique Azure AD avec SAP Fiori, vous devez suivre les indications des sections suivantes :

| Tâche | Description |
| --- | --- |
| **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** | Autorise les utilisateurs à utiliser cette fonctionnalité. |
| **[Configurer l’authentification unique SAP Fiori](#configure-sap-fiori-single-sign-on)** | Configure les paramètres d’authentification unique dans l’application. |
| **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** | Teste l’authentification unique Azure AD pour un utilisateur appelé Britta Simon. |
| **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** | Permet à Britta Simon d’utiliser l’authentification unique Azure AD. |
| **[Créer un utilisateur de test SAP Fiori](#create-an-sap-fiori-test-user)** | Crée un équivalent de Britta Simon dans SAP Fiori lié à la représentation Azure AD associée. |
| **[Tester l’authentification unique](#test-single-sign-on)** | Vérifie que la configuration fonctionne. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez configurer l’authentification unique Azure AD avec SAP Fiori dans le portail Azure.

1. Ouvrez une nouvelle fenêtre de navigateur web et connectez-vous à votre site d’entreprise SAP Fiori en tant qu’administrateur.

1. Vérifiez que les services **http** et **https** sont actifs, et que les ports appropriés sont affectés au code de transaction **SMICM**.

1. Connectez-vous à SAP Business Client pour le système SAP **T01**, où l’authentification unique est requise. Ensuite, activez la gestion de session de sécurité HTTP.

    1. Accédez au code de transaction **SICF_SESSIONS**. Tous les paramètres de profil appropriés sont affichés avec les valeurs actuelles. Ils doivent ressembler à l’exemple qui suit :

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > Ajustez les paramètres en fonction des besoins de votre organisation. Les paramètres précédents sont fournis uniquement à titre d’exemple.

    1. Si nécessaire, ajustez les paramètres dans le profil (par défaut) d’instance du système SAP et redémarrez le système SAP.

    1. Double-cliquez sur le client approprié pour activer la session de sécurité HTTP.

        ![La page de valeurs actuelles des paramètres de profil pertinents dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Activez les services SICF suivants :

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Accédez au code de transaction **SAML2** dans Business Client pour le système SAP [**T01/122**]. L’interface utilisateur de configuration s’ouvre dans une nouvelle fenêtre de navigateur. Dans cet exemple, nous utilisons Business Client pour le système SAP 122.

    ![La page de connexion Business Client SAP Fiori](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Entrez votre nom d’utilisateur et votre mot de passe, puis sélectionnez **Log on** (Connexion).

    ![La page de configuration SAML 2.0 du système ABAP T01/122 dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Dans la zone **Provider Name** (Nom du fournisseur), remplacez **T01122** par **http:\//T01122**, puis sélectionnez **Save** (Enregistrer).

    > [!NOTE]
    > Par défaut, le nom du fournisseur est au format \<sid>\<client>. Azure AD attend le nom au format \<protocole>://\<nom>. Nous vous conseillons de conserver https\://\<sid>\<client> comme nom du fournisseur, afin de pouvoir configurer plusieurs moteurs ABAP SAP Fiori dans Azure AD.

    ![Nom du fournisseur mis à jour dans la page de configuration SAML 2.0 du système ABAP T01/122 dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Sélectionnez l’onglet **Local Provider** > **Metadata** (Fournisseur Local>Métadonnées).

1. Dans la boîte de dialogue **SAML 2.0 Metadata** (Métadonnées SAML 2.0), téléchargez le fichier XML de métadonnées généré et enregistrez-le sur votre ordinateur.

    ![Lien de téléchargement des métadonnées dans la boîte de dialogue de métadonnées SAP SAML 2.0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Dans le [portail Azure](https://portal.azure.com/), dans le volet d’intégration de l’application **SAP Fiori**, sélectionnez **Authentification unique**.

    ![Option d’authentification unique](common/select-sso.png)

1. Dans le volet **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML** ou **SAML/WS-Fed** pour activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

1. Dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez **Modifier** (icône de crayon) pour ouvrir le volet **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    1. Sélectionnez **Charger le fichier de métadonnées**.

        ![Option Charger le fichier de métadonnées](common/upload-metadata.png)

   1. Pour sélectionner le fichier de métadonnées, sélectionnez l’icône de dossier, puis **Charger**.

       ![Sélectionnez le fichier de métadonnées, puis le bouton Charger](common/browse-upload-metadata.png)

1. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont renseignées automatiquement dans le volet **Configuration SAML de base**. Dans la zone **URL de connexion**, entrez une URL au format suivant : https:\//\<votre_instance_d’entreprise_de_SAP Fiori\>.

    ![Informations d’authentification unique dans Domaine et URL SAP Fiori](common/sp-identifier-reply.png)

    > [!NOTE]
    > Quelques clients ont signalé des erreurs de configuration des valeurs **URL de réponse**. Si vous rencontrez ce genre d’erreur, vous pouvez utiliser le script PowerShell suivant afin de définir l’URL de réponse appropriée pour votre instance :
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Vous pouvez définir l’ID d’objet `ServicePrincipal` vous-même avant d’exécuter le script, ou vous pouvez le passer ici.

1. L’application SAP Fiori attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Pour gérer ces valeurs d’attributs, dans le volet **Configurer l’authentification unique avec SAML**, sélectionnez **Modifier**.

    ![Volet Attributs de l’utilisateur](common/edit-attribute.png)

1. Dans le volet **Attributs et revendications de l’utilisateur**, configurez les attributs de jetons SAML comme illustré dans l’image précédente. Effectuez ensuite les tâches suivantes :

    1. Sélectionnez **Modifier** pour ouvrir le volet **Gérer les revendications des utilisateurs**.

    1. Dans la liste **Transformation**, sélectionnez **ExtractMailPrefix()** .

    1. Dans la liste **Paramètre 1**, sélectionnez **user.userprinicipalname**.

    1. Sélectionnez **Enregistrer**.

       ![Volet Gérer les revendications des utilisateurs](./media/sapfiori-tutorial/nameidattribute.png)

       ![Section Transformation dans le volet Gérer les revendications des utilisateurs](./media/sapfiori-tutorial/nameidattribute1.png)


1. Dans le volet **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, sélectionnez **Télécharger** en regard de **XML de métadonnées de fédération**. Sélectionnez une option de téléchargement en fonction de vos exigences. Enregistrez le certificat sur votre ordinateur.

    ![Option Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer SAP Fiori**, copiez les URL suivantes en fonction de vos besoins :

    * URL de connexion
    * Identificateur Azure AD
    * URL de déconnexion

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Configurer l’authentification unique SAP Fiori

1. Connectez-vous au système SAP et accédez au code de transaction **SAML2**. Une nouvelle fenêtre de navigateur s’ouvre avec la page de configuration SAML.

1. Pour configurer des points de terminaison pour un fournisseur d’identité approuvé (Azure AD), accédez à l’onglet **Trusted Providers** (Fournisseurs approuvés).

    ![Onglet Trusted Providers dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Sélectionnez **Add** (Ajouter) puis **Upload Metadata File** (Charger le fichier de métadonnées) dans le menu contextuel.

    ![Options Add et Upload Metadata File dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Chargez le fichier de métadonnées que vous avez téléchargé dans le portail Azure. Sélectionnez **Suivant**.

    ![Sélectionnez le fichier de métadonnées à charger dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Dans la page suivante, dans la zone **Alias**, entrez le nom d’alias. Par exemple, **aadsts**. Sélectionnez **Suivant**.

    ![Zone Alias dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Vérifiez que la valeur de la zone **Digest Algorithm** est **SHA-256**. Sélectionnez **Suivant**.

    ![Vérifiez la valeur d’algorithme Digest dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Sous **Single Sign-On Endpoints** (Points de terminaison d’authentification unique), sélectionnez **HTTP POST**, puis **suivant**.

    ![Options Single Sign-On Endpoints dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Sous **Single Logout Endpoints** (Points de terminaison de déconnexion unique), sélectionnez **HTTP Redirect**, puis **suivant**.

    ![Options Single Logout Endpoints dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Sous **Artifact Endpoints** (Points de terminaison d’artefact), sélectionnez **Suivant** pour continuer.

    ![Options Artifact Endpoints dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Sous **Authentication Requirements** (Exigences d’authentification), sélectionnez **Finish** (Terminer).

    ![Options Authentication Requirements et option Finish dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Sélectionnez **Trusted Provider** > **Identity Federation** (Fournisseurs approuvés>Fédération des identités) en bas de la page. Sélectionnez **Edit**.

    ![Onglets Trusted Provider et Identity Federation dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Sélectionnez **Add**.

    ![Option Add sous l’onglet Identity Federation](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Dans la boîte de dialogue **Supported NameID Formats** (Formats NameID pris en charge), sélectionnez **Unspecified** (Non spécifié). Sélectionnez **OK**.

    ![Boîte de dialogue Supported NameID Formats dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Les valeurs **user ID Source** (Source d’ID utilisateur) et **user ID mapping mode** (Mode de mappage d’ID utilisateur) déterminent le lien entre l’utilisateur SAP et la revendication Azure AD.  

    **Scenario 1** : Mappage d’un utilisateur SAP à un utilisateur Azure AD

    1. Dans SAP, sous **Details of NameID Format "Unspecified"** (Détails du format NameID « Unspecified »), notez les détails :

        ![Boîte de dialogue Details of NameID Format "Unspecified" dans SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. Dans le portail Azure, sous **Attributs et revendications de l’utilisateur**, notez les revendications requises à partir d’Azure AD.

        ![Boîte de dialogue Attributs et revendications de l’utilisateur dans le portail Azure](./media/sapfiori-tutorial/claimsaad1.png)

    **Scénario 2** : Sélectionner un ID utilisateur SAP en fonction de l’adresse e-mail configurée dans SU01. Dans ce cas, l’ID de l’adresse e-mail doit être configuré dans SU01 pour chaque utilisateur nécessitant l’authentification unique.

    1.  Dans SAP, sous **Details of NameID Format "Unspecified"** (Détails du format NameID « Unspecified »), notez les détails :

        ![Boîte de dialogue Details of NameID Format "Unspecified" dans SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Dans le portail Azure, sous **Attributs et revendications de l’utilisateur**, notez les revendications requises à partir d’Azure AD.

       ![Boîte de dialogue Attributs et revendications de l’utilisateur dans le portail Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. Sélectionnez **Save** (Enregistrer), puis **Enable** (Activer) pour activer le fournisseur d’identité.

    ![Options Save et Enable dans SAP](./media/sapfiori-tutorial/configuration1.png)

1. À l’invite, sélectionnez **OK**.

    ![Option OK dans la boîte de dialogue SAML 2.0 Configuration dans SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, créez un utilisateur de test nommé Britta Simon dans le portail Azure.

1. Dans le portail Azure, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.

    ![Options Utilisateurs et Tous les utilisateurs](common/users.png)

1. Sélectionnez **Nouvel utilisateur**.

    ![Option Nouvel utilisateur](common/new-user.png)

1. Dans le volet **Utilisateur**, effectuez les étapes suivantes :

    1. Dans la zone **Nom**, entrez **BrittaSimon**.
  
    1. Dans la zone **Nom d’utilisateur**, entrez **brittasimon\@\<votre_domaine_d’entreprise>.\<extension>** . Par exemple, **brittasimon\@contoso.com**.

    1. Cochez la case **Afficher le mot de passe**. Notez la valeur affichée dans la zone **Mot de passe**.

    1. Sélectionnez **Créer**.

    ![Volet Utilisateur](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à accéder à SAP Fiori pour lui permettre d’utiliser l’authentification unique Azure.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise** > **Toutes les applications** > **SAP Fiori**.

    ![Volet Applications d’entreprise](common/enterprise-applications.png)

1. Dans la liste des applications, sélectionnez **SAP Fiori**.

    ![Fiori SAP dans la liste des applications](common/all-applications.png)

1. Dans le menu, sélectionnez **Utilisateurs et groupes**.

    ![Option Utilisateurs et groupes](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**. Ensuite, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

1. Dans le volet **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs. Choisissez **Select**.

1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans le volet **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste. Choisissez **Select**.

1. Dans le volet **Ajouter une attribution**, sélectionnez **Attribuer**.

### <a name="create-an-sap-fiori-test-user"></a>Créer un utilisateur de test SAP Fiori

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SAP Fiori. Collaborez avec votre équipe d’experts SAP en interne ou avec le partenaire SAP de votre organisation pour ajouter l’utilisateur à la plateforme SAP Fiori.

### <a name="test-single-sign-on"></a>Tester l’authentification unique

1. Une fois le fournisseur d’identité Azure AD activé dans SAP Fiori, essayez d’accéder à l’une des URL suivantes pour tester l’authentification unique (vous ne devriez pas être invité à entrer un nom d’utilisateur et un mot de passe) :

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Remplacez *sapurl* par le nom d’hôte SAP réel.

1. L’URL de test doit vous mener à la page d’application de test suivante dans SAP. Si la page s’ouvre, cela signifie que l’authentification unique Azure AD est correctement configurée.

    ![Page d’application de test standard dans SAP](./media/sapfiori-tutorial/testingsso.png)

1. Si vous êtes invité à fournir un nom d’utilisateur et un mot de passe, activez la trace pour vous aider à diagnostiquer le problème. Utilisez l’URL suivante pour la trace : https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

- [Listes des tutoriels pour intégrer des applications SaaS à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
