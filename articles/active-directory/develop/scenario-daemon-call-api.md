---
title: Démon application appeler des API web (appeler des API web) - plateforme d’identité Microsoft
description: Découvrez comment créer une application démon par appels web API (appeler des API web)
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
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076269"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Application démon qu’appelle des API - web appeler une API web à partir de l’application

Une application démon peut appeler une API web à partir d’une application de démon de .NET ou appeler plusieurs pré-approuvée des API web.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Appeler une API web à partir d’une application de démon de .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Appeler plusieurs API

Pour les applications de démon, les API web que vous appelez doivent être préalablement approuvées. Il n’est pas un consentement incrémentiel avec les applications de démon (il n’existe aucune interaction utilisateur). L’administrateur client doit consentement préalable de l’application et toutes les autorisations d’API. Si vous souhaitez appeler plusieurs API, vous aurez besoin acquérir un jeton pour chaque ressource, chaque appel de temps `AcquireTokenForClient`. MSAL utilisera le cache de jetons d’application afin d’éviter les appels de service inutiles.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application démon - déplacement en production](./scenario-daemon-production.md)