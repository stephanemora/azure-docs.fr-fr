---
title: 'Tutoriel : Intégration de l’authentification unique Azure Active Directory à FortiGate SSL VPN | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299692"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Tutoriel : Intégration de l’authentification unique Azure Active Directory à FortiGate SSL VPN

Dans ce tutoriel, vous allez apprendre à intégrer FortiGate SSL VPN à Azure Active Directory (Azure AD). Quand vous intégrez FortiGate SSL VPN à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à FortiGate SSL VPN.
* Permettre à vos utilisateurs de se connecter automatiquement à FortiGate SSL VPN avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement FortiGate SSL VPN pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* FortiGate SSL VPN prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**.
* Après avoir configuré FortiGate SSL VPN, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>Ajout de FortiGate SSL VPN depuis la galerie

Pour configurer l’intégration de FortiGate SSL VPN avec Azure AD, vous devez ajouter FortiGate SSL VPN à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **FortiGate SSL VPN** dans la zone de recherche.
1. Sélectionnez **FortiGate SSL VPN** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Configurer et tester l’authentification unique (SSO) Azure AD pour FortiGate SSL VPN

Configurez et testez l’authentification unique Azure AD avec FortiGate SSL VPN pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur FortiGate SSL VPN associé.

Pour configurer et tester l’authentification unique Azure AD avec FortiGate SSL VPN, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique FortiGate SSL VPN](#configure-fortigate-ssl-vpn-sso)** pour configurer les paramètres d’authentification unique côté application.
    1. **Créer un utilisateur de test FortiGate SSL VPN** pour disposer, dans FortiGate SSL VPN, d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **FortiGate SSL VPN**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<FQDN>/remote/login`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<FQDN>/remote/saml/metadata`

    c. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://><FQDN/remote/saml/login`

    d. Dans la zone de texte **URL de déconnexion**, tapez une URL au format suivant : `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec les valeurs réelles de l’URL d’authentification, de l’identificateur, de l’URL de réponse et de l’URL de déconnexion. Pour obtenir ces valeurs, contactez [l’équipe du support technique FortiGate SSL VPN](mailto:tac_amer@fortinet.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application FortiGate SSL VPN s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application FortiGate SSL VPN s’attend à ce que quelques attributs supplémentaires, indiqués ci-après, soient passés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.
    
    | Nom |  Attribut source|
    | ------------ | --------- |
    | username | user.userprincipalname |
    | group | user.groups |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer FortiGate SSL VPN**, copiez la ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à FortiGate SSL VPN.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **FortiGate SSL VPN**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-a-security-group-for-the-test-user"></a>Créer un groupe de sécurité pour l’utilisateur de test

Dans cette section, vous allez créer un groupe de sécurité dans Azure Active Directory pour l’utilisateur de test. Ce groupe de sécurité sera utilisé par FortiGate pour accorder à l’utilisateur l’accès réseau via le VPN.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, puis **Groupes**.
1. Sélectionnez **Nouveau groupe** dans la partie supérieure de l’écran.
1. Dans les propriétés de **Nouveau groupe**, effectuez les étapes suivantes :
   1. Dans le champ **Type de groupe**, sélectionnez **Sécurité**.
   1. Dans le champ **Nom**, entrez `FortiGateAccess`.
   1. Dans le champ **Description du groupe**, entrez `Group for granting FortiGate VPN access`.
   1. Pour **Des rôles Azure AD peuvent être attribués au groupe (préversion)** , sélectionnez **Non**.
   1. Dans le champ **Type d’appartenance**, sélectionnez **Affectée**.
   1. Sous **Membres**, sélectionnez **Aucun membre sélectionné**.
   1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
   1. Sélectionnez **Create** (Créer).
1. Une fois de retour au panneau **Groupes** dans Azure Active Directory, localisez le groupe **Accès FortiGate** et notez l’**ID d’objet** pour une utilisation ultérieure.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Configurer l’authentification unique FortiGate SSL VPN

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Charger le certificat SAML Base64 sur l’appliance FortiGate

Après avoir terminé la configuration SAML de l’application FortiGate dans votre locataire, vous avez téléchargé le certificat SAML encodé en Base64. Celui-ci doit être téléchargé sur l’appliance FortiGate :

1. Connectez-vous au portail de gestion de votre appliance FortiGate.
1. Dans le menu de gauche, cliquez sur **Système**.
1. Sous **Système**, cliquez sur **Certificats**.
1. Cliquez sur **Importer** -> **Certificat distant**.
1. Accédez au certificat téléchargé à partir du déploiement de l’application FortiGate dans le locataire Azure, sélectionnez-le, puis cliquez sur **OK**

Une fois le certificat chargé, prenez note de son nom sous **Système** > **Certificats** > **Certificat distant**. Par défaut, il est nommé REMOTE_Cert_**N**, où **N** est une valeur entière.

### <a name="perform-fortigate-command-line-configuration"></a>Effectuer la configuration de FortiGate par ligne de commande

Les étapes suivantes requièrent la configuration de l’URL de déconnexion Azure. Cette URL contient un point d’interrogation (?). Des étapes spéciales sont nécessaires pour envoyer correctement ce caractère. Les étapes ne peuvent pas être effectuées à partir de la console CLI FortiGate. Au lieu de cela, établissez une session SSH pour l’appareil FortiGate à l’aide d’un outil tel que PuTTy. Si votre appliance FortiGate est une machine virtuelle Azure, vous pouvez effectuer les étapes suivantes à partir de la console série de la machine virtuelle Azure.

Pour effectuer ces étapes, vous aurez besoin des valeurs enregistrées précédemment :

- L’ID d’entité
- URL de réponse
- URL de déconnexion
- URL de connexion Azure
- Identificateur Azure AD
- URL de déconnexion Azure
- Nom du certificat SAML Base64 (REMOTE_Cert_N)

1. Établissez une session SSH pour votre appliance FortiGate et connectez-vous avec un compte administrateur FortiGate.
1. Exécutez les commandes suivantes :

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > **L’URL de déconnexion Azure** contient un caractère `?`. Vous devez entrer une séquence de touches spéciale pour fournir correctement l’URL de la console série FortiGate. En règle générale, l’URL est `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
   >
   > Pour entrer l’URL de déconnexion Azure dans la console série, entrez `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`.
   > 
   > Ensuite, sélectionnez CTRL + V, puis collez le reste de l’URL pour terminer la ligne : `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

### <a name="configure-fortigate-for-group-matching"></a>Configurer FortiGate pour la correspondance de groupe

Dans cette section, vous allez configurer FortiGate pour reconnaître l’ID d’objet du groupe de sécurité dans lequel l’utilisateur de test réside. Cela permettra à FortiGate de prendre des décisions d’accès en fonction de cette appartenance à un groupe.

Pour effectuer cette procédure, vous avez besoin de l’ID d’objet du groupe de sécurité **FortiGateAccess** créé précédemment

1. Établissez une session SSH pour votre appliance FortiGate et connectez-vous avec un compte administrateur FortiGate.
1. Exécutez les commandes suivantes :

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Créer des portails VPN FortiGate et une stratégie de pare-feu

Dans cette section, vous configurez les portails VPN FortiGate et la stratégie de pare-feu qui accordent l’accès au groupe de sécurité **FortiGateAccess** créé ci-dessus.

Collaborez avec l’ [équipe de support FortiGate](mailto:tac_amer@fortinet.com) pour ajouter les portails VPN et la stratégie de pare-feu à la plateforme VPN FortiGate. Vous devez effectuer ces étapes avant d’utiliser l’authentification unique.

## <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette FortiGate SSL VPN dans le panneau d’accès doit vous connecter automatiquement à l’application FortiGate SSL VPN pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Microsoft et FortiGate vous conseillent d’utiliser le client VPN de Fortinet, FortiClient, pour une expérience utilisateur optimale.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer FortiGate SSL VPN avec Azure AD](https://aad.portal.azure.com/)

- [Qu’est-ce que le contrôle de session dans Microsoft Cloud App Security ?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
