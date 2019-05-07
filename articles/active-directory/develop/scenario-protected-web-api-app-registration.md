---
title: API web - inscription de l’application protégée | Azure
description: Découvrez comment créer une API Web protégée et les informations nécessaires inscrire l’application.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1d8c8fcf84cd008957fcdb7cd14c4a07d9f3643
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074904"
---
# <a name="protected-web-api---app-registration"></a>API - inscription de l’application web protégée

Cet article explique les spécificités de l’inscription d’application pour une API web protégée.

Consultez [Démarrage rapide : Inscrire une application avec la plateforme Microsoft identity](quickstart-register-app.md) pour connaître les étapes courantes sur l’inscription de l’application.

## <a name="accepted-token-version"></a>Version du jeton acceptée

Le point de terminaison Microsoft identity plateforme peut émettre les deux types de jetons : jetons v1.0 et v2.0 jetons. Plus d’informations sur ces jetons dans [de jetons d’accès](access-tokens.md). La version de jeton acceptée varie selon le **pris en charge les types de comptes** que vous avez choisi lorsque vous avez créé votre application :

- Si la valeur de **pris en charge les types de comptes** est **comptes dans n’importe quel annuaire d’organisation et les comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)**, la version de jeton acceptée sera v2.0.
- Sinon, la version de jeton acceptée sera v2.0.

Une fois que vous avez créé l’application, vous pouvez modifier la version de jeton acceptée en suivant ces étapes :

1. Dans le portail Azure, sélectionnez votre application, puis le **manifeste** pour votre application.
2. Dans le manifeste, recherchez **« accessTokenAcceptedVersion »** et voir sa valeur est **2**. Cette propriété permet de savoir que l’API web accepte les jetons v2.0 Azure AD. S’il s’agit **null**, la version de jeton acceptée sera v1.0.
3. Sélectionnez **Enregistrer**.

> [!NOTE]
> C’est à l’API web pour décider quelle version de jeton (v1.0 ou v2.0) il accepte. Lorsque les clients demandent un jeton pour votre site web API à l’aide de Microsoft identity plateforme point de terminaison v2.0, ils obtiendront un jeton qui indique que la version est acceptée par l’API web.

## <a name="no-redirect-uri"></a>Aucun URI de redirection

API Web n’avez pas besoin de s’inscrire à un URI de redirection car aucun utilisateur n’est connecté de manière interactive.

## <a name="expose-an-api"></a>Exposer une API

Un autre paramètre spécifique aux API web est l’API exposée et les étendues exposées.

### <a name="resource-uri-and-scopes"></a>URI de ressource et étendues

Étendues présentent généralement sous la forme `resourceURI/scopeName`. Pour Microsoft Graph, les étendues ont des raccourcis tels que `User.Read`, mais cette chaîne est simplement un raccourci pour `https://graph.microsoft.com/user.read`.

Lors de l’inscription d’application, vous devez définir les paramètres suivants :

- Recommande d’une ressource URI - par défaut, le portail d’inscription des applications qui vous permettent d’utiliser `api://{clientId}`. Cet URI de ressource est unique, mais il n’est pas lisible. Vous pouvez le modifier, mais assurez-vous qu’il est unique.
- Une ou plusieurs étendues

Les étendues sont également affichés sur l’écran de consentement est présentée aux utilisateurs finaux qui utilisent votre application. Par conséquent, vous devrez fournir les chaînes correspondantes décrivant l’étendue :

- Comme indiqué par l’utilisateur final
- Comme indiqué par l’administrateur de locataire, qui peut donner leur consentement administrateur

### <a name="how-to-expose-the-api"></a>Comment exposer l’API

1. Sélectionnez le **exposer une API** section dans l’inscription de l’application, et :
   1. sélectionner **Ajouter une étendue**.
   1. Accepter l’URI ID d’Application proposé (api  :// {clientId}) en sélectionnant **enregistrer et continuer**.
   1. Entrez les paramètres suivants :
      - Pour **Nom_Étendue**, utilisez `access_as_user`.
      - Pour **qui peut accepter**, assurez-vous que le **administrateurs et utilisateurs** option est sélectionnée.
      - Dans **nom complet de consentement administrateur**, type `Access TodoListService as a user`.
      - Dans **description du consentement administrateur**, type `Accesses the TodoListService Web API as a user`.
      - Dans **nom complet de consentement utilisateur**, type `Access TodoListService as a user`.
      - Dans **description du consentement utilisateur**, type `Accesses the TodoListService Web API as a user`.
      - Conserver **état** définie sur **activé**.
      - Sélectionnez **ajouter une étendue**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-protected-web-api-app-configuration.md)
