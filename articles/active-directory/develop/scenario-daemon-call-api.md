---
title: Application démon conçue pour appeler des API web (appel des API web) - Plateforme d’identités Microsoft
description: Apprendre à générer une application démon qui appelle des API web (appel des API web)
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076269"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Application démon appelant des API web - appeler une API web à partir de l’application

Une application démon peut appeler une API web à partir d’une application démon .NET ou appeler plusieurs API web pré-approuvées.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Appeler une API web à partir d’une application démon .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Appels de plusieurs API

Pour les applications de démon, les API web que vous appelez doivent être pré-approuvées. Il n’y aura pas de consentement incrémentiel avec des applications démons (il n’y a aucune interaction utilisateur). L’administrateur locataire doit approuver l’application et toutes les autorisations d’API au préalable. Si vous souhaitez appeler plusieurs API, vous aurez besoin d’acquérir un jeton pour chaque ressource, en appelant `AcquireTokenForClient` à chaque fois. MSAL utilisera le cache de jetons d’application afin d’éviter les appels de service inutiles.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application démon - déplacement en production](./scenario-daemon-production.md)