---
title: Gérer l’authentification | Microsoft Azure Maps
description: Vous pouvez utiliser le portail Azure pour gérer l’authentification dans Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1f7f128898089292a8ccd92686af5d68fe328f3c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766076"
---
# <a name="manage-authentication-in-azure-maps"></a>Gérer l’authentification dans Azure Maps

Suite à la création d’un compte Azure Maps, un ID client et des clés sont créés pour prendre en charge l’authentification Azure Active Directory (Azure AD) et l’authentification par clé partagée.

## <a name="view-authentication-details"></a>Afficher les détails de l’authentification

Après la création du compte Azure Maps, les clés primaires et secondaires sont générées. Utilisez la clé primaire comme clé d’abonnement. Parfois, ces noms sont utilisés de manière interchangeable. La clé secondaire est utilisable dans des scénarios tels que des changements de clé constants. Dans les deux cas, une clé est nécessaire pour appeler Azure Maps. Ce processus est appelé [authentification par clé partagée](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Pour en savoir plus sur l’authentification par clé partagée et l’authentification Azure AD, voir [Authentification avec Azure Maps](https://aka.ms/amauth).

Vous pouvez afficher les détails de votre authentification sur le portail Microsoft Azure. Accédez à votre compte, puis sélectionnez **Authentification** dans le menu **Paramètres**.

![Informations sur l’authentification](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>Configurer l’inscription d’application Azure AD

Dès lors qu’un compte Azure Map est créé, vous devez établir un lien entre votre locataire Azure AD et la ressource Azure Maps.

1. Dans le menu du portail, sélectionnez **Azure Active Directory**. Donnez un nom à l’inscription. Cliquez sur **Inscriptions d’applications**, puis sur **Nouvelle inscription**. Dans la zone **URI de redirection**, indiquez la page d’accueil de l’application web. Par exemple : https://localhost/. Si vous disposez déjà d’une application inscrite, passez à l’étape 2.

    ![Inscription d'application](./media/how-to-manage-authentication/app-registration.png)

    ![Détails de l’inscription d’application](./media/how-to-manage-authentication/app-create.png)

2. Pour attribuer des autorisations d’API déléguées à Azure Maps, accédez à l’application sous **Inscriptions d’applications**, puis sélectionnez **Paramètres**. Sélectionnez **Ajouter une autorisation**. Recherchez et sélectionnez **Azure Maps** sous **Sélectionner une API**.

    ![Autorisations de l’API de l’application](./media/how-to-manage-authentication/app-permissions.png)

3. Sous **Sélectionnez des autorisations**, activez la case à cocher **Emprunter l’identité de l’utilisateur**, puis cliquez sur le bouton **Sélectionner** en bas.

    ![Sélection des autorisations de l’API de l’application](./media/how-to-manage-authentication/select-app-permissions.png)

4. Effectuez l’étape a ou b, selon la méthode d’authentification choisie.

    1. Si votre application utilise l’authentification du jeton utilisateur avec le Kit de développement logiciel (SDK) Azure Maps, activez `oauth2AllowImplicitFlow` en lui affectant la valeur true dans la section Manifeste de votre inscription d’application.
    
       ![Manifeste de l’application](./media/how-to-manage-authentication/app-manifest.png)

    2. Si votre application utilise l’authentification du serveur ou de l’application, accédez au panneau **Certificats et secrets** de l’inscription de l’application, puis créez un mot de passe ou chargez un certificat de clé publique dans l’inscription d’application. Si vous créez un mot de passe, stockez-le en toute sécurité pour une utilisation ultérieure. Vous utiliserez ce mot de passe pour acquérir des jetons à partir d’Azure AD.

       ![Clés de l’application](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Accorder un contrôle d’accès en fonction du rôle (RBAC) à Azure Maps

Après avoir associé un compte Azure Maps à votre locataire Azure AD, vous pouvez accorder un contrôle d’accès. Vous accordez un contrôle d’accès en assignant un utilisateur, un groupe ou une application à un ou plusieurs rôles de contrôle d’accès Azure Maps.

1. Accédez à votre **compte Azure Maps**. Sélectionnez **Contrôle d’accès (IAM)** , puis **Attribution de rôle**.

    ![Octroyer le RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Dans la fenêtre **Attribution de rôle**, sous **Rôle**, sélectionnez **Lecteur de données Azure Maps (préversion)** . Sous **Autorisation de l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service Azure AD**. Sélectionnez l’utilisateur ou l’application. Sélectionnez **Enregistrer**.

    ![Ajouter une attribution de rôle](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Afficher les rôles RBAC disponibles pour Azure Maps

Pour afficher les rôles de contrôle d’accès en fonction du rôle (RBAC) qui sont disponibles pour Azure Maps, accédez à la zone **Contrôle d’accès (IAM)** , choisissez **Rôles**, puis recherchez les rôles commençant par **Azure Maps**. Ces rôles sont ceux auxquels vous pouvez accorder l’accès.

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

Pour en savoir plus sur les demandes de jetons d’accès à partir d’Azure AD pour des utilisateurs et principaux de service, voir [Scénarios d’authentification pour Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Étapes suivantes

Pour en apprendre davantage sur l’authentification Azure AD et le kit SDK web Azure Maps, consultez [kit SDK web Azure AD et Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Découvrez comment afficher les métriques d’utilisation des API pour votre compte Azure Maps :
> [!div class="nextstepaction"] 
> [Afficher les métriques d’utilisation](how-to-view-api-usage.md)

Pour obtenir une liste d’exemples montrant comment intégrer AAD (Azure Active Directory) à Azure Maps, consultez :

> [!div class="nextstepaction"]
> [Exemples d’authentification Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
