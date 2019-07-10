---
title: 'Didacticiel : Intégration d’Azure Active Directory à Citrix Netscaler | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Citrix Netscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: cc32f464044149e263db60fc2de237b473da5ab2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105499"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Didacticiel : Intégration d’Azure Active Directory à Citrix Netscaler

Dans ce tutoriel, vous allez apprendre à intégrer Citrix Netscaler à Azure Active Directory (Azure AD).
L’intégration de Citrix Netscaler à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Citrix Netscaler.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Citrix Netscaler (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Citrix Netscaler, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Citrix Netscaler pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* Citrix Netscaler prend en charge l’authentification unique lancée par le **fournisseur de services**

* Citrix Netscaler prend en charge l’attribution d’utilisateurs **juste-à-temps**

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Ajout de Citrix Netscaler à partir de la galerie

Pour configurer l’intégration de Citrix Netscaler à Azure AD, vous devez ajouter Citrix Netscaler, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Citrix Netscaler à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Citrix Netscaler**, sélectionnez **Citrix Netscaler** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![Citrix Netscaler dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Citrix Netscaler avec un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre l’utilisateur Azure AD et l’utilisateur Citrix Netscaler associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Citrix Netscaler, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Citrix Netscaler](#configure-citrix-netscaler-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Citrix Netscaler](#create-citrix-netscaler-test-user)** pour avoir un équivalent de Britta Simon dans Citrix Netscaler, lié à la représentation Azure AD associée.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Citrix Netscaler, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Citrix Netscaler**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Citrix Netscaler](common/sp-identifier-reply.png)

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<<Your FQDN>>`

    c. Dans la zone de texte **URL de réponse (URL Assertion Consumer Service)** , tapez une URL à l’aide du modèle suivant : `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support Citrix Netscaler](https://www.citrix.com/contact/technical-support.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

    > [!NOTE]
    > Pour que l’authentification unique fonctionne, ces URL doivent être accessibles à partir de sites publics. Vous devez activer le pare-feu ou d’autres paramètres de sécurité côté Netscaler pour qu’Azure AD puisse publier le jeton sur l’URL ACS configurée.

5. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer Citrix Netscaler**, copiez la ou les URL appropriées correspondant à vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-citrix-netscaler-single-sign-on"></a>Configurer l’authentification unique Citrix Netscaler

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre locataire Citrix Netscaler en tant qu’administrateur.

2. Vérifiez que la **version du microprogramme NetScaler est NS12.1: Build 48.13.nc**.

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure01.png)

3. Dans la page **VPN Virtual Server** (Serveur virtuel du VPN), effectuez les étapes suivantes :

     ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure02.png)

    a. Définissez le paramètre de passerelle **ICA Only** (ICA uniquement) sur **true**.
    
    b. Définissez **Enable Authentication** (Activer l’authentification) sur **true**.
    
    c. **DTLS** est facultatif.
    
    d. Vérifiez que **SSLv3** a pour valeur **Disabled** (Désactivé).

4. Un groupe personnalisé **SSL Ciphers** (Chiffrements SSL) est créé pour atteindre A+ sur https://www.ssllabs.com comme indiqué ci-dessous :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure03.png)

5. Dans la page **Configure Authentication SAML Server** (Configurer le serveur SAML d’authentification), effectuez les étapes suivantes :

      ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure04.png)

    a. Dans la zone de texte **Name**, entrez le nom du serveur.

    b. Dans la zone de texte **Redirect URL** (URL de redirection), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    c. Dans la zone de texte **Single Logout URL** (URL SLO), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du Portail Azure.

    d. Dans **IDP Certificate Name** (nom du certificat IDP), cliquez sur le signe **« + »** pour ajouter le certificat que vous avez téléchargé à partir du portail Azure. Une fois le certificat chargé, sélectionnez-le dans la liste déroulante.

    e. Vous devez définir les champs supplémentaires suivants dans cette page.

      ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure24.png)

    f. Définissez **Requested Authentication Context** (Contexte de l’authentification demandée) sur **Exact**.

    g. Définissez **Signature Algorithm** (Algorithme de signature) sur **RSA-SHA256**.

    h. Définissez **Digest Method** (Méthode Digest) sur **SHA256**.

    i. Cochez la case **Enforce Username** (Appliquer le nom d’utilisateur).

    j. Cliquez sur **OK**

6. Pour configurer le **profil de session**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure06.png)

    a. Dans la zone de texte **Name**, entrez le nom du profil de session.

    b. Sous l’onglet **Client Experience** (Expérience cliente), apportez les modifications comme indiqué dans la capture d’écran ci-dessous.

    c. Apportez les modifications sous l’**onglet General** comme indiqué ci-dessous, puis cliquez sur **OK**.

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure07.png)

    d. Sous l’onglet **Published Applications** (Applications publiées), apportez les modifications comme indiqué dans la capture d’écran ci-dessous, puis cliquez sur **OK**.

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure08.png)

    e. Sous l’onglet **Security**, apportez les modifications comme indiqué dans la capture d’écran ci-dessous, puis cliquez sur **OK**.

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure09.png)

