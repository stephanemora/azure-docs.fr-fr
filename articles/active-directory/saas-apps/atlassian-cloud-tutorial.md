---
title: 'Tutoriel : Intégration d’Azure Active Directory dans Atlassian Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Atlassian Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: ca75aea393e4850eb302cb03914296d7c1eaa951
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517646"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutoriel : Intégrer Atlassian Cloud à Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer Atlassian Cloud à Azure Active Directory (Azure AD). Lorsque vous intégrez Atlassian Cloud à Azure AD, vous pouvez :

* Dans Azure AD, contrôlez qui a accès à Atlassian Cloud.
* Permettre à vos utilisateurs de se connecter automatiquement à Atlassian Cloud avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous n’en avez pas, vous pouvez obtenir un essai gratuit d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Atlassian Cloud pour lequel l’authentification unique est activée.
* Si vous souhaitez activer l’authentification unique SAML (Security Assertion Markup Language) pour les produits Atlassian Cloud, vous devez configurer Atlassian Access. En savoir plus sur [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 

* Atlassian Cloud prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identités**
* Atlassian Cloud prend en charge le [provisionnement et le déprovisionnement automatiques des utilisateurs](atlassian-cloud-provisioning-tutorial.md)
* Une fois que vous avez configuré Atlassian Cloud, vous pouvez appliquer le contrôle de session, qui protège l'exfiltration et l'infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Ajout d’Atlassian Cloud à partir de la galerie

Pour configurer l’intégration d’Atlassian Cloud à Azure AD, vous devez ajouter Atlassian Cloud de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Atlassian Cloud** dans la zone de recherche.
1. Sélectionnez **Atlassian Cloud** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Atlassian Cloud à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Atlassian Cloud associé.

Pour configurer et tester l’authentification unique (SSO) Azure AD avec Atlassian Cloud, suivez les indications des sections ci-après :

1. **[Configurer Azure AD avec l'authentification unique Atlassian](#configure-azure-ad-sso)** - pour permettre à vos utilisateurs d'utiliser l'authentification unique Azure AD basée sur SAML avec Atlassian Cloud.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Créer un utilisateur de test Atlassian Cloud](#create-atlassian-cloud-test-user)** pour avoir un équivalent de B. Simon dans Atlassian Cloud lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Pour automatiser la configuration dans Atlassian Cloud, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Une fois l’extension ajoutée au navigateur, le fait de cliquer sur **Configurer Atlassian Cloud** vous redirige vers l’application Atlassian Cloud. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Atlassian Cloud. Cette extension de navigateur configure automatiquement l’application pour vous.

    ![Configuration](common/setup-sso.png)

1. Si vous voulez configurer manuellement Atlassian Cloud, connectez-vous à votre site d’entreprise Atlassian Cloud en tant qu’administrateur, puis effectuez les étapes suivantes.

1. Avant de commencer, accédez à l'instance de votre produit Atlassian et copiez/enregistrez son URL.
   > [!NOTE]
   > L'URL doit correspondre au modèle `https://<instancename>.atlassian.net`.

   ![image](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Ouvrez le [portail d'administration Atlassian](https://admin.atlassian.com/) et cliquez sur le nom de votre organisation.

   ![image](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. Vous devez vérifier votre domaine avant de passer à la configuration de l’authentification unique. Pour plus d’informations, consultez le document [Domain verification(Vérification de domaine) d’Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).
1. Sur l'écran du portail d'administration Atlassian, sélectionnez **Sécurité** dans le tiroir de gauche.

   ![image](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Sur l'écran de sécurité du portail d'administration Atlassian, sélectionnez **Authentification unique SAML** dans le tiroir de gauche.

   ![image](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Cliquez sur **Ajouter la configuration SAML** et laissez la page ouverte.

   ![image](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![image](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. Sur le [portail Azure](https://portal.azure.com/), accédez à la page d'intégration de l'application **Atlassian Cloud**, recherchez la section **Gérer** et sélectionnez **Configurer l'authentification unique**.

   ![image](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.

   ![image](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Sur la page **Configurer l'authentification unique avec SAML**, faites défiler jusqu'à **Configurer Atlassian Cloud**.
   
   a. Cliquez sur **URL de configuration**.

   ![image](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Copiez la valeur du champ **Identificateur Azure AD** à partir du portail Azure et collez-la dans la zone de texte **ID d'entité du fournisseur d'identité** d'Atlassian.
   
   c. Copiez la valeur du champ **URL de connexion** à partir du portail Azure et collez-la dans la zone de texte **URL d'authentification unique du fournisseur d'identité** d'Atlassian.

   ![image](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![image](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![image](./media/atlassian-cloud-tutorial/certificate.png)

   ![image](./media/atlassian-cloud-tutorial/certificate-1.png)

1. **Ajoutez/enregistrez** la configuration SAML dans Atlassian.

1. Si vous souhaitez configurer l'application en mode lancé par le **fournisseur d'identité**, modifiez la section **Configuration SAML de base** de la page **Configurer l'authentification unique avec SAML** dans Azure, puis ouvrez la page **Authentification unique SAML** sur le portail d'administration Atlassian.

   a. Copiez la valeur du champ **ID d'entité du fournisseur de services** d'Atlassian, collez-la dans la zone **Identificateur (ID d'entité)** d'Azure et définissez-la comme valeur par défaut.
   
   b. Copiez la valeur du champ **URL Assertion Consumer Service du fournisseur de services** d'Atlassian, collez-la dans la zone **URL de réponse (URL Assertion Consumer Service)** d'Azure et définissez-la comme valeur par défaut.
   
   c. Copiez la valeur du champ **URL de l'instance**, que vous avez copiée à l'étape 1, et collez-la dans la zone **État de relais** d'Azure.

   ![image](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/urls.png)
   
1. Si vous souhaitez configurer l'application en mode initié par le **fournisseur de services**, modifiez la section **Configuration SAML de base** de la page **Configurer l'authentification unique avec SAML** dans Azure. Copiez l'**URL de l'instance** (étape 1) et collez-la dans la zone **URL de connexion** d'Azure.

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. L’application Atlassian Cloud attend les assertions SAML dans un certain format, ce qui oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. Vous pouvez modifier le mappage d'attribut en cliquant sur l'icône **Modifier**. 

   ![image](./media/atlassian-cloud-tutorial/default-attributes.png)
   
   1. Mappage d'attributs pour un locataire Azure AD doté d'une licence Office 365
      
      a. Cliquez sur la revendication **Identificateur d'utilisateur unique (ID de nom)** .

      ![image](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud s'attend à ce que **nameidentifier** (**Identificateur d'utilisateur unique**) soit mappé avec l'adresse e-mail de l'utilisateur (**user.email**). Modifiez l'**attribut source** et remplacez-le par **user.mail**. Enregistrez les modifications apportées à la revendication.

      ![image](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. Les mappages d'attributs finaux doivent se présenter comme suit.

      ![image](common/default-attributes.png)
      
   1. Mappage d'attributs pour un locataire Azure AD sans licence Office 365 

      a. Cliquez sur la revendication **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

      ![image](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Azure ne renseigne pas l'attribut **user.mail** pour les utilisateurs créés dans des locataires Azure AD sans licences Office 365 et stocke l'e-mail de ces utilisateurs dans l'attribut **userprincipalname**. Atlassian Cloud s'attend à ce que **nameidentifier** (**Identificateur d'utilisateur unique**) soit mappé avec l'adresse e-mail de l'utilisateur (**user.userprincipalname**).  Modifiez l'**attribut source** et remplacez-le par **user.userprincipalname**. Enregistrez les modifications apportées à la revendication.

      ![image](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. Les mappages d'attributs finaux doivent se présenter comme suit.

      ![image](common/default-attributes.png)
     
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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Atlassian Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Atlassian Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](./media/atlassian-cloud-tutorial/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-atlassian-cloud-test-user"></a>Créer un utilisateur de test Atlassian Cloud

Pour permettre aux utilisateurs d’Azure AD de se connecter à Atlassian Cloud, approvisionnez manuellement les comptes d’utilisateur dans Atlassian Cloud en suivant les étapes ci-dessous :

1. Dans le volet **Administration**, sélectionnez **Utilisateurs**.

    ![Lien Utilisateurs Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Pour créer un utilisateur dans Atlassian Cloud, sélectionnez **Inviter un utilisateur**.

    ![Créer un utilisateur Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Dans la zone **Adresse e-mail**, entrez l’adresse e-mail de l’utilisateur, puis affectez l’accès à l’application.

    ![Créer un utilisateur Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Pour envoyer une invitation par e-mail à l’utilisateur, sélectionnez **Inviter les utilisateurs**. Une invitation est envoyée par e-mail à l’utilisateur ; dès qu’il aura accepté l’invitation, il sera actif dans le système.

> [!NOTE]
> Vous pouvez également créer des utilisateurs en bloc en cliquant sur le bouton **Créer en bloc** dans la section **Utilisateurs**.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Lorsque vous sélectionnez la vignette Atlassian Cloud dans le volet d’accès, vous devez être connecté automatiquement à l’application Atlassian Cloud pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Atlassian avec Azure AD](https://aad.portal.azure.com/)

- [Guide pratique pour protéger Atlassian Cloud avec une visibilité et des contrôles avancés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)