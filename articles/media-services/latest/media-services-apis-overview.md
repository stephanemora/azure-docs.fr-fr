---
title: Développement avec des API v3 - Azure | Microsoft Docs
description: Cet article décrit les règles qui s’appliquent aux entités et API lors du développement avec Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4c5b30ab075bbca22b6a58ccf65e55d332820937
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406544"
---
# <a name="developing-with-media-services-v3-apis"></a>Développement avec Media Services v3 API

Cet article décrit les règles qui s’appliquent aux entités et API lors du développement avec Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>L’accès à l’API Azure Media Services

Pour être autorisé à accéder aux ressources Media Services et à l’API Media Services, vous devez tout d’abord être authentifié. Media Services prend en charge [Azure Active Directory (Azure AD)-basé](../../active-directory/fundamentals/active-directory-whatis.md) l’authentification. Deux options d’authentification courantes sont :
 
* **Authentification de principal du service** : utilisé pour authentifier un service (par exemple : applications web, applications de fonction, applications logiques, API et microservices). Les applications qui utilisent généralement cette méthode d’authentification sont des applications qui exécutent des services démon, des services de niveau intermédiaire ou des travaux planifiés, Par exemple, pour le Web applications il doivent toujours être un niveau intermédiaire qui se connecte à Media Services avec un Principal de Service.
* **Authentification utilisateur** : utilisé pour authentifier une personne qui est à l’aide de l’application pour interagir avec les ressources Media Services. L’application interactive invite tout d’abord l’utilisateur à entrer ses informations d’identification. Par exemple, une application de console de gestion peut être utilisée par les utilisateurs autorisés pour contrôler les travaux d’encodage ou de streaming en direct.

L’API Media Services nécessite que l’utilisateur ou une application qui effectue l’API REST demande ont accès à la ressource du compte Media Services et d’utiliser un **contributeur** ou **propriétaire** rôle. L’API est accessible avec la **lecteur** , mais uniquement le rôle **obtenir** ou **liste**   opérations seront disponibles. Pour plus d’informations, consultez [le contrôle d’accès en fonction du rôle pour les comptes Media Services](rbac-overview.md).

Au lieu de créer un principal de service, envisagez d’utiliser des identités gérées pour les ressources Azure à accéder à l’API Media Services via Azure Resource Manager. Pour en savoir plus sur les identités pour les ressources Azure, consultez [What ' s des identités gérées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Principal de service Azure AD 

Si vous créez une application Azure AD et un service principal, l’application doit se trouver dans son propre client. Après avoir créé l’application, donnez à l’application **contributeur** ou **propriétaire** rôle d’accéder au compte Media Services. 

Si vous ne savez pas si vous disposez d’autorisations pour créer une application Azure AD, consultez [autorisations requises](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Dans la figure suivante, les nombres représentent le flux de requêtes dans l’ordre chronologique :

![Applications de niveau intermédiaire](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Une application de niveau intermédiaire demande un jeton d’accès Azure AD qui possède les paramètres suivants :  

   * Point de terminaison de locataire Azure AD.
   * URI de ressource Media Services.
   * URI de ressource pour REST Media Services.
   * Valeurs de l’application Azure AD : ID client et clé secrète client.
   
   Pour obtenir toutes les valeurs nécessaires, consultez [API Access Azure Media Services avec Azure CLI](access-api-cli-how-to.md)

2. Le jeton d’accès Azure AD est envoyé au niveau intermédiaire.
4. Le niveau intermédiaire envoie une requête à l’API REST Azure Media avec le jeton Azure AD.
5. Le niveau intermédiaire récupère les données de Media Services.

### <a name="samples"></a>Exemples

Consultez les exemples qui montrent comment se connecter avec un principal de service Azure AD :

* [Se connecter avec REST](media-rest-apis-with-postman.md)  
* [Se connecter avec Java](configure-connect-java-howto.md)
* [Se connecter avec .NET](configure-connect-dotnet-howto.md)
* [Se connecter avec Node.js](configure-connect-nodejs-howto.md)
* [Se connecter avec Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Les noms de ressources Azure Media Services v3 (par exemple Assets, Jobs, Transforms) sont sujets à des restrictions d’appellation par Azure Resource Manager. Conformément à Azure Resource Manager, les noms des ressources sont toujours uniques. Ainsi, vous pouvez utiliser n’importe quelle chaîne d’identificateur unique (par exemple des GUID) pour les noms de vos ressources. 

Les noms de ressources Media Services ne peuvent pas contenir : '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', le caractère de citation unique ou tout caractère de commande. Tous les autres caractères sont autorisés. La longueur maximale d’un nom de ressources est de 260 caractères. 

Pour plus d'informations sur l'affectation de noms dans Azure Resource Manager, consultez : [Exigences en matière d'affectation de noms](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) et [Conventions d'affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Opérations à long terme

Les opérations marquées avec `x-ms-long-running-operation` dans les Services de média Azure [swagger fichiers](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) sont longues opérations en cours d’exécution. 

Pour plus d’informations sur le suivi des opérations asynchrones Azure, consultez [les opérations asynchrones](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services a les opérations de longue suivantes :

* Créer l’événement en direct
* Événement en direct de la mise à jour
* Supprimer l’événement en direct
* Démarrer l’événement en direct
* Arrêter l’événement en direct
* Réinitialisation d’événement en direct
* Créer LiveOutput
* Supprimer LiveOutput
* Créer StreamingEndpoint
* Mise à jour StreamingEndpoint
* Supprimer StreamingEndpoint
* Démarrer StreamingEndpoint
* Arrêter StreamingEndpoint
* Mise à l’échelle StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrage, classement et pagination d’entités Media Services

Consultez [filtrage, classement, la pagination des entités Azure Media Services](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

[Commencer à développer avec l’API Media Services v3 à l’aide de kits de développement logiciel/tools](developers-guide.md)
