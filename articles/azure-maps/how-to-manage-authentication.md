---
title: Gérer l’authentification | Microsoft Azure Maps
description: Utilisez le portail Azure pour gérer l’authentification dans Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335529"
---
# <a name="manage-authentication-in-azure-maps"></a>Gérer l’authentification dans Azure Maps

Suite à la création d’un compte Azure Maps, un ID client et des clés sont créés pour prendre en charge l’authentification Azure Active Directory (Azure AD) et l’authentification par clé partagée.

## <a name="view-authentication-details"></a>Afficher les détails de l’authentification

Après la création du compte Azure Maps, les clés principales et secondaires sont générées. Nous vous recommandons d’utiliser la clé principale comme clé d’abonnement quand vous [utilisez l’authentification par clé partagée pour appeler Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Vous pouvez utiliser une clé secondaire dans des scénarios comme des changements dans la permutation des clés. Pour plus d’informations, consultez [Authentification dans Azure Maps](https://aka.ms/amauth).

Vous pouvez voir les détails de votre authentification dans le portail Azure. Dans votre compte, dans le menu **Paramètres**, sélectionnez **Authentification**.

![Informations sur l’authentification](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Inscrire et configurer une application Azure AD

1. Dans le portail Azure, dans la liste des services Azure, sélectionnez **Azure Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.  

    ![Inscription d'application](./media/how-to-manage-authentication/app-registration.png)

1. Si vous avez déjà inscrit votre application, passez à l’étape suivante. Si vous n’avez pas inscrit votre application, entrez un **Nom**, choisissez un **Type de compte de support**, puis sélectionnez **Inscrire**.  

    ![Détails de l’inscription d’application](./media/how-to-manage-authentication/app-create.png)

1. Pour affecter des autorisations d’API déléguées à Azure Maps, accédez à l’application. Ensuite, sous **Inscriptions d’applications**, sélectionnez **Autorisations d’API** > **Ajouter une autorisation**. Sous **API utilisées par que mon organisation**, recherchez et sélectionnez **Azure Maps**.

    ![Ajouter des autorisations d’API d’application](./media/how-to-manage-authentication/app-permissions.png)

1. Cochez la case en regard de **Accéder à Azure Maps**, puis sélectionnez **Ajouter des autorisations**.

    ![Sélection des autorisations de l’API de l’application](./media/how-to-manage-authentication/select-app-permissions.png)

1. Effectuez une des étapes suivantes, en fonction de votre méthode d’authentification. 

    * Si votre application utilise l’authentification par jeton d’utilisateur avec le SDK web Azure Maps, activez `oauth2AllowImplicitFlow`. Pour l’activer , dans la section **Manifeste** de l’inscription de votre application, définissez `oauth2AllowImplicitFlow` sur true. 
    
       ![Manifeste de l’application](./media/how-to-manage-authentication/app-manifest.png)

    * Si votre application utilise l’authentification du serveur ou de l’application, dans la page d’inscription de votre application, accédez à **Certificats et secrets**. Ensuite, chargez un certificat de clé publique ou créez un mot de passe en sélectionnant **Nouveau secret client**. 
    
       ![Créer une clé secrète client](./media/how-to-manage-authentication/app-keys.png)

        Si vous créez un mot de passe, après avoir sélectionné **Ajouter**, copiez le mot de passe et stockez-le à un endroit sécurisé. Vous utiliserez ce mot de passe pour obtenir des jetons auprès d’Azure AD.

       ![Ajouter un secret client](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Accorder un contrôle d’accès en fonction du rôle (RBAC) à Azure Maps

Après avoir associé un compte Azure Maps à votre locataire Azure AD, vous pouvez accorder un contrôle d’accès. Vous accordez un *contrôle d’accès en fonction du rôle* (RBAC) en affectant un utilisateur, un groupe ou une application à un ou plusieurs rôles de contrôle d’accès d’Azure Maps. 

1. Accédez à votre **compte Azure Maps**. Sélectionnez **Contrôle d’accès (IAM)**  > **Attributions de rôles**.

    ![Octroyer le RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Sous l’onglet **Attributions de rôles**, sous **Rôle**, sélectionnez **Lecteur de données Azure Maps (préversion)** . Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service Azure AD**. Sélectionnez l’utilisateur ou l’application. Ensuite, sélectionnez **Enregistrer**.

    ![Ajouter une attribution de rôle](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Afficher les rôles RBAC disponibles pour Azure Maps

Pour voir les rôles RBAC disponibles pour Azure Maps, accédez à **Contrôle d’accès (IAM)** . Sélectionnez **Rôles**, puis recherchez les rôles qui commencent par *Azure Maps*. Ces rôles Azure Maps sont les rôles auxquels vous pouvez accorder un accès.

![Affichage des rôles disponibles](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Afficher le contrôle d’accès en fonction du rôle (RBAC) pour Azure Maps

La fonction de contrôle d’accès en fonction du rôle (RBAC) propose un contrôle granulaire.

Pour voir les applications et les utilisateurs auxquels le contrôle d’accès en fonction du rôle (RBAC) pour Azure Maps a été accordé, accédez à l’option **Contrôle d’accès (IAM)** . Ensuite, sélectionnez **Attributions de rôles**, puis filtrez sur **Azure Maps**.

![Voir les utilisateurs et les applications auxquels RBAC a été accordé](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Demander des jetons pour Azure Maps

Une fois que votre application est inscrite et associée à Azure Maps, vous pouvez demander des jetons d’accès.

Si votre application utilise l’authentification du jeton utilisateur avec le SDK web Azure Maps, configurez votre page HTML avec l’ID de client Azure Maps et l’ID d’application Azure AD.

Si votre application utilise l’authentification du serveur ou de l’application, demandez un jeton auprès du point de terminaison de jeton Azure AD `https://login.microsoftonline.com`. Dans votre demande, utilisez les informations suivantes : 

* ID de ressource Azure AD `https://atlas.microsoft.com/`
* ID de client Azure Maps
* ID de l’application Azure AD
* Mot de passe ou certificat d’inscription de l’application Azure AD

| Environnement Azure   | Point de terminaison de jeton Azure AD | ID de ressource Azure |
| --------------------|-------------------------|-------------------|
| Cloud public Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud Azure Government   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Pour en savoir plus sur les demandes de jetons d’accès à partir d’Azure AD pour les utilisateurs et les principaux de service, voir [Scénarios d’authentification pour Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Azure AD et SDK web Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Recherchez les métriques d’utilisation de l’API pour votre compte Azure Maps :
> [!div class="nextstepaction"] 
> [Afficher les métriques d’utilisation](how-to-view-api-usage.md)

Explorez des exemples qui montrent comment intégrer Azure AD à Azure Maps :

> [!div class="nextstepaction"]
> [Exemples d’authentification Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
