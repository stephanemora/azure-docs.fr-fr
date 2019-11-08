---
title: Authentification auprès d’Azure Maps | Microsoft Docs
description: Authentification pour l’utilisation des services Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 84af496a92bd3c7b30062e965335782f7661aa4a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575654"
---
# <a name="authentication-with-azure-maps"></a>Authentification avec Azure Maps

Azure Maps prend en charge deux méthodes pour authentifier les requêtes : la clé partagée et Azure Active Directory (Azure AD). Cet article explique ces méthodes d’authentification pour vous guider dans votre implémentation.

## <a name="shared-key-authentication"></a>Authentification par clé partagée

L’authentification par clé partagée transmet les clés générées par le compte Azure Maps avec de chaque requête envoyée à Azure Maps.  Lors de la création de votre compte Azure Maps, deux clés sont générées. Chaque requête envoyée aux services Azure Maps, la clé d’abonnement doit être ajoutée à l’URL en tant que paramètre.

> [!Tip]
> Nous vous recommandons de regénérer régulièrement vos clés. Vous disposez de deux clés, ce qui signifie que vous pouvez maintenir les connexions à l’aide d’une clé pendant que vous regénérez l’autre clé. Lorsque vous regénérez vos clés, vous devez mettre à jour toutes les applications qui accèdent à ce compte pour qu’elles utilisent les nouvelles clés.

Pour plus d’informations sur l’affichage de vos clés, consultez [Voir les détails de l’authentification](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Authentification à l’aide d’Azure Active Directory (préversion)

Azure Maps propose désormais une intégration [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) pour l’authentification des requêtes qui sont envoyées aux services Azure Maps. Azure AD fournit une authentification basée sur l’identificateur, notamment le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), pour accorder un accès de niveau utilisateur, application ou groupe aux ressources Azure Maps. Les sections qui suivent ont pour but de vous aider à comprendre les concepts et les composants de l’intégration d’Azure Maps et Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Authentification à l’aide de jetons d’accès OAuth

Azure Maps accepte les jetons d’accès **OAuth 2.0** pour les locataires Azure AD associés à un abonnement Azure comprenant un compte Azure Maps. Azure Maps accepte les jetons pour :

* Les utilisateurs Azure AD. 
* Les applications partenaires qui utilisent des autorisations déléguées pour les utilisateurs.
* Identités managées pour les ressources Azure.

Azure Maps génère un *identificateur unique (ID client)* pour chaque compte Azure Maps. Lorsque vous combinez cet ID de client avec d’autres paramètres, vous pouvez demander des jetons Azure AD en spécifiant les valeurs dans le tableau suivant en fonction de votre environnement Azure.

| Environnement Azure   | Point de terminaison de jeton Azure AD |
| --------------------|-------------------------|
| Azure (public)        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Si vous souhaitez obtenir plus d’informations sur la configuration d’Azure AD et sur les requêtes de jetons pour Azure Maps, consultez [Gérer l’authentification dans Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Pour obtenir des informations générales sur la requête de jetons Azure AD, consultez [Qu’est l’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Requête de ressources Azure Map à l’aide de jetons OAuth

Après avoir obtenu un jeton Azure AD, vous pouvez envoyer une requête à Azure Maps, dans laquelle vous aurez défini les deux en-têtes de requête suivants :

| En-tête de requête    |    Valeur    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Authorization     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` est le compte Azure Maps basé sur le GUID, qui apparaît dans la page d’authentification Azure Maps.

Voici un exemple de requête de routage Azure Maps qui utilise un jeton OAuth :

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Pour plus d’informations sur l’affichage de votre ID client, consultez [Voir les détails de l’authentification](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Contrôler l’accès avec RBAC

Azure AD vous permet de contrôler l’accès aux ressources sécurisées à l’aide d’autorisations basées sur les rôles. Après avoir créé votre compte Azure Maps et inscrit l’application Azure Maps Azure AD dans votre locataire Azure AD, vous pouvez configurer le contrôle d’accès en fonction du rôle (RBAC) pour un utilisateur, une application, un groupe ou une ressource Azure dans la page du portail de votre compte Azure Maps.

Azure Maps prend en charge le contrôle d’accès en lecture pour les utilisateurs Azure AD, les applications, les services et les groupes Azure via les identités managées pour les ressources Azure.

![Lecteur de données Azure Maps (préversion)](./media/azure-maps-authentication/concept.png)

Pour plus d’informations sur la consultation de vos paramètres RBAC, consultez [Comment configurer le contrôle RBAC pour Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identités managées pour les ressources Azure et Azure Maps

Les [identités managées pour ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fournissent aux services Azure (Azure App Service, Azure Functions, Machines Virtuelles, etc.) une identité managée automatiquement, dont l’accès aux services Azure Maps peut être autorisé.  

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’authentification d’une application avec Azure AD et Azure Maps, consultez [Gérer l’authentification dans Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Pour plus d’informations sur l’authentification d’Azure Maps, de Map Control et d’Azure AD, consultez [Utiliser Azure AD et Azure Maps Map Control](https://aka.ms/amaadmc).
