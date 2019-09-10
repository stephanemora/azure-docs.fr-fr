---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à F5 | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165522"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à F5

Dans ce tutoriel, vous allez découvrir comment intégrer F5 à Azure Active Directory (Azure AD). Quand vous intégrez F5 à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à F5.
* Permettre à vos utilisateurs de se connecter automatiquement à F5 avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement F5 pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* La fonctionnalité d’authentification unique F5 peut être configurée de trois façons différentes.

- [Configurer l’authentification unique F5 pour une application basé sur l’en-tête](#configure-f5-single-sign-on-for-header-based-application)

- [Configurer l’authentification unique F5 pour une application Kerberos](kerbf5-tutorial.md)

- [Configurer l’authentification unique F5 pour une application Kerberos avancée](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Ajout de F5 à partir de la galerie

Pour configurer l’intégration de F5 avec Azure AD, vous devez ajouter F5, disponible dans la galerie, à votre liste d’applications SaaS managées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **F5** dans la zone de recherche.
1. Sélectionnez **F5** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurer et tester l’authentification unique Azure AD pour F5

Configurez et testez l’authentification unique Azure AD avec F5 à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur F5 associé.

Pour configurer et tester l’authentification unique Azure AD avec F5, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique F5](#configure-f5-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test F5](#create-f5-test-user)** pour disposer, dans F5, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans la page d’intégration de l’application **F5** du [portail Azure](https://portal.azure.com/), recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<YourCustomFQDN>.f5.com/`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<YourCustomFQDN>.f5.com/`

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**, puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer F5**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à F5.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **F5**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-f5-sso"></a>Configurer l’authentification unique F5

- [Configurer l’authentification unique F5 pour une application Kerberos](kerbf5-tutorial.md)

- [Configurer l’authentification unique F5 pour une application Kerberos avancée](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configurer l’authentification unique F5 pour une application basé sur l’en-tête

1. Ouvrez une nouvelle fenêtre de navigateur web et connectez-vous à votre site d’entreprise F5 (basé sur l’en-tête) en tant qu’administrateur, puis effectuez les étapes suivantes :

1. Vous devez importer le certificat de métadonnées dans F5 (basé sur l’en-tête) qui sera utilisé plus tard lors du processus d’installation. Accédez à **System > Certificate Management > Traffic Certificate Management >> SSL Certificate Lists** (Système > Gestion des certificats > Gestion des certificats de trafic > Liste des certificats SSL). Cliquez sur **Import** (Importer) en haut à droite.

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure12.png)
 
1. Vous avez également besoin d’un **certificat SSL** pour le nom d’hôte (`headerapp.superdemo.live`). Dans cet exemple, nous avons utilisé un certificat générique.

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure13.png)

1. Accédez à F5 (basé sur l’en-tête) BIG-IP. Cliquez sur **Access > Guided Configuration > Federation > SAML Service Provider** (Accès > Configuration guidée > Fédération > Fournisseur de services SAML).

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure01.png)

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure02.png)
 
1. Spécifiez l’ID d’entité (**Entity ID**) (identique à ce que vous avez configuré dans la configuration de l’application Azure AD).

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure03.png) 

1. Créez un serveur virtuel. Spécifiez l’adresse de destination (**Destination Address**). Le port de redirection (**Redirect Port**) est facultatif. Choisissez le certificat générique (**Wild Card Certificate**) (ou le **certificat** que vous avez chargé pour l’application) que nous avons chargé précédemment et la clé privée associée (**Associated Private Key**).

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure04.png) 

1. Chargez les **métadonnées** de configuration et spécifiez un nouveau nom pour le connecteur de fournisseur d’identité SAML (**Name for SAML IDP Connector**). Vous devez également spécifier le certificat de fédération qui a été chargé précédemment.

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure05.png)
 
1. **Créez un pool d’applications back-end** et spécifiez **la ou les adresses IP** des serveurs d’applications back-end.

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure06.png)

1. Sous Single Sign-on (Authentification unique), choisissez **HTTP header-based** (basé sur l’en-tête HTTP). Vous pouvez ajouter d’autres en-têtes en fonction de votre application. Pour obtenir la liste des variables de session SAML, consultez l’annexe.

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure07.png) 

1. Contactez l’[équipe de support technique F5 (basé sur l’en-tête)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) pour obtenir les détails de la documentation sur les **propriétés de vérification du point de terminaison**.

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure08.png)

1. Contactez l’[équipe de support technique F5 (basé sur l’en-tête)](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) pour obtenir les détails de la documentation sur les **propriétés de gestion de session**.

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure09.png)

1. **Passez en  revue la synthèse**, puis cliquez sur **Deploy** (Déployer).

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure10.png)

    ![Configuration de F5 (basé sur l’en-tête)](./media/headerf5-tutorial/configure11.png)

### <a name="create-f5-test-user"></a>Créer un utilisateur de test F5

Dans cette section, vous allez créer un utilisateur appelé B.Simon dans F5. Collaborez avec l’ [équipe du support client de F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) pour ajouter des utilisateurs à la plateforme F5. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette F5 dans le volet d’accès, vous devez être connecté automatiquement à l’application F5 pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer F5 avec Azure AD](https://aad.portal.azure.com/)

- [Configurer l’authentification unique F5 pour une application Kerberos](kerbf5-tutorial.md)

- [Configurer l’authentification unique F5 pour une application Kerberos avancée](advance-kerbf5-tutorial.md)

