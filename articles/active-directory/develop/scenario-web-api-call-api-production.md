---
title: Web API que les appels API web (passer à la production) - plateforme d’identité Microsoft
description: Découvrez comment créer une web API que les appels en aval web API (passer à la production).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074754"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>API Web qui appelle des API - web passer en production

Une fois que vous avez acquis un jeton pour appeler des API web, vous pouvez déplacer votre application en production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>En savoir plus

Maintenant que vous connaissez les principes fondamentaux de l’appel des API web à partir de vos propres API web, vous souhaitez peut-être ce didacticiel, qui décrit le code qui a utilisé pour créer une API web protégée appelle des API web.

| Exemple | Plateforme | Description |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | 2.2 API Web ASP.NET Core, Desktop (WPF) | API de Web ASP.NET Core 2.2 appeler Microsoft Graph, lui-même appelé à partir d’une application WPF à l’aide de la plateforme Microsoft identity (v2.0) |
