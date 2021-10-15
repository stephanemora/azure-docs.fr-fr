---
title: Étapes courantes pour la migration d’un client public vers MSAL
description: Fichier include qui explique les étapes courantes à exécuter pour toutes les applications clientes publiques lorsqu’il s’agit de migrer d’ADAL vers MSAL.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 09/08/2021
ms.author: sahmalik
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 419b038f89df0d7fb5185fe2ef8ae43c76db7f7f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838467"
---
Les étapes suivantes pour la mise à jour du code s’appliquent à tous les scénarios de client confidentiel :

1. Ajoutez l’espace de noms MSAL.NET dans votre code source : `using Microsoft.Identity.Client;`.
2. Au lieu d’instancier `AuthenticationContext`, utilisez `PublicClientApplicationBuilder.Create` pour instancier `IPublicClientApplication`.
3. Au lieu de la chaîne `resourceId`, MSAL.NET utilise les étendues. Comme les applications qui utilisent ADAL.NET sont pré-autorisées, vous pouvez toujours utiliser les étendues suivantes : `new string[] { $"{resourceId}/.default" }`.
4. Remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IPublicClientApplication.AcquireTokenXXX`, où *XXX* dépend de votre scénario.