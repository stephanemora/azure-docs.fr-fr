---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec Citrix NetScaler (authentification basée sur l’en-tête) | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Citrix NetScaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c442ca731ecb10f977c19b86cb32caded36659
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470534"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-header-based-authentication"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory avec Citrix NetScaler (authentification basée sur l’en-tête)

Ce tutoriel explique comment intégrer Citrix NetScaler avec Azure Active Directory (Azure AD). Quand vous intégrez Citrix NetScaler avec Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Citrix NetScaler.
* Permettre à vos utilisateurs de se connecter automatiquement à Citrix NetScaler avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Conditions préalables requises

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Citrix NetScaler pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Citrix NetScaler prend en charge l’authentification unique lancée par le **fournisseur de services**

* Citrix NetScaler prend en charge l’attribution d’utilisateurs **juste-à-temps**

- [Configurer l’authentification unique Citrix NetScaler pour l’authentification basée sur l’en-tête](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Configurer l’authentification unique Citrix NetScaler pour l’authentification Kerberos](citrix-netscaler-tutorial.md)


## <a name="adding-citrix-netscaler-from-the-gallery"></a>Ajout de Citrix NetScaler à partir de la galerie

Pour configurer l’intégration de Citrix NetScaler avec Azure AD, vous devez ajouter Citrix NetScaler, disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Citrix NetScaler** dans la zone de recherche.
1. Sélectionnez **Citrix NetScaler** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Configurer et tester l’authentification unique Azure AD pour Citrix NetScaler

Configurez et testez l’authentification unique Azure AD avec Citrix NetScaler à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Citrix NetScaler associé.

