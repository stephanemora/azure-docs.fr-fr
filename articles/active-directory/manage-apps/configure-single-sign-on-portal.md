---
title: Configurer l’authentification unique – Azure Active Directory | Microsoft Docs
description: Ce didacticiel utilise le portail Azure pour configurer une authentification unique SAML pour une application avec Azure Active Directory (Azure AD).
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 12/06/2018
ms.author: barbkess
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 73e97a38913b3098ba2e14294ecf621add310428
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465715"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Tutoriel : Configurer l’authentification unique SAML pour une application avec Azure Active Directory

Ce didacticiel utilise le [portail Azure](https://portal.azure.com) pour configurer une authentification unique SAML pour une application avec Azure Active Directory (Azure AD). Utilisez ce tutoriel quand un [tutoriel propre à l’application](../saas-apps/tutorial-list.md) n’est pas disponible. 

Ce didacticiel utilise le Portail Azure pour :

> [!div class="checklist"]
> * Sélectionner le mode d’authentification unique SAML
> * Configurer le domaine et les URL spécifiques à l’application
> * Configurer les attributs de l’utilisateur
> * Créer un certificat de signature SAML
> * Affecter des utilisateurs à l’application
> * Configurer l’application pour l’authentification unique SAML
> * Tester les paramètres SAML

## <a name="before-you-begin"></a>Avant de commencer

1. Si l’application n’a pas été ajoutée à votre locataire Azure AD, consultez [Démarrage rapide : Ajouter une application à votre locataire Azure AD](add-application-portal.md).

2. Demandez les informations décrites dans [Configurer le domaine et les URL](#configure-domain-and-urls) à votre fournisseur d’applications.

3. Pour tester les étapes de ce didacticiel, nous recommandons l’utilisation d’un environnement de non-production. Si vous n’avez pas d’environnement de non-production Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

4. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur d’application cloud ou administrateur d’application pour votre locataire Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Sélectionner un mode d'authentification unique

Une fois qu’une application est ajoutée à votre locataire Azure AD, vous êtes prêt à configurer son authentification unique.

Pour ouvrir les paramètres de l'authentification unique :

1. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**. 

2. Dans le panneau **Azure Active Directory**, cliquez sur **Applications d’entreprise**. Le panneau **Toutes les applications** s’ouvre pour afficher un échantillon aléatoire des applications de votre locataire Azure AD. 

3. À partir du menu déroulant **Type d’Application**, sélectionnez **Toutes les Applications**, puis cliquez sur **Appliquer**.

4. Saisissez le nom de l’application pour laquelle vous souhaitez configurer l’authentification unique. Choisissez votre propre application, ou entrez **GitHub-test** pour configurer l’application que vous avez ajoutée dans le guide de démarrage rapide [Ajouter une application](add-application-portal.md).

5. Cliquez sur **Authentification unique**. Sous **Mode d’authentification unique**, **Authentification SAML** apparaît comme option par défaut. 

    ![Options de configuration](media/configure-single-sign-on-portal/config-options.png)

6. Cliquez sur **Enregistrer** dans la partie supérieure du panneau. 

## <a name="configure-domain-and-urls"></a>Configurer le domaine et les URL

Pour configurer le domaine et les URL :

1. Contactez le fournisseur de l’application pour obtenir les informations appropriées pour les paramètres suivants :

    | Paramètres de configuration | Initiée par SP | Initiée par idP | Description |
    |:--|:--|:--|:--|
    | URL de connexion | Obligatoire | Ne spécifiez pas | Lorsqu’un utilisateur ouvre cette URL, le fournisseur de services redirige vers Azure AD pour authentifier et connecter l’utilisateur. Azure AD utilise l’URL pour démarrer l’application à partir d’Office 365 ou du panneau d’accès Azure AD. Quand elle est vide, Azure AD s’appuie sur le fournisseur d’identité pour lancer l’authentification unique quand un utilisateur démarre l’application.|
    | Identificateur (ID d'entité) | Requis pour certaines applications | Requis pour certaines applications | Identifie de façon unique l’application pour laquelle l’authentification unique est en cours de configuration. Azure AD envoie l’identificateur à l’application en tant que paramètre Audience du jeton SAML. L’application est censée le valider. Cette valeur apparaît également en tant qu’ID d’entité dans les métadonnées SAML fournies par l’application.|
    | URL de réponse | Facultatif | Obligatoire | Spécifie l’adresse où l’application attend le jeton SAML. L’URL de réponse est aussi appelée URL ACS (Assertion Consumer Service). |
    | État de relais | Facultatif | Facultatif | Indique à l’application où rediriger l’utilisateur une fois l’authentification terminée. En général, la valeur est une URL valide pour l’application. Cependant, certaines applications utilisent ce champ différemment. Pour plus d’informations, consultez le fournisseur de l’application.

2. Entrez les informations. Pour afficher tous les paramètres, cliquez sur **Afficher les paramètres d’URL avancés**.

    ![Options de configuration](media/configure-single-sign-on-portal/config-urls.png)

3. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

4. Il existe un bouton **Paramètres SAML de test** dans cette section. Exécuter ce test ultérieurement dans ce didacticiel au sein de la section [Tester l’authentification unique](#test-single-sign-on).

## <a name="configure-user-attributes"></a>Configurer les attributs de l’utilisateur

Les attributs de l’utilisateur vous permettent de contrôler les informations qu’Azure AD envoie à l’application dans le jeton SAML chaque fois qu’un utilisateur se connecte. Par exemple, Azure AD peut lui envoyer le nom, l’adresse e-mail et l’ID d’employé de l’utilisateur. 

Ces attributs peuvent être obligatoires ou facultatifs afin que l’authentification unique fonctionne correctement. Pour plus d’informations, consultez le [didacticiel spécifique à l’application](../saas-apps/tutorial-list.md), ou demandez au fournisseur de l’application.

1. Pour afficher toutes les options, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**.

    ![Configurer les attributs de l’utilisateur](media/configure-single-sign-on-portal/config-user-attributes.png)

2. Saisissez **l’Identificateur de l'utilisateur**.

    L’identificateur d’utilisateur identifie de façon unique chaque utilisateur au sein de l’application. Par exemple, si l’adresse e-mail est le nom d’utilisateur et l’identificateur unique, définissez la valeur sur *user.mail*.

3. Pour plus d’attributs du jeton SAML, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**.

4. Pour ajouter un attribut aux **attributs du jeton SAML**, cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans le menu.

5. Cliquez sur **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.
 
## <a name="create-a-saml-signing-certificate"></a>Créer un certificat de signature SAML

Azure AD utilise un certificat pour signer les jetons SAML qu’il envoie à l’application. 

1. Pour afficher toutes les options, cliquez sur **Afficher les options avancées de signature de certificat**.

    ![Configurer les certificats](media/configure-single-sign-on-portal/config-certificate.png)

2. Pour configurer un certificat, cliquez sur **Créer un nouveau certificat**.

3. Dans le panneau **Créer un nouveau certificat**, définissez la **date d’expiration**, puis cliquez sur **Enregistrer**.

4. Cliquez sur **Activer le nouveau certificat**.

5. Pour plus d’informations, consultez [Options avancées de signature de certificat](certificate-signing-options.md).

6. Pour conserver les modifications que vous avez effectuées jusqu'à présent, veillez à cliquer sur **Enregistrer** en haut du panneau **Authentification unique**. 

## <a name="assign-users-to-the-application"></a>Affecter des utilisateurs à l’application

Microsoft recommande de tester l’authentification unique avec plusieurs utilisateurs ou groupes avant de déployer l’application au sein de votre organisation.

Pour affecter un utilisateur ou un groupe à l’application :

1. Ouvrez l’application dans le portail, si elle n’est pas encore ouverte.
2. Dans le panneau gauche des applications, cliquez sur **Utilisateurs et groupes**.
3. Cliquez sur **Add User**.
4. Dans le panneau **Ajouter une affectation**, cliquez sur **Utilisateurs et groupes**.
5. Pour rechercher un utilisateur spécifique, tapez le nom d’utilisateur dans la zone **Sélectionner**, cliquez sur la case à cocher proche de la photo de profil ou du logo de l’utilisateur, puis cliquez sur **Sélectionner**. 
6. Recherchez votre nom d’utilisateur actuel, puis sélectionnez-le. Vous pouvez éventuellement sélectionner plusieurs utilisateurs.
7. Dans le panneau **Ajouter une affectation**, cliquez sur **Affecter**. Une fois terminé, les utilisateurs sélectionnés s’affichent dans la liste **Utilisateurs et groupes**.

## <a name="configure-the-application-to-use-azure-ad"></a>Configuration de l’application pour utiliser Azure AD

Vous avez presque terminé.  Dans la dernière étape, vous devez configurer l’application pour utiliser Azure AD comme fournisseur d’identité SAML. 

1. Faites défiler jusqu'à la fin du panneau **Authentification unique** pour votre application. 

    ![Configurer une application](media/configure-single-sign-on-portal/configure-app.png)

2. Cliquez sur **Configurer application** dans le portail, puis suivez les instructions.
3. Créez manuellement des comptes d’utilisateur dans l’application afin de tester l’authentification unique. Créez les comptes d’utilisateur que vous avez affectés à l’application dans la [section précédente](#assign-users-to-the-application). 

## <a name="test-single-sign-on"></a>Tester l’authentification unique

Vous êtes maintenant prêt à tester vos paramètres.  

1. Ouvrez les paramètres d’authentification unique pour votre application. 
2. Faites défiler jusqu’à la section **Configurer le domaine et les URL**.
2. Cliquez sur **Tester les paramètres SAML**. Les options de tests s’affichent.

    ![Options de test d’authentification unique](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Cliquez sur **Se connecter en tant qu’utilisateur actuel**. Ce test vous permet de commencer par voir si l’authentification unique fonctionne pour vous, l’administrateur.
4. Si une erreur se produit, un message d’erreur s’affiche. Copiez et collez les détails dans la case **À quoi ressemble l’erreur ?**.

    ![Obtenir l’aide de résolution](media/configure-single-sign-on-portal/error-guidance.png)

5. Cliquez sur **Obtenir l’aide de résolution**. L’aide de résolution et de la cause racine apparaît.  Dans cet exemple, l’utilisateur n’était pas affecté à l’application.

    ![Corriger les erreurs](media/configure-single-sign-on-portal/fix-error.png)

6. Lisez l’aide de résolution et, si nécessaire, cliquez sur **Corriger**.

7. Réexécutez le test jusqu'à ce qu’il se termine correctement.



## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez configuré les paramètres d’authentification unique pour une application. Après avoir terminé la configuration, vous avez affecté un utilisateur à l’application et vous avez configuré l’application pour utiliser l’authentification unique SAML. Une fois tout ce travail terminé, vous avez vérifié que l’authentification SAML fonctionne correctement.

Voici les étapes que vous avez effectuées :
> [!div class="checklist"]
> * Sélectionner le mode d’authentification unique SAML
> * Contacter le fournisseur de l’application pour configurer le domaine et les URL
> * Configurer les attributs de l’utilisateur
> * Créer un certificat de signature SAML
> * Affecter manuellement des utilisateurs ou des groupes à l’application
> * Configurer l’application pour utiliser Azure AD en tant que fournisseur d’identité SAML
> * Tester l’authentification unique SAML

Pour déployer l’application pour d’autres utilisateurs de votre organisation, nous vous recommandons d’utiliser le provisionnement d’utilisateur automatique.

> [!div class="nextstepaction"]
>[Découvrir comment affecter des utilisateurs avec le provisionnement automatique](configure-automatic-user-provisioning-portal.md)


