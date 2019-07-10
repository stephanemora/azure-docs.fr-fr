---
title: Configurer l’authentification unique – Azure Active Directory | Microsoft Docs
description: Ce didacticiel utilise le portail Azure pour configurer une authentification unique SAML pour une application avec Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f6707c780931eac58e2a870c321385e63bd948a
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550469"
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

2. Demandez les informations décrites dans [Configurer les options SAML de base](#configure-basic-saml-options) à votre fournisseur d’applications.

3. Pour tester les étapes de ce tutoriel, utilisez un environnement de non-production. Si vous n’avez pas d’environnement de non-production Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

4. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur d’application cloud ou administrateur d’application pour votre locataire Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Sélectionner un mode d'authentification unique

Une fois qu’une application est ajoutée à votre locataire Azure AD, vous êtes prêt à configurer son authentification unique.

Pour ouvrir les paramètres de l'authentification unique :

1. Dans le panneau de navigation gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

2. Sous **Gérer** dans le panneau de navigation **Azure Active Directory** qui s’affiche, sélectionnez **Applications d’entreprise**. Un échantillon aléatoire des applications dans votre locataire Azure AD s’affiche. 

3. Dans le menu **Type d’Application**, sélectionnez **Toutes les Applications**, puis sélectionnez **Appliquer**.

4. Saisissez le nom de l’application pour laquelle vous souhaitez configurer l’authentification unique. Vous pouvez, par exemple, entrer **GitHub-test** pour configurer l’application que vous avez ajoutée dans le guide de démarrage rapide [Ajouter une application](add-application-portal.md).  

     ![Capture d’écran montrant la barre de recherche d’application.](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Choisissez l’application pour laquelle vous souhaitez configurer l’authentification unique.

6. Sous la section **Gérer**, sélectionnez **Authentification unique**. 

7. Sélectionnez **SAML** pour configurer l’authentification unique. La page **Configurer l’authentification unique avec SAML - Préversion** s’affiche.

## <a name="configure-basic-saml-options"></a>Configurer les options SAML de base

Pour configurer le domaine et les URL :

1. Contactez le fournisseur de l’application pour obtenir les informations appropriées pour les paramètres suivants :

    | Paramètres de configuration | Initiée par SP | Initiée par idP | Description |
    |:--|:--|:--|:--|
    | Identificateur (ID d'entité) | Requis pour certaines applications | Requis pour certaines applications | Identifie de façon unique l’application pour laquelle l’authentification unique est en cours de configuration. Azure AD envoie l’identificateur à l’application en tant que paramètre Audience du jeton SAML. L’application est censée le valider. Cette valeur apparaît également en tant qu’ID d’entité dans les métadonnées SAML fournies par l’application.|
    | URL de réponse | Facultatif | Obligatoire | Spécifie l’adresse où l’application attend le jeton SAML. L’URL de réponse est aussi appelée URL ACS (Assertion Consumer Service). |
    | URL de connexion | Obligatoire | Ne spécifiez pas | Lorsqu’un utilisateur ouvre cette URL, le fournisseur de services redirige vers Azure AD pour authentifier et connecter l’utilisateur. Azure AD utilise l’URL pour démarrer l’application à partir d’Office 365 ou du panneau d’accès Azure AD. Quand elle est vide, Azure AD s’appuie sur le fournisseur d’identité pour lancer l’authentification unique quand un utilisateur démarre l’application.|
    | État de relais | Facultatif | Facultatif | Indique à l’application où rediriger l’utilisateur une fois l’authentification terminée. En règle générale, la valeur est une URL valide pour l’application. Toutefois, certaines applications utilisent ce champ différemment. Pour plus d’informations, consultez le fournisseur de l’application.
    | URL de déconnexion | Facultatif | Facultatif | Utilisé pour renvoyer les réponses de déconnexion SAML à l’application.


2. Pour modifier les options de configuration SAML de base, sélectionnez l’icône **Modifier** (crayon) dans le coin supérieur droit de la section **Configuration SAML de base**.

     ![Configurer les certificats](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. Dans les champs appropriés sur la page, entrez les informations indiquées par le fournisseur de l’application à l’étape 1.

4. En haut de la page, sélectionnez **Enregistrer**.

## <a name="configure-user-attributes-and-claims"></a>Configurer les attributs et revendications d’un utilisateur 

Vous pouvez contrôler les informations qu’Azure AD envoie à l’application dans le jeton SAML lorsqu’un utilisateur se connecte. Vous contrôlez ces informations en configurant les attributs utilisateur. Par exemple, vous pouvez configurer Azure AD pour envoyer le nom, l’e-mail et l’ID d’employé d’un utilisateur à l’application lorsqu’un utilisateur se connecte. 

Ces attributs peuvent être obligatoires ou facultatifs afin que l’authentification unique fonctionne correctement. Pour plus d’informations, consultez le [didacticiel spécifique à l’application](../saas-apps/tutorial-list.md), ou demandez au fournisseur de l’application.

1. Pour modifier les attributs et les revendications d’un utilisateur, sélectionnez l’icône **Modifier** (crayon) dans le coin supérieur droit de la section **Attributs et revendications utilisateur**.

   La **Valeur d’identificateur de nom** est définie sur la valeur par défaut *user.principalname*. L’identificateur d’utilisateur identifie de façon unique chaque utilisateur au sein de l’application. Par exemple, si l’adresse e-mail est le nom d’utilisateur et l’identificateur unique, définissez la valeur sur *user.mail*.

2. Pour modifier la **Valeur d’identificateur de nom**, sélectionnez l’icône **Modifier** (crayon) pour le champ **Valeur d’identificateur de nom**. Apportez les modifications appropriées au format de l’identificateur et à la source. Quand vous avez terminé, enregistrez les modifications. Pour plus d’informations sur la personnalisation de revendications, consultez l’article [Personnaliser les revendications émises dans le jeton SAML pour les applications d’entreprise](../develop/active-directory-saml-claims-customization.md).

3. Pour ajouter une revendication, sélectionnez **Ajouter une nouvelle revendication** en haut de la page. Entrez le **Nom** et sélectionnez la source appropriée. Si vous sélectionnez la source **Attribut**, vous devez choisir **l’attribut Source** à utiliser. Si vous sélectionnez la source **Translation**, vous devez choisir les éléments **Transformation** et **Parameter 1** à utiliser.

4. Sélectionnez **Enregistrer**. La nouvelle revendication s’affiche dans la table.
 
## <a name="generate-a-saml-signing-certificate"></a>Générer un certificat de signature SAML

Azure AD utilise un certificat pour signer les jetons SAML qu’il envoie à l’application. 

1. Pour générer un nouveau certificat, sélectionnez l’icône **Modifier** (crayon) dans le coin supérieur droit de la section **Certificat de signature SAML**.

2. Dans la section **Certificat de signature SAML**, sélectionnez **Nouveau certificat**.

3. Dans la ligne de nouveau certificat qui s’affiche, définissez la **Date d’expiration**. Pour plus d’informations sur les options de configuration disponibles, consultez l’article [Options avancées de signature de certificat](certificate-signing-options.md).

4. Sélectionnez **Enregistrer** en haut de la section **Certificat de signature SAML**. 

## <a name="assign-users-to-the-application"></a>Affecter des utilisateurs à l’application

Il est recommandé de tester l’authentification unique avec plusieurs utilisateurs ou groupes avant de déployer l’application au sein de votre organisation.

> [!NOTE]
>
> Ces étapes vous mènent à la section de configuration **Utilisateurs et groupes** dans le portail. Lorsque vous avez terminé, vous devrez revenir à la section **Authentification unique** pour suivre le tutoriel.

Pour affecter un utilisateur ou un groupe à l’application :

1. Ouvrez l’application dans le portail, si elle n’est pas encore ouverte.
2. Dans le volet de navigation de gauche de l’application, sélectionnez **Utilisateurs et groupes**.
3. Sélectionnez **Ajouter un utilisateur**.
4. Dans la section **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
5. Pour rechercher un utilisateur spécifique, tapez le nom d’utilisateur dans la zone **Sélectionner un membre ou inviter un utilisateur externe**. Ensuite, sélectionnez le logo ou la photo de profil de l’utilisateur, puis choisissez **Sélectionner**. 
6. Dans la section **Ajouter une attribution**, sélectionnez **Attribuer**. Une fois terminé, les utilisateurs sélectionnés s’affichent dans la liste **Utilisateurs et groupes**.

## <a name="set-up-the-application-to-use-azure-ad"></a>Configurer l’application pour utiliser Azure AD

Vous avez presque terminé.  Dans la dernière étape, vous devez configurer l’application pour utiliser Azure AD comme fournisseur d’identité SAML. 

1. Faites défiler jusqu'à la section **Configurer \<applicationName >** . Pour ce tutoriel, cette section est appelée **Configurer GitHub-test**. 
2. Copiez la valeur de chaque ligne dans cette section. Ensuite, collez chaque valeur dans la ligne appropriée dans la section **Configuration SAML de base**. Par exemple, copiez la valeur **URL de connexion** à partir de la section **Configurer GitHub-test** et collez-la dans le champ **URL de connexion** de la section **Configuration SAML de base** et ainsi de suite.
3. Lorsque vous avez collé toutes les valeurs dans les champs appropriés, sélectionnez **Enregistrer**.

## <a name="test-single-sign-on"></a>Tester l’authentification unique

Vous êtes prêt à tester vos paramètres.  

1. Ouvrez les paramètres d’authentification unique pour votre application. 
2. Faites défiler vers la section **Valider l’authentification unique avec \<applicationName>** . Pour ce tutoriel, cette section est appelée **Configurer GitHub-test**.
3. Sélectionnez **Test**. Les options de tests s’affichent.
4. Sélectionnez **Se connecter en tant qu’utilisateur actuel**. Ce test vous permet de commencer par voir si l’authentification unique fonctionne pour vous, l’administrateur.

Si une erreur se produit, un message d’erreur s’affiche. Effectuez ensuite les tâches suivantes :

1. Copiez et collez les détails dans la case **À quoi ressemble l’erreur ?** .

    ![Obtenir l’aide de résolution](media/configure-single-sign-on-portal/error-guidance.png)

2. Sélectionnez **Obtenir l’aide de résolution**. L’aide de résolution et de la cause racine apparaît.  Dans cet exemple, l’utilisateur n’était pas affecté à l’application.

3. Lisez l’aide de résolution et, si possible, corrigez le problème.

4. Réexécutez le test jusqu'à ce qu’il se termine correctement.

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

Pour déployer l’application pour d’autres utilisateurs de votre organisation, utilisez le provisionnement d’utilisateur automatique.

> [!div class="nextstepaction"]
> [Découvrez comment affecter des utilisateurs avec le provisionnement automatique](configure-automatic-user-provisioning-portal.md)