Pour configurer et tester l’authentification unique Azure AD avec Citrix NetScaler, suivez les indications des modules ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Citrix NetScaler](#configure-citrix-netscaler-sso)** – pour configurer les paramètres d’authentification unique côté application.
    1. **[Créer un utilisateur de test Citrix NetScaler](#create-citrix-netscaler-test-user)** – pour avoir un équivalent de B.Simon dans Citrix NetScaler qui soit lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [Portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Citrix NetScaler**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<<Your FQDN>>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support Citrix NetScaler](https://www.citrix.com/contact/technical-support.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

    > [!NOTE]
    > Pour que l’authentification unique fonctionne, ces URL doivent être accessibles à partir de sites publics. Vous devez activer le pare-feu ou d’autres paramètres de sécurité côté Netscaler pour qu’Azure AD puisse publier le jeton sur l’URL ACS configurée.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **URL des métadonnées de fédération d’application**, copiez cette URL et enregistrez-la dans le Bloc-notes.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. L’application Citrix NetScaler attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône **Modifier** et changez le mappage d’attribut.

    ![image](common/edit-attribute.png)

1. En plus de ce qui précède, l’application Citrix NetScaler s’attend à ce que quelques attributs supplémentaires soient repassés dans la réponse SAML. Dans la section Revendications des utilisateurs de la boîte de dialogue Attributs utilisateur, effectuez les étapes suivantes pour ajouter le jeton SAML, comme indiqué dans le tableau ci-dessous :

    | Name | Attribut source|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    1. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    1. Laissez le champ **Espace de noms** vide.

    1. Sélectionnez Source comme **Attribut**.

    1. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    1. Cliquez sur **OK**

    1. Cliquez sur **Enregistrer**.


1. Dans la section **Configurer Citrix NetScaler**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Citrix NetScaler.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Citrix NetScaler**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-citrix-netscaler-sso"></a>Configurer l’authentification unique Citrix NetScaler

- [Configurer l’authentification unique Citrix NetScaler pour l’authentification basée sur l’en-tête](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Configurer l’authentification unique Citrix NetScaler pour l’authentification Kerberos](citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Publication du serveur web 

1. Créez un **serveur virtuel**.

    a. Accédez à **Traffic Management > Load Balancing > Services** (Gestion de trafic > Équilibrage de charge > Services).
    
    b. Cliquez sur **Add**.

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

    c. Spécifiez les informations du serveur web qui exécute les applications ci-dessous :
    * **Nom du service** :
    * **Adresse IP du serveur/ Serveur existant**
    * **Protocole**
    * **Port**

     ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Configuration de l’équilibreur de charge

1. Pour configurer l’équilibreur de charge, effectuez les étapes suivantes :

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/load01.png)

    a. Accédez à **Traffic Management > Load Balancing > Virtual Servers** (Gestion de trafic > Équilibrage de charge > Serveurs virtuels).

    b. Cliquez sur **Add**.

    c. Spécifiez les informations ci-dessous :

    * **Nom**
    * **Protocole**
    * **Adresse IP**
    * **Port**
    * Cliquez sur **ok**.

### <a name="bind-virtual-server"></a>Lier le serveur virtuel

Liez l’équilibreur de charge au serveur virtuel créé précédemment.

![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind01.png)

![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Lier le certificat

Comme nous publierons ce service en tant que SSL, liez le certificat de serveur, puis testez votre application.

![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind03.png)

![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profil SAML de Citrix ADC

### <a name="create-authentication-policy"></a>Créer une stratégie d’authentification

1. Accédez à **Security > AAA – Application Traffic > Policies > Authentication > Authentication Policies** (Sécurité > AAA – Trafic d’application > Stratégies > Authentification > Stratégies d’authentification).

2. Cliquez sur **Add** (Ajouter), puis spécifiez les informations.

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/policy01.png)

    a. Nom de la **stratégie d’authentification**.

    b. Expression : **true**.

    c. Type d’action **SAML**.

    d. Action = Cliquez sur **Add** (suivez l’Assistant de création d’un serveur SAML d’authentification).
    
    e. Cliquez sur Create (Créer) dans la **stratégie d’authentification**.

### <a name="create-authentication-saml-server"></a>Créer un serveur SAML d’authentification

1. Procédez comme suit :

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/server01.png)

    a. Spécifiez le **nom**.

    b. Importez des métadonnées (spécifiez l’URL des métadonnées de fédération à partir de l’interface utilisateur Azure SAML que vous avez copiée ci-dessus).
    
    c. Spécifiez le **nom de l’émetteur**.

    d. Cliquez sur **Create** (Créer).

### <a name="create-authentication-virtual-server"></a>Créer un serveur virtuel d’authentification

1.  Accédez à **Security > AAA - Application Traffic >> Authentication Virtual Servers** (Sécurité > AAA – Trafic d’application >> Serveurs virtuels d’authentification).

2.  Cliquez sur **Add** (Ajouter) et effectuez les étapes suivantes :

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/server02.png)

    a.  Entrez un **nom**.

    b.  Choisissez **Non-Addressable** (Non adressable).

    c.  Protocole **SSL**.

    d.  Cliquez sur **OK**.

    e.  Cliquez sur **Continuer**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurer le serveur virtuel d’authentification pour utiliser Azure AD

Vous devrez modifier les 2 sections du serveur virtuel d’authentification.

1.  **Advanced Authentication Policies** (Stratégies d’authentification avancées)

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual01.png)

    a. Sélectionnez la **stratégie d’authentification** que vous avez créée précédemment.

    b. Cliquez sur **Bind** (Lier).

      ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual02.png)

2. **Form Based Virtual Servers** (Serveurs virtuels basés sur des formulaires)

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual03.png)

    a.  Vous devez fournir un nom de domaine complet (**FQDN**), car il est imposé par l’interface utilisateur.

    b.  Choisissez l’équilibreur de charge de serveur virtuel (**Virtual Server Load Balancer**) que vous souhaitez protéger avec Azure AD Authentication.

    c.  Cliquez sur **Bind** (Lier).

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Veillez également à cliquer sur **Done** (Terminé) dans la page Authentication Virtual Server Configuration (Configuration du serveur virtuel d’authentification).

3. Vérifiez les modifications. Accédez à l’URL de l’application. Vous devez voir votre page de connexion de locataire à la place de l’accès non authentifié précédent.

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-header-based-authentication"></a>Configurer l’authentification unique Citrix NetScaler pour l’authentification basée sur l’en-tête

