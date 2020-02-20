---
title: Méthodes d’authentification | Microsoft Azure Maps
description: Dans cet article, vous allez découvrir l’authentification Azure Active Directory (Azure AD) et l’authentification par clé partagée. Les deux sont utilisées pour les services Microsoft Azure Maps. Découvrez comment obtenir une clé d’abonnement Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ee8b166077c64ae5e0f2cce18ee0bc77e8c996f4
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210121"
---
# <a name="authentication-with-azure-maps"></a>Authentification avec Azure Maps

Azure Maps prend en charge deux méthodes pour authentifier les requêtes : Authentification par clé partagée et authentification Azure Active Directory. Cet article explique ces méthodes d’authentification pour vous guider dans votre implémentation des services Azure Maps.

## <a name="shared-key-authentication"></a>Authentification par clé partagée

 Les clés primaires et secondaires sont générées après la création du compte Azure Maps. Nous vous encourageons à utiliser la clé primaire comme clé d’abonnement lors de l’appel d’Azure Maps à l’aide de l’authentification par clé partagée. L’authentification par clé partagée transmet une clé générée par un compte Azure Maps à un service Azure Maps. Pour chaque requête envoyée aux services Azure Maps, ajoutez la *clé d’abonnement* à l’URL en tant que paramètre. La clé secondaire peut être utilisée dans des scénarios tels que le roulement des changements de clés.  

Pour plus d’informations sur l’affichage de vos clés dans le portail Azure, consultez [Gérer l’authentification](https://aka.ms/amauthdetails).

> [!Tip]
> Nous vous recommandons de regénérer régulièrement vos clés. Vous disposez de deux clés, ce qui signifie que vous pouvez maintenir les connexions à l’aide d’une clé pendant que vous regénérez l’autre clé. Lorsque vous régénérez vos clés, vous devez mettre à jour toutes les applications qui accèdent à votre compte pour qu’elles utilisent les nouvelles clés.

## <a name="authentication-with-azure-active-directory-preview"></a>Authentification à l’aide d’Azure Active Directory (préversion)

Azure Maps propose désormais une authentification pour les services Azure Maps à l’aide d’[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Azure AD propose une authentification basée sur l’identité, notamment le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). RBAC est utilisé pour accorder l’accès aux ressources Azure Maps au niveau de l’utilisateur, au niveau du groupe ou au niveau de l’application. Les sections ci-après traitent des concepts et des composants de l’intégration d’Azure Maps à Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Authentification à l’aide de jetons d’accès OAuth

Azure Maps accepte les jetons d’accès **OAuth 2.0** pour les locataires Azure AD associés à un abonnement Azure comprenant un compte Azure Maps. Azure Maps accepte également les jetons pour :

* Utilisateurs Azure AD
* Applications partenaires qui utilisent des autorisations déléguées par des utilisateurs
* Identités gérées pour les ressources Azure

Azure Maps génère un *identificateur unique (ID client)* pour chaque compte Azure Maps. Vous pouvez demander des jetons à Azure AD lorsque vous combinez cet ID client avec des paramètres supplémentaires. Pour demander un jeton, spécifiez les valeurs dans le tableau suivant en fonction de votre environnement Azure.

| Environnement Azure   | Point de terminaison de jeton Azure AD |
| --------------------|-------------------------|
| Azure (public)        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Si vous souhaitez obtenir plus d’informations sur la configuration d’Azure AD et sur les requêtes de jetons pour Azure Maps, consultez [Gérer l’authentification dans Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Pour obtenir des informations générales sur la requête de jetons Azure AD, consultez [Qu’est l’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Requête de ressources Azure Map à l’aide de jetons OAuth

Une fois qu’Azure AD a reçu un jeton, Azure Maps envoie une requête avec l’ensemble suivant d’en-têtes de requête nécessaires :

| En-tête de requête    |    Valeur    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorisation     | Bearer eyJ0e….HNIVN |

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

Dans Azure AD, utilisez RBAC pour contrôler l’accès aux ressources sécurisées. Configurez votre compte Azure Maps et inscrivez votre locataire Azure AD Azure Maps. Azure Maps prend en charge le contrôle d’accès en lecture pour les utilisateurs Azure AD, les groupes, les applications, les ressources Azure et les services Azure par le biais d’identités managées pour les ressources Azure. Dans la page du portail Azure Maps, vous pouvez configurer RBAC pour les rôles choisis.

![Lecteur de données Azure Maps (préversion)](./media/azure-maps-authentication/concept.png)

Pour plus d’informations sur la consultation de vos paramètres RBAC, consultez [Comment configurer le contrôle RBAC pour Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identités managées pour les ressources Azure et Azure Maps

Les [identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fournissent aux services Azure une identité managée automatiquement, qui peut être autorisée à accéder aux services Azure Maps. Voici quelques exemples d’identités managées : Azure App Service, Azure Functions et Machines virtuelles Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’authentification d’une application avec Azure AD et Azure Maps, consultez [Gérer l’authentification dans Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Pour plus d’informations sur l’authentification d’Azure Maps, de Map Control et d’Azure AD, consultez [Utiliser Azure AD et Azure Maps Map Control](https://aka.ms/amaadmc).