7. Établissez les connexions ICA sur le port de fiabilité de session **2598** comme indiqué dans la capture d’écran ci-dessous.

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure10.png)

8. Dans la section **SAML**, ajoutez les **serveurs** comme indiqué dans la capture d’écran ci-dessous.

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure11.png)

9. Dans la section **SAML**, ajoutez les **stratégies** comme indiqué dans la capture d’écran ci-dessous.

     ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure12.png)

10. Dans la page **Global Settings** (Paramètres généraux), accédez à la section **Clientless Access** (Accès sans client).

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure13.png)

11. Sous l’onglet **Configuration**, effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure14.png)

    a. Sélectionnez **Allow Domains** (Autoriser les domaines).

    b. Dans la zone de texte **Domain Name** (Nom de domaine), sélectionnez le domaine.

    c. Cliquez sur **OK**.

12. Définissez les paramètres **StoreFront** sous l’onglet **Receiver for Web Sites** (Récepteur pour les sites web) comme indiqué dans la capture d’écran ci-dessous :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure15.png)

13. Dans la fenêtre contextuelle **Manage Authentication Methods - Corp** (Gérer les méthodes d’authentification - Corp), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure16.png)

    a. Sélectionnez **Nom d’utilisateur et mot de passe**.

    b. Sélectionnez **Pass-through from NetScaler Gateway** (Transmission directe à partir de la passerelle NetScaler).

    c. Cliquez sur **OK**.

14. Dans la fenêtre contextuelle **Configure Trusted Domains** (Configurer les domaines approuvés), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure17.png)

    a. Sélectionnez **Trusted domains only** (Domaines approuvés uniquement).

    b. Cliquez sur **Add** (Ajouter) pour ajouter votre domaine dans la zone de texte **Trusted domains** (Domaines approuvés).

    c. Sélectionnez le domaine par défaut dans la liste **Default domain** (Domaine par défaut).

    d. Sélectionnez **Show domains list in logon page** (Afficher la liste des domaines dans la page de connexion).

    e. Cliquez sur **OK**.

15. Dans la fenêtre contextuelle **Manage NetScaler Gateways** (Gérer les passerelles NetScaler), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure18.png)

    a. Cliquez sur **Ajouter** pour ajouter vos passerelles NetScaler dans la zone de texte **NetScaler Gateways** (Passerelles NetScaler).

    b. Cliquez sur **Fermer**.

16. Sous l’onglet **StoreFront General Settings** (Paramètres généraux StoreFront), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure19.png)

    a. Dans la zone de texte **Display name** (Nom d’affichage), tapez le nom de votre passerelle NetScaler.

    b. Dans la zone de texte **NetScaler Gateway URL** (URL de la passerelle NetScaler), tapez l’URL de votre passerelle NetScaler.

    c. Définissez **Usage or role** (Utilisation ou rôle) sur **Authentication and HDX routing** (Authentification et routage HDX).

    d. Cliquez sur **OK**.

17. Sous l’onglet **StoreFront Secure Ticket Authority** (Autorité de tickets sécurisés StoreFront), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure20.png)

    a. Cliquez sur **Add** (Ajouter) pour ajouter les **URL d’autorité de tickets sécurisés** dans la zone de texte.

    b. Sélectionnez **Enable session reliability** (Activer la fiabilité de la session).

    c. Cliquez sur **OK**.

18. Sous l’onglet **StoreFront Authentication Settings** (Paramètres d’authentification StoreFront), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure21.png)

    a. Sélectionnez votre **Version**.

    b. Définissez **Logon type** (Type d’ouverture de session) sur **Domaine**.

    c. Entrez votre **URL de rappel**.

    d. Cliquez sur **OK**.

19. Sous l’onglet **StoreFront Deploy Citrix Receiver** (StoreFront - Déployer le récepteur Citrix), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure22.png)

    a. Définissez **Deployment option** (Option de déploiement) sur **Use Receiver for HTML5 if local Receiver is unavailable** (Utiliser le récepteur pour HTML5 si le récepteur local n’est pas disponible).

    b. Cliquez sur **OK**.

20. Dans la fenêtre contextuelle **Manage Beacons** (Gérer les balises), effectuez les étapes suivantes :

    ![Configurer l'authentification unique](./media/citrix-netscaler-tutorial/configure23.png)

    a. Définissez **Internal beacon** (Balise interne) sur **Use the service URL** (Utiliser l’URL du service).

    b. Cliquez sur **Add** (Ajouter) pour ajouter vos URL dans la zone de texte **External beacons** (Balises externes).

    c. Cliquez sur **OK**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Citrix Netscaler.

1. Dans le Portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis sélectionnez **Citrix Netscaler**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Citrix Netscaler**.

    ![Lien Citrix Netscaler dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-citrix-netscaler-test-user"></a>Créer un utilisateur de test Citrix Netscaler

Dans cette section, un utilisateur appelé Britta Simon est créé dans Citrix Netscaler. Citrix Netscaler prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur souhaité n’existe pas déjà dans Citrix Netscaler, il est créé après l’authentification.

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique de Citrix Netscaler](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Citrix Netscaler dans le panneau d’accès doit vous connecter automatiquement à l’application Citrix Netscaler pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