### <a name="citrix-adc-configuration"></a>Configuration de Citrix ADC

### <a name="create-an-rewrite-action"></a>Créer une action de réécriture

1. Accédez à **AppExpert > Rewrite > Rewrite Actions** (AppExpert > Réécrire > Actions de réécriture).
 
    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header01.png)

2.  Cliquez sur **Add**.

    a.  Spécifiez le **nom**.

    b.  Type = **INSERT_HTTP_HEADER**.

    c.  Spécifiez le **nom de l’en-tête** (SecretID dans cet exemple).

    d.  Spécifiez l’expression **aaa.USER.ATTRIBUTE(`mySecretID`)** , où **mySecretID** est la revendication SAML Azure AD envoyée à Citrix ADC.

    e.  Cliquez sur **Créer**.

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header02.png)
 
### <a name="create-a-rewrite-policy"></a>Créez une stratégie de réécriture.

1.  Accédez à **AppExpert > Rewrite > Rewrite Policies** (AppExpert > Réécrire > Stratégies de réécriture).
 
    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header03.png)

2.  Cliquez sur **Add**.

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header04.png)

    a.  Spécifiez le **nom**.

    b.  Choisissez l’**action** créée précédemment.

    c. Expression : spécifiez **true**.

    d.  Cliquez sur **Create** (Créer).

### <a name="bind-rewrite-policy-to-virtual-servers"></a>Lier la stratégie de réécriture aux serveurs virtuels

1. Pour lier une stratégie de réécriture à un serveur virtuel spécifique à l’aide de l’interface graphique utilisateur

2. Accédez à **Traffic Management > Load Balancing > Virtual Servers** (Gestion de trafic > Équilibrage de charge > Serveurs virtuels).

3. Dans la liste des serveurs virtuels du volet d’informations, sélectionnez le **serveur virtuel** auquel vous souhaitez lier la stratégie de réécriture, puis cliquez sur **Open** (Ouvrir).

4. Dans la boîte de dialogue Configure Virtual Server (Load Balancing) (Configurer le serveur virtuel (équilibrage de charge)), sélectionnez l’onglet **Policies** (Stratégies). Toutes les stratégies configurées sur NetScaler apparaissent dans la liste.
 
    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header06.png)

5.  Cochez la **case** en regard du nom de la stratégie que vous souhaitez lier à ce serveur virtuel.

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header07.png)

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header08.png)
 
6.  Cliquez sur **OK**. Un message s’affiche dans la barre d’état, indiquant que la stratégie a été configurée avec succès.

### <a name="modify-saml-server-to-extract-attributes-from-claim"></a>Modifier le serveur SAML pour extraire les attributs de la revendication

1.  Accédez à **Security > AAA - Application Traffic >Policies > Authentication > Advanced Policies > Actions > Servers** (Sécurité > AAA – Trafic d’application > Stratégies > Authentification > Stratégies avancées > Actions > Serveurs).

2.  Sélectionnez le **serveur SAML d’authentification** approprié pour l’application.
 
    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header09.png)

3. Dans la section Attribut, tapez les attributs SAML que vous souhaitez extraire en utilisant « , ». Dans le cas présent, nous spécifions l’attribut appelé **mySecretID**.
 
    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header10.png)

4. Vérifiez votre accès aux applications.

    ![Configuration de Citrix NetScaler](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-citrix-netscaler-test-user"></a>Créer un utilisateur de test Citrix NetScaler

Dans cette section, un utilisateur appelé B.Simon est créé dans Citrix NetScaler. Citrix NetScaler prend en charge l’attribution d’utilisateurs juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si l’utilisateur souhaité n’existe pas déjà dans Citrix NetScaler, il est créé après l’authentification.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez l’[équipe du support technique de Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Citrix NetScaler dans le panneau d’accès doit vous connecter automatiquement à l’application Citrix NetScaler pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Citrix NetScaler avec Azure AD](https://aad.portal.azure.com/)

- [Configurer l’authentification unique Citrix NetScaler pour l’authentification Kerberos](citrix-netscaler-tutorial.md)
