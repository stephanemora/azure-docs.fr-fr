---
title: Gérer l’authentification dans Azure Maps | Microsoft Docs
description: Vous pouvez utiliser le portail Azure pour gérer l’authentification dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 057bd18c50d7074e8a88b8273bec766a306a3776
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484351"
---
# <a name="manage-authentication-in-azure-maps"></a>Gérer l’authentification dans Azure Maps

Suite à la création d’un compte Azure Maps, un ID client et des clés sont créés pour prendre en charge l’authentification Azure Active Directory (Azure AD) ou l’authentification par clé partagée.

## <a name="view-authentication-details"></a>Afficher les détails de l’authentification

Vous pouvez afficher les détails de votre authentification sur le portail Microsoft Azure. Accédez à votre compte, puis sélectionnez **Authentification** dans le menu **Paramètres**.

![Informations sur l’authentification](./media/how-to-manage-authentication/how-to-view-auth.png)

 Pour en savoir plus, voir [Authentification avec Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Configurer l’inscription d’application Azure AD

Dès lors qu’un compte Azure Map est créé, vous devez établir un lien entre votre locataire Azure AD et la ressource Azure Maps.

1. Accédez au panneau Azure AD et créez une inscription d’application. Donnez un nom à l’inscription. Dans la zone **URL de connexion**, indiquez la page d’accueil de l’application web ou de l’API (par exemple, https:\//localhost/). Si vous disposez déjà d’une application inscrite, passez à l’étape 2.

    ![Inscription d'application](./media/how-to-manage-authentication/app-registration.png)

    ![Détails de l’inscription d’application](./media/how-to-manage-authentication/app-create.png)

2. Pour attribuer des autorisations d’API déléguées à Azure Maps, accédez à l’application sous **Inscriptions d’applications**, puis cliquez sur **Paramètres**.  Sélectionnez **Autorisations requises**, puis **Ajouter**. Recherchez, puis sélectionnez **Azure Maps** sous **Sélectionner une API** et cliquez sur le bouton **Sélectionner**.

    ![Autorisations de l’API de l’application](./media/how-to-manage-authentication/app-permissions.png)

3. Sous **Sélectionner les autorisations**, choisissez **Access Azure Maps** (Accéder à Azure Maps), puis cliquez sur le bouton **Sélectionner**.

    ![Sélection des autorisations de l’API de l’application](./media/how-to-manage-authentication/select-app-permissions.png)

4. Effectuez l’étape a ou b, selon la méthode d’authentification choisie.

    1. Si votre application utilise l’authentification du jeton utilisateur avec le Kit de développement logiciel (SDK) Azure Maps, activez `oauthEnableImplicitFlow` en lui affectant la valeur true dans la section Manifeste de la page des détails de l’inscription d’application.
    
       ![Manifeste de l’application](./media/how-to-manage-authentication/app-manifest.png)

    2. Si votre application utilise l’authentification du serveur ou de l’application, accédez au panneau **Clés** de l’inscription d’application et, au choix, créez un mot de passe ou chargez un certificat de clé publique sur l’inscription d’application. Si vous créez un mot de passe, après avoir cliqué sur **Enregistrer**, copiez le mot de passe pour une utilisation ultérieure et stockez-le en toute sécurité. Vous utiliserez ce mot de passe pour acquérir des jetons à partir d’Azure AD.

       ![Clés de l’application](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Accorder le RBAC à Azure Maps

Après avoir associé un compte Azure Maps à votre locataire Azure AD, vous pouvez octroyer le contrôle d’accès en assignant à un utilisateur, un groupe ou une application un ou plusieurs rôles de contrôle d’accès Azure Maps.

1. Accédez à la zone **Contrôle d’accès (IAM)** , et sélectionnez **Affectations de rôles**, puis **Ajouter une attribution de rôle**.

    ![Octroyer le RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Dans la fenêtre **Ajouter une attribution de rôle**, sous **Rôle**, sélectionnez **Lecteur de données Azure Maps (préversion)** . Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service Azure AD**. Sous **Sélectionner**, choisissez l’utilisateur ou l’application. Sélectionnez **Enregistrer**.

    ![Ajouter une attribution de rôle](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Afficher les rôles RBAC disponibles pour Azure Maps

Pour afficher les rôles de contrôle d’accès en fonction du rôle (RBAC) qui sont disponibles pour Azure Maps, accédez à la zone **Contrôle d’accès (IAM)** , choisissez **Rôles**, puis recherchez les rôles commençant par **Azure Maps**. Voici les rôles auxquels vous pouvez accorder un accès.

![Affichage des rôles disponibles](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Afficher le contrôle d’accès en fonction du rôle (RBAC) pour Azure Maps

La fonction de contrôle d’accès en fonction du rôle (RBAC) propose un contrôle granulaire.

Pour afficher les applications et les utilisateurs auxquels le contrôle d’accès en fonction du rôle (RBAC) pour Azure Maps a été accordé, accédez à l’option **Contrôle d’accès (IAM)** , sélectionnez **Attributions de rôle** et filtrez par **Azure Maps**.

![Afficher les utilisateurs et applications bénéficiant du contrôle d’accès en fonction du rôle (RBAC)](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Demander des jetons pour Azure Maps

Une fois que votre application est inscrite et associée à Azure Maps, vous pouvez demander des jetons d’accès.

* Si votre application utilise l’authentification du jeton utilisateur avec le Kit de développement logiciel (SDK) Azure Maps, vous devez configurer votre page HTML avec l’ID client Azure Maps et l’ID de l’application Azure AD.

* Pour les applications utilisant l’authentification du serveur ou de l’application, vous devez demander un jeton à partir du point de terminaison de jeton Azure AD `https://login.microsoftonline.com` avec l’ID de ressource Azure AD `https://atlas.microsoft.com/`, l’ID client Azure Maps, l’ID de l’application Azure AD et le mot de passe ou le certificat d’inscription d’application Azure AD App.

| Environnement Azure   | Point de terminaison de jeton Azure AD | ID de la ressource Azure |
| --------------------|-------------------------|-------------------|
| Azure (public)        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Pour en savoir plus sur les demandes de jetons d’accès à partir d’Azure AD pour les utilisateurs et les principaux de service, voir [Scénarios d’authentification pour Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Étapes suivantes

Pour en apprendre davantage sur l’authentification Azure AD et le kit SDK web Azure Maps, consultez [kit SDK web Azure AD et Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Découvrez comment afficher les métriques d’utilisation des API pour votre compte Azure Maps :
> [!div class="nextstepaction"] 
> [Afficher les métriques d’utilisation](how-to-view-api-usage.md)

Pour obtenir une liste d’exemples montrant comment intégrer AAD (Azure Active Directory) à Azure Maps, consultez :

> [!div class="nextstepaction"]
> [Exemples d’authentification Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)