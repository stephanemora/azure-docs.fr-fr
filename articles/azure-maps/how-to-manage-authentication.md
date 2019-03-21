---
title: Gérer l’authentification dans Azure Maps | Microsoft Docs
description: Vous pouvez utiliser le portail Azure pour gérer l’authentification dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 414ca7156fd59ec1dc08e45c73e9eb30835078d4
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259922"
---
# <a name="manage-authentication-in-azure-maps"></a>Gérer l’authentification dans Azure Maps

Après avoir créé un compte Azure Maps, un ID client et les clés sont créées pour prendre en charge d’Azure Active Directory (Azure AD) ou authentification par clé partagée.

## <a name="view-authentication-details"></a>Afficher les détails de l’authentification

Vous pouvez afficher les détails de votre authentification sur le portail Azure. Accédez à votre compte, puis sélectionnez **authentification** sur le **paramètres** menu.

![Informations sur l’authentification](./media/how-to-manage-authentication/how-to-view-auth.png)

 Pour plus d’informations, consultez [l’authentification avec Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Configuration de l’inscription d’application Azure AD

Après avoir créé un compte Azure Maps, vous devez établir un lien entre votre client Azure AD et la ressource Azure Maps.

1. Accédez au panneau Azure AD et créer une inscription d’application. Fournissez un nom pour l’inscription. Dans le **Sign-on URL** zone, fournissez la page d’accueil de l’application web / API (par exemple, https :\//localhost/). Si vous disposez déjà d’une application inscrite, passez à l’étape 2.

    ![Inscription d'application](./media/how-to-manage-authentication/app-registration.png)

    ![Détails de l’inscription d’application](./media/how-to-manage-authentication/app-create.png)

2. Pour affecter des autorisations déléguées d’API à Azure Maps, accédez à l’application sous **inscriptions**, puis sélectionnez **paramètres**.  Sélectionnez **autorisations requises**, puis sélectionnez **ajouter**. Recherchez et sélectionnez **Azure Maps** sous **sélectionner une API**, puis sélectionnez le **sélectionnez** bouton.

    ![Autorisations d’application API](./media/how-to-manage-authentication/app-permissions.png)

3. Sous **sélectionner les autorisations**, sélectionnez **accès Azure Maps**, puis sélectionnez le **sélectionnez** bouton.

    ![Sélectionnez les autorisations d’application API](./media/how-to-manage-authentication/select-app-permissions.png)

4. L’étape un ou b, en fonction de votre méthode d’authentification.

    1. Si votre application utilise l’authentification de jeton d’utilisateur avec le Kit de développement logiciel Azure Maps Web, activez `oauthEnableImplicitFlow` en lui affectant la valeur true dans la section du manifeste de la page de détails d’inscription de votre application.
    
       ![Manifeste de l’application](./media/how-to-manage-authentication/app-manifest.png)

    2. Si votre application utilise l’authentification de serveur ou l’application, accédez à la **clés** panneau dans l’inscription de l’application et créer un mot de passe ou télécharger un certificat de clé publique pour l’inscription d’application. Si vous créez un mot de passe, une fois que vous sélectionnez **enregistrer**, copiez le mot de passe pour une utilisation ultérieure et le stocker en toute sécurité. Vous utiliserez ce mot de passe pour acquérir des jetons d’Azure AD.

       ![Clés de l’application](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Accorder le RBAC à Azure Maps

Une fois que vous associez un compte Azure Maps avec votre client Azure AD, vous pouvez accorder le contrôle d’accès en affectant un utilisateur ou une application à un ou plusieurs rôles de contrôle d’accès Azure Maps.

1. Accédez à **contrôle d’accès (IAM)**, sélectionnez **attributions de rôles**, puis sélectionnez **ajouter une attribution de rôle**.

    ![Octroyer le RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Dans le **ajouter une attribution de rôle** fenêtre, sous **rôle**, sélectionnez **Azure Maps Date Reader (version préliminaire)**. Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service Azure AD**. Sous **sélectionnez**, sélectionnez l’utilisateur ou l’application. Sélectionnez **Enregistrer**.

    ![Ajouter une attribution de rôle](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Afficher les rôles RBAC disponibles pour Azure Maps

Pour afficher les rôles de contrôle (RBAC) d’accès en fonction du rôle qui sont disponibles pour Azure Maps, accédez à **contrôle d’accès (IAM)**, sélectionnez **rôles**, et puis rechercher les rôles commençant par **Azure Maps**. Voici les rôles que vous pouvez accorder l’accès à.

![Affichage des rôles disponibles](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Afficher les mappages Azure RBAC

RBAC fournit un contrôle d’accès granulaire.

Pour afficher les utilisateurs et les applications qui ont été accordées RBAC pour Azure Maps, accédez à **contrôle d’accès (IAM)**, sélectionnez **attributions de rôles**, puis filtrez par **Azure Maps**.

![Afficher les utilisateurs et applications accordées RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Demander des jetons pour Azure Maps

Une fois que vous inscrivez votre application et associé à Azure Maps, vous pouvez demander des jetons d’accès.

* Si votre application utilise l’authentification de jeton d’utilisateur avec le Kit de développement logiciel Azure Maps Web, vous devez configurer votre page HTML avec l’ID de client Azure Maps et l’ID d’application Azure AD.

* Si votre application utilise l’authentification de serveur ou l’application, vous devez demander un jeton à partir du point de terminaison de connexion Azure AD `https://login.microsoftonline.com` avec l’ID de ressource Azure AD `https://atlas.microsoft.com/`, l’ID de client Azure Maps, l’ID d’application Azure AD et l’inscription d’application Azure AD mot de passe ou certificat.

Pour plus d’informations sur la demande de jetons d’accès à partir d’Azure AD pour les utilisateurs et principaux du service, consultez [scénarios d’authentification pour Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Étapes suivantes

* Pour en apprendre davantage sur l’authentification Azure AD et le kit SDK web Azure Maps, consultez [kit SDK web Azure AD et Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).