---
title: 'Tutoriel : Intégration d’Azure Active Directory dans Atlassian Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3a54b096019e9e38bc800ae313016a430062dab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74964308"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutoriel : Intégrer Atlassian Cloud à Azure Active Directory

Dans ce didacticiel, vous allez apprendre à intégrer Atlassian Cloud à Azure Active Directory (Azure AD). Lorsque vous intégrez Atlassian Cloud à Azure AD, vous pouvez :

* Dans Azure AD, contrôlez qui a accès à Atlassian Cloud.
* Permettre à vos utilisateurs de se connecter automatiquement à Atlassian Cloud avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous n’en avez pas, vous pouvez obtenir un essai gratuit d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement Atlassian Cloud pour lequel l’authentification unique est activée.
* Si vous souhaitez activer l’authentification unique SAML (Security Assertion Markup Language) pour les produits Atlassian Cloud, vous devez configurer Atlassian Access. En savoir plus sur [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. 

* Atlassian Cloud prend en charge l’authentification unique initiée par **le fournisseur de services et le fournisseur d’identités**
* Atlassian Cloud prend en charge le [provisionnement et le déprovisionnement automatiques des utilisateurs](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Ajout d’Atlassian Cloud à partir de la galerie

Pour configurer l’intégration d’Atlassian Cloud à Azure AD, vous devez ajouter Atlassian Cloud de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Atlassian Cloud** dans la zone de recherche.
1. Sélectionnez **Atlassian Cloud** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Atlassian Cloud à l’aide d’un utilisateur de test appelé **B. Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Atlassian Cloud associé.

Pour configurer et tester l’authentification unique (SSO) Azure AD avec Atlassian Cloud, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Atlassian Cloud](#configure-atlassian-cloud-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Atlassian Cloud](#create-atlassian-cloud-test-user)** pour avoir un équivalent de B. Simon dans Atlassian Cloud lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Atlassian Cloud**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode Initié par le **fournisseur d’identité**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://auth.atlassian.com/saml/<unique ID>`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Cliquez sur **Définir des URL supplémentaires**.

    d. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Les valeurs ci-dessus ne sont pas réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Ces valeurs réelles sont accessibles dans l’écran de **configuration SAML pour Atlassian Cloud**, comme nous le verrons plus loin dans la section **Configurer l’authentification unique Atlassian Cloud** de ce tutoriel.

1. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<instancename>.atlassian.net`

    > [!NOTE]
    > La valeur d’URL de connexion n’est pas réelle. Collez la valeur de l’instance qui vous permet de vous connecter au portail d’administration d’Atlassian Cloud.

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. L’application Atlassian Cloud attend les assertions SAML dans un certain format, ce qui oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut, où **nameidentifier** est mappé avec **user.userprincipalname**. L’application Atlassian Cloud s’attend à ce que **nameidentifier** soit mappé avec **user.mail**. Vous devez donc modifier le mappage d’attribut en cliquant sur l’icône **Modifier**.

    ![image](common/edit-attribute.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Set up Atlassian Cloud** (Configurer Atlassian Cloud), copiez la ou les URL appropriées correspondant à vos besoins.

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

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Atlassian Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Atlassian Cloud**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-atlassian-cloud-sso"></a>Configurer l’authentification unique Atlassian Cloud

1. Pour automatiser la configuration dans Atlassian Cloud, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Install the extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

1. Après l’ajout de l’extension au navigateur, cliquez sur **Setup Atlassian Cloud** pour être orienté vers l’application Atlassian Cloud. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Atlassian Cloud. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 7.

    ![Configuration](common/setup-sso.png)

1. Si vous souhaitez configurer manuellement Atlassian Cloud, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Atlassian Cloud en tant qu’administrateur et effectuez les étapes suivantes :

1. Vous devez vérifier votre domaine avant de passer à la configuration de l’authentification unique. Pour plus d’informations, consultez le document [Domain verification(Vérification de domaine) d’Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

1. Dans le volet de gauche, sélectionnez **Sécurité** > **Authentification unique SAML**. Si ce n’est pas déjà fait, abonnez-vous à Atlassian Identity Manager.

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Dans la fenêtre **Ajouter une configuration SAML**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Dans la zone **Identity provider Entity ID**, collez l’**Identificateur Azure AD** que vous avez copié à partir du portail Azure.

    b. Dans la zone **Identity provider SSO URL**, collez la valeur de l’**URL de connexion** que vous avez copiée à partir du portail Azure.

    c. Ouvrez le certificat téléchargé sur le Portail Azure dans un fichier .txt, copiez la valeur (sans les lignes *Begin Certificate* et *End Certificate*), puis collez-la dans la zone **Certificat X509 public**.

    d. Cliquez sur **Enregistrer la configuration**.

1. Pour vérifier que les URL configurées sont correctes, mettez à jour les paramètres d’Azure AD en suivant les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Dans la fenêtre SAML, copiez l’**ID d’identité SP**. Ensuite, collez-le dans la zone **Identificateur** du portail Azure, sous **Configuration SAML de base** d’Atlassian Cloud.

    b. Dans la fenêtre SAML, copiez l’**URL Assertion Consumer Service SP**. Ensuite, collez-le dans la zone **URL de réponse** du portail Azure, sous **Configuration SAML de base** d’Atlassian Cloud. L’URL d’authentification est l’URL locataire de votre Atlassian Cloud.

    > [!NOTE]
    > Si vous êtes déjà client, après avoir mis à jour les valeurs **ID d’identité SP** et **URL Assertion Consumer Service SP** sur le Portail Azure, sélectionnez **Oui, mettre à jour la configuration**. Si vous êtes un nouveau client, vous pouvez ignorer cette étape.

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

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Atlassian avec Azure AD](https://aad.portal.azure.com/)
