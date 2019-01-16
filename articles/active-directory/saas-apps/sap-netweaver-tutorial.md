---
title: 'Tutoriel : Intégration d’Azure Active Directory avec SAP NetWeaver | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 607d05818966e62407795640d223f1aed2f59bbb
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156746"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Tutoriel : Intégration d’Azure Active Directory avec SAP NetWeaver

Dans ce didacticiel, vous allez apprendre à intégrer SAP NetWeaver à Azure Active Directory (Azure AD).

L’intégration de SAP NetWeaver à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SAP NetWeaver.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SAP NetWeaver (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique auprès d’Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à SAP NetWeaver, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SAP NetWeaver pour lequel l’authentification unique est activée
- SAP NetWeaver V7.20 (version minimale requise)

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SAP NetWeaver à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sap-netweaver-from-the-gallery"></a>Ajout de SAP NetWeaver à partir de la galerie

Pour configurer l’intégration de SAP NetWeaver à Azure AD, vous devez ajouter SAP NetWeaver depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SAP NetWeaver à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SAP NetWeaver**, sélectionnez **SAP NetWeaver** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SAP NetWeaver dans la liste des résultats](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP NetWeaver, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SAP NetWeaver équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur SAP NetWeaver associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec SAP NetWeaver, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SAP NetWeaver](#creating-sapnetweaver-test-user)** pour avoir un équivalent de Britta Simon dans SAP NetWeaver lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SAP NetWeaver.

**Pour configurer l’authentification unique Azure AD avec SAP NetWeaver, procédez comme suit :**

1. Ouvrez une nouvelle fenêtre de navigateur web et connectez-vous au site de votre entreprise SAP NetWeaver en tant qu’administrateur.

2. Vérifiez que les services **http** et **https** sont actifs et que les ports appropriés sont affectés dans le T-Code **SMICM**.

3. Connectez-vous au client d’entreprise de SAP System (T01) où l’authentification unique est requise et activez HTTP Security session Management.

    a. Accédez au code Transaction **SICF_SESSIONS**. Tous les paramètres de profil appropriés sont affichés avec les valeurs actuelles. Ils se présentent comme ceci :
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
    > Ajustez les paramètres ci-dessus conformément aux besoins de votre organisation. Les paramètres ci-dessus sont fournis ici à titre indicatif.

    b. Si nécessaire, ajustez les paramètres dans l’instance ou le profil par défaut du système SAP et redémarrez le système SAP.

    c. Double-cliquez sur le client approprié pour activer la session de sécurité HTTP.

    ![Lien Téléchargement de certificat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Activez les services SICF ci-dessous :
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Accédez au code Transaction **SAML2** dans le client d’entreprise du système SAP [T01/122]. Une interface utilisateur s’ouvre dans un navigateur. Dans cet exemple, nous utilisons 122 comme client d’entreprise SAP.

    ![Lien Téléchargement de certificat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Indiquez vos nom d’utilisateur et mot de passe à entrer dans l’interface utilisateur, puis cliquez sur **Edit** (Modifier).

    ![Lien Téléchargement de certificat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Dans **Provider Name** (nom du fournisseur), remplacez T01122 par `http://T01122`, puis cliquez sur **Save** (Enregistrer).

    > [!NOTE]
    > Par défaut, le nom du fournisseur est au format <sid><client>, mais Azure AD attend le nom au format <protocol>://<name>. Il est donc recommandé de conserver le nom du fournisseur au format https://<sid><client> pour autoriser la configuration de plusieurs moteurs ABAP SAP NetWeaver dans Azure AD.

    ![Lien Téléchargement de certificat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Génération des métadonnées du fournisseur de services** : une fois la configuration du **Local Provider** (fournisseur local) et des **Trusted Providers** (fournisseurs approuvés) terminée dans l’interface utilisateur de SAML 2.0, l’étape suivante consiste à générer le fichier de métadonnées du fournisseur de services qui contient l’ensemble des paramètres, des contextes de l’authentification et des autres configurations dans SAP. Une fois ce fichier généré, nous devons le charger sur Azure AD.

    ![Lien Téléchargement de certificat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Accédez à l’onglet **Local Provider** (Fournisseur local).

    b. Cliquez sur **Metadata** (Métadonnées).

    c. Enregistrez le **fichier XML de métadonnées** sur votre ordinateur, puis chargez-le dans la section **Configuration SAML de base** pour renseigner automatiquement les valeurs **Identificateur** et **URL de réponse** dans le portail Azure.

8. Dans le portail Azure, sur la page d’intégration de l’application **SAP NetWeaver**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

9. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, cliquez sur **Sélectionner** pour le mode **SAML** afin d’activer l’authentification unique.

    ![Configurer l'authentification unique](common/tutorial_general_301.png)

10. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Configurer l'authentification unique](common/editconfigure.png)

11. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Cliquez sur **Charger le fichier de métadonnées** pour charger le **fichier de métadonnées du fournisseur de services** obtenu précédemment.

    ![Charger le fichier de métadonnées](common/editmetadataupload.png)

    b. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.

    ![Charger le fichier de métadonnées](common/uploadmetadata.png)

    c. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la zone de texte de la section **Configuration SAML de base**, comme indiqué ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL de SAP NetWeaver](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<your company instance of SAP NetWeaver>`

12. L’application SAP NetWeaver attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![Section de l’attribut](./media/sapnetweaver-tutorial/edit_attribute.png)

13. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, configurez le jeton SAML comme sur l’image ci-dessus et procédez comme suit :

    a. Cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.
    
    ![Section de l’attribut](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. Sous l’onglet **Gérer les revendications des utilisateurs**, effectuez les étapes suivantes :

    ![Section de l’attribut](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * Sélectionnez **Transformation**.
  
    * Dans la liste **Transformation**, sélectionnez `ExtractMailPrefix()`.
  
    * Dans la liste **Paramètre 1**, sélectionnez `user.userprincipalname`.

    * Cliquez sur **Enregistrer**.

14. Dans la page **Certificat de signature SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. Dans la section **Configurer SAP NetWeaver**, copiez l’URL appropriée en fonction de vos besoins.

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

    ![Configuration de SAP NetWeaver](common/configuresection.png)

16. Ouvrez une session sur le système SAP et accédez au code Transaction SAML2. Une nouvelle fenêtre de navigateur s’ouvre avec l’écran de configuration SAML.

17. Pour configurer des points de terminaison pour le fournisseur d’identité approuvé (Azure AD), accédez à l’onglet **Trusted Providers** (Fournisseurs approuvés).

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. Appuyez sur **Add** (Ajouter) et sélectionnez **Upload Metadata File** (Charger le fichier de métadonnées) dans le menu contextuel.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Chargez le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. Dans l’écran suivant, tapez le nom d’alias. Tapez par exemple aadsts, puis appuyez sur **Next** (Suivant) pour continuer.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. Vérifiez que votre **Digest Algorithm** (Algorithme Digest) est **SHA-256** et qu’aucun changement n’est requis, puis appuyez sur **Next** (Suivant).

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. Dans **Single Sign-On Endpoints** (Points de terminaison d’authentification unique), utilisez **HTTP POST** et cliquez sur **Next** (Suivant) pour continuer.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. Dans **Single Logout Endpoints** (Points de terminaison Single Logout), utilisez **HTTPRedirect** et cliquez sur **Next** (Suivant) pour continuer.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. Dans **Artifact Endpoints** (Points de terminaison d’artefact), appuyez sur **Next** (Suivant) pour continuer.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. Dans **Authentication Requirements** (Exigences d’authentification), cliquez sur **Finish** (Terminer).

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. Accédez à l’onglet **Trusted Providers** (Fournisseurs approuvés) > **Identity Federation** (Fédération des identités), en bas de l’écran. Cliquez sur **Modifier**.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. Cliquez sur **Add** (Ajouter) sous l’onglet **Identity Federation** (Fédération des identités), dans la fenêtre du bas.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. Dans la fenêtre contextuelle, sélectionnez **Unspecified** (Non spécifié) dans **Supported NameID formats** (Formats d’ID de nom pris en charge), puis cliquez sur OK.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. Notez que les valeurs **user ID Source** (source d’identifiant utilisateur) et **user id mapping mode** (mode mappage d’identifiant utilisateur) déterminent le lien entre l’utilisateur SAP et la revendication Azure AD.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scénario : Mappage d’un utilisateur SAP à un utilisateur Azure AD.

    a. Capture d’écran des détails de NameID à partir de SAP.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Capture d’écran mentionnant les revendications requises d’Azure AD.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scénario : Sélection un identifiant utilisateur SAP en fonction de l’adresse e-mail configurée dans SU01. Dans ce cas, l’ID de l’adresse e-mail doit être configurée dans su01 pour chaque utilisateur nécessitant l’authentification unique.

    a.  Capture d’écran des détails de NameID à partir de SAP.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Capture d’écran mentionnant les revendications requises d’Azure AD.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/claimsaad2.png)

30. Cliquez sur **Save** (Enregistrer), puis sur **Enable** (Activer) pour activer le fournisseur d’identité.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/configuration1.png)

31. Cliquez sur **OK** quand vous y êtes invité.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Créer un utilisateur Azure AD][100]

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_01.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Création d’un utilisateur de test Azure AD](common/create_aaduser_02.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon@yourcompanydomain.extension**  
    Par exemple, BrittaSimon@contoso.com

    c. Sélectionnez **Propriétés**, cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Sélectionnez **Créer**.

### <a name="creating-sap-netweaver-test-user"></a>Création d’un utilisateur de test de SAP NetWeaver

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SAP NetWeaver. Collaborez avec votre équipe d’experts SAP en interne ou avec le partenaire SAP de votre organisation pour ajouter les utilisateurs dans la plateforme SAP NetWeaver.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP NetWeaver.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **SAP NetWeaver**.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

1. Une fois le fournisseur d’identité Azure AD activé, essayez d’accéder à l’URL ci-dessous pour vérifier l’authentification unique (aucune invite n’est fournie pour entrer le nom d’utilisateur et le mot de passe)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (ou) utilisez l’URL ci-dessous.

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Remplacez sapurl par le nom d’hôte SAP réel.

2. L’URL ci-dessus doit vous diriger vers l’écran mentionné ci-dessous. Si vous pouvez accéder à la page ci-dessous, cela signifie que l’installation de l’authentification unique Azure AD s’est déroulée correctement.

    ![Configurer l'authentification unique](./media/sapnetweaver-tutorial/testingsso.png)

3. Si une invite demandant à entrer le nom d’utilisateur et le mot de passe apparaît, diagnostiquez le problème en activant la trace. Pour cela, utilisez l’URL ci-dessous.

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
