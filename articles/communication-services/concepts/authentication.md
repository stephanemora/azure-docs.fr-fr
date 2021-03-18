---
title: S’authentifier auprès d’Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les différentes façons dont une application ou un service peut s’authentifier auprès de Communication Services.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b702afe9c4359d9f8711846d93fd79df9fc2f42e
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485959"
---
# <a name="authenticate-to-azure-communication-services"></a>S’authentifier auprès d’Azure Communication Services

Chaque interaction client avec Azure Communication Services doit être authentifiée. Dans une architecture classique, l’[architecture client et serveur](./client-and-server-architecture.md), les *clés d’accès* ou l’*identité managée* sont utilisées dans le service d’accès des utilisateurs approuvés pour créer des utilisateurs et émettre des jetons. Le *jeton d’accès utilisateur* émis par le service d’accès des utilisateurs approuvés est utilisé pour permettre aux applications clientes d’accéder à d’autres services de communication, comme le service de conversation ou d’appel.

Le service Azure Communication Services SMS accepte également les *clés d’accès* et l’*identité managée* pour l’authentification. Cela se produit généralement dans une application de service s’exécutant dans un environnement de service approuvé.

Chaque option d’autorisation est décrite brièvement ci-dessous :

- Authentification par **clé d’accès** pour les opérations SMS et Identity. L’authentification par clé d’accès est adaptée aux applications de service qui s’exécutent dans un environnement de service approuvé. La clé d’accès est accessible sur le portail Azure Communication Services. Pour s’authentifier avec une clé d’accès, une application de service utilise cette clé comme informations d’identification afin d’initialiser les bibliothèques de client SMS ou Identity correspondantes. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../quickstarts/access-tokens.md). Étant donné que la clé d’accès fait partie de la chaîne de connexion de votre ressource, consultez [Créer et gérer des ressources Communication Services](../quickstarts/create-communication-resource.md). L’authentification avec une chaîne de connexion est équivalente à l’authentification avec une clé d’accès.
- Authentification par **identité managée** pour les opérations SMS et Identity. L’identité managée (voir [Identité managée](../quickstarts/managed-identity.md)) convient aux applications de service s’exécutant dans un environnement de service approuvé. Pour s’authentifier avec une identité managée, une application de service crée des informations d’identification avec l’ID et un secret de l’identité managée, puis initialise les bibliothèques de client SMS ou Identity correspondantes. Pour plus d’informations, consultez [Création et gestion des jetons d’accès](../quickstarts/access-tokens.md).
- Authentification par **jeton d’accès utilisateur** pour les conversations et les appels. Les jetons d’accès utilisateur permettent à vos applications clientes de s’authentifier auprès des services de conversation et d’appel Azure Communication Services. Ces jetons sont générés dans un « service d’accès des utilisateurs approuvés » que vous devez créer. Ils sont ensuite fournis aux appareils clients qui utilisent ce jeton pour initialiser les bibliothèques clientes de conversation et d’appel. Pour plus d’informations, consultez [Ajouter la conversation à votre application](../quickstarts/chat/get-started.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer et gérer des ressources Communication Services](../quickstarts/create-communication-resource.md)
> [Créer une application d’identité managée Azure Active Directory à partir d’Azure CLI](../quickstarts/managed-identity-from-cli.md)
> [Création de jetons d’accès utilisateur](../quickstarts/access-tokens.md)

Pour plus d’informations, consultez les articles suivants :
- [En savoir plus sur l’architecture client et serveur](../concepts/client-and-server-architecture.md)
