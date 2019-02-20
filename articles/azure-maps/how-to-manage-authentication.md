---
title: Gérer l’authentification dans Azure Maps | Microsoft Docs
description: Vous pouvez utiliser le portail Azure pour gérer l’authentification dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 68c3c8ac39f5803e01ee1038ec85ddb96ac80b30
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242683"
---
# <a name="manage-authentication-in-azure-maps"></a>Gérer l’authentification dans Azure Maps

Suite à la création d’un compte Azure Maps, l’ID client et les clés sont créés pour prendre en charge l’authentification Azure Active Directory (Azure AD) ou l’authentification par clé partagée.

Vous trouvez les détails de votre authentification en consultant la page **Authentification** sous **Paramètres** dans le portail Azure. Naviguez jusqu’à votre compte. Ensuite, dans le menu, sélectionnez **Authentification**.


## <a name="view-authentication-details"></a>Afficher les détails de l’authentification

Pour afficher les détails de votre authentification, accédez à l’option **Authentification** dans le menu Paramètres.

![Afficher l’authentification](./media/how-to-manage-authentication/how-to-view-auth.png)

 Pour en savoir plus sur l’authentification et Azure Maps, consultez [Authentification avec Azure Maps](https://aka.ms/amauth).


## <a name="configure-azure-ad-app-registration"></a>Configurer l’inscription Azure AD App

Dès lors qu’un compte Azure Map est créé, un lien doit être établi entre votre locataire Azure AD et la ressource Azure Maps. 

1. Accédez au panneau Azure AD et créez une inscription d’application, avec un nom et l’URL de connexion, comme page d’accueil de l’application web ou de l’API web, par exemple « https://localhost/ ». Si vous disposez déjà d’une application inscrite, passez à l’étape 2.

    ![Inscription d'application](./media/how-to-manage-authentication/app-registration.png)

    ![Inscription d'application](./media/how-to-manage-authentication/app-create.png)

2. Attribuez des permissions déléguées d’API à Azure Maps en accédant à l’application sous Inscriptions d’application, puis cliquez sur **Paramètres**.  Sélectionnez **Autorisations requises**, puis **Ajouter**. Recherchez, puis sélectionnez Azure Maps sous **Sélectionner une API** et cliquez sur **Sélectionner**.

    ![Autorisations de l’API de l’application](./media/how-to-manage-authentication/app-permissions.png)

3. Enfin, sous Sélectionner des autorisations, choisissez Accès Azure Maps et cliquez sur Sélectionner.

    ![Sélection des autorisations de l’API de l’application](./media/how-to-manage-authentication/select-app-permissions.png)

4. Suivez l’étape a ou b, selon l’implémentation de l’authentification.

    1. S’il est prévu que l’application utilise l’authentification du jeton utilisateur avec notre kit SDK web Azure Maps, vous devez activer `oauthEnableImplicitFlow` en lui affectant la valeur true dans la section Manifeste de la page des détails de l’inscription de votre application.
    
       ![Manifeste de l’application](./media/how-to-manage-authentication/app-manifest.png)

    2. Si votre application utilise l’authentification du serveur ou de l’application, accédez au panneau **Clés** dans l’inscription de l’application et, au choix, créez un mot de passe ou chargez un certificat de clé publique sur l’inscription de l’application. Si vous créez un mot de passe, après avoir cliqué sur **Enregistrer**, notez ce mot de passe en vue d’une utilisation ultérieure et conservez-le dans un endroit sûr. Il vous sera utile pour acquérir des jetons à partir d’Azure AD.

       ![Clés de l’application](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Accorder le RBAC à Azure Maps

Dès lors qu’un compte Azure Maps a été associé à votre locataire Azure AD, le contrôle d’accès peut être octroyé en assignant à l’utilisateur ou à l’application un ou plusieurs rôles de contrôle d’accès Azure Maps disponibles.

1. Accédez à l’option **Contrôle d’accès**, cliquez sur **Attributions de rôles** et **Ajouter une attribution de rôle**.

    ![Octroyer le RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Dans la fenêtre contextuelle de l’attribution de rôle, sélectionnez le **Rôle** Lecteur de données Azure Maps (préversion), puis dans **Attribuer l’accès à** : Utilisateur, groupe ou principal du service Azure AD, puis dans **Sélectionner** : Utilisateur ou Application à partir de la liste déroulante, et enfin **Enregistrer**.

    ![Ajouter une attribution de rôle](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Afficher les rôles RBAC disponibles pour Azure Maps

Pour voir les rôles de contrôle d’accès en fonction du rôle qui sont disponibles pour Azure Maps, et dont l’accès peut être accordé, naviguez jusqu’à l’option **Contrôle d’accès (IAM)**, cliquez sur **Rôles** et recherchez les rôles commençant par **Azure Maps**.

![Affichage des rôles disponibles](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Afficher le contrôle d’accès en fonction du rôle (RBAC) pour Azure Maps

Azure AD offre un contrôle d’accès granulaire via le contrôle d’accès en fonction du rôle (RBAC). 

Pour afficher les applications ou les utilisateurs auxquels le RBAC pour Azure Maps a été accordé, accédez à l’option **Contrôle d’accès (IAM)**, sélectionnez **Attributions de rôle** et filtrez par **Azure Maps**. Tous les rôles disponibles s’affichent en dessous.

![Affichage du RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Demander des jetons pour Azure Maps

Une fois que votre application est enregistrée et associée à Azure Maps, vous pouvez demander des jetons d’accès.

* S’il est prévu que l’application utilise l’authentification du jeton utilisateur avec notre kit SDK web Azure Maps (Web), vous devez configurer votre page html avec l’ID client Azure Maps et l’ID Azure AD App.

* Pour les applications utilisant l’authentification du serveur ou de l’application, demandez un jeton à partir du point de terminaison de connexion Azure AD `https://login.microsoftonline.com` avec l’ID de la ressource Azure AD `https://atlas.microsoft.com/`, l’ID client Azure Maps, l’ID de l’application Azure AD et le mot de passe ou le certificat d’inscription Azure AD App.

Pour plus d’informations sur la demande de jetons d’accès à partir d’Azure AD pour les utilisateurs et les principaux de service, consultez [Scénarios d’authentification pour Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Étapes suivantes

* Pour en apprendre davantage sur l’authentification Azure AD et le kit SDK web Azure Maps, consultez [kit SDK web Azure AD et Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).