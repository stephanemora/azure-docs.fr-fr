---
title: Authentification auprès d’Azure Maps | Microsoft Docs
description: Authentification pour l’utilisation des services Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118815"
---
# <a name="authentication-with-azure-maps"></a>Authentification avec Azure Maps

Azure Maps prend en charge deux méthodes pour authentifier les requêtes. L’authentification par clé partagée et l’authentification Azure Active Directory (Azure AD) sont les deux méthodes utilisées pour autoriser les requêtes envoyées à Azure Maps. L’objectif de cet article est d’expliquer ces deux méthodes d’authentification pour faciliter votre implémentation de l’authentification.

## <a name="shared-key-authentication"></a>Authentification par clé partagée

L’authentification par clé partagée s’appuie sur la transmission des clés générées pour les comptes Azure Maps lors de chaque requête envoyée à Azure Maps.  Lors de la création de votre compte Azure Maps, deux clés sont générées.  Chaque requête envoyée aux services Azure Maps nécessite que vous ajoutiez la clé d’abonnement à l’URL en tant que paramètre.

> [!Tip]
> Nous vous recommandons de regénérer régulièrement vos clés. Vous disposez de deux clés, ce qui signifie que vous pouvez maintenir les connexions à l’aide d’une clé pendant que vous regénérez l’autre clé. Lorsque vous regénérez vos clés, vous devez mettre à jour toutes les applications qui accèdent à ce compte pour qu’elles utilisent les nouvelles clés.

Pour afficher vos clés, consultez [Informations sur l’authentification](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Authentification à l’aide d’Azure Active Directory (préversion)

Azure Maps propose désormais une intégration [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) pour l’authentification des requêtes qui sont envoyées aux services Azure Maps.  Azure AD fournit une authentification basée sur l’identificateur, notamment le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), pour accorder un accès de niveau utilisateur ou application aux ressources d’Azure Maps. Cet article a pour but de vous aider à comprendre les concepts et les composants de l’intégration d’Azure Maps Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Authentification à l’aide de jetons d’accès OAuth

Azure Maps accepte les jetons d’accès **OAuth 2.0** pour les locataires Azure AD qui sont associés à un abonnement Azure comprenant un compte Azure Maps.  Azure Maps accepte les jetons pour :

* Les utilisateurs Azure AD 
* Les applications tierces qui utilisent des autorisations déléguées pour les utilisateurs
* Identités gérées pour les ressources Azure

Azure Maps génère un `unique identifier (client ID)` pour chaque compte Azure Maps.  Lorsque l’ID client est associé à d’autres paramètres, vous pouvez envoyer des requêtes dans Azure AD pour obtenir les jetons, en spécifiant la valeur ci-dessous :

```
https://login.microsoftonline.com
```
Si vous souhaitez obtenir plus d’informations sur la configuration d’Azure AD et sur les requêtes de jetons pour Azure Maps, consultez [Comment gérer l’authentification](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Pour obtenir des informations générales sur les requêtes de jetons dans Azure AD, consultez [Principes de base de l’authentification dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>Envoi de requêtes pour les ressources Azure Map à l’aide de jetons OAuth

Une fois que vous avez obtenu un jeton dans Azure AD, vous pouvez envoyer une requête à Azure Maps, dans laquelle vous aurez défini les deux en-têtes de requête suivants :

| En-tête de requête    |    Valeur    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Authorization     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` est le compte Azure Maps basé sur le GUID, qui est affiché dans la page d’authentification Azure Maps

Voici un exemple de requête de routage Azure Maps qui utilise un jeton OAuth :

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Pour afficher votre ID client, consultez [Informations sur l’authentification](https://aka.ms/amauthdetails).

## <a name="control-access-with-role-based-access-control-rbac"></a>Gérer l’accès avec le contrôle d’accès en fonction du rôle (RBAC)

L’une des principales fonctionnalités d’Azure AD est la gestion de l’accès aux ressources sécurisées par le biais du contrôle RBAC. Une fois que vous avez créé votre compte Azure Maps et que vous avez inscrit l’application Azure Maps Azure AD dans votre locataire Azure AD, vous êtes prêt à configurer le contrôle RBAC pour un utilisateur, une application ou une ressource Azure dans la page du portail de votre compte Azure Maps. 

Azure Maps prend en charge le contrôle d’accès en lecture pour les utilisateurs Azure AD, les applications et les services Azure via les identités managées pour ressources Azure.

![concept](./media/azure-maps-authentication/concept.png)

Pour afficher vos paramètres RBAC, consultez [Comment configurer le contrôle RBAC pour Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identités managées pour ressources Azure et Azure Maps

Les [identités managées pour ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fournissent aux services Azure (service Azure App, Azure Functions, Machines Virtuelles, etc.) une identité managée automatiquement, dont l’accès aux services Azure Maps peut être autorisé.  

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’authentification d’une application avec Azure AD et Azure Maps, consultez [Comment gérer l’authentification](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Pour plus d’informations sur l’authentification d’Azure Maps, de Map Control et d’Azure AD, consultez [Azure AD et Azure Maps Map Control](https://aka.ms/amaadmc).