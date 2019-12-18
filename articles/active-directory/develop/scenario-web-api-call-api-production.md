---
title: Passer en production une API web appelant des API web – Plateforme d’identités Microsoft | Azure
description: Découvrez comment passer en production une API web qui appelle des API web.
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
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965167"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>API web appelant des API web - passage en production

Une fois que vous avez acquis un jeton pour appeler des API web, vous pouvez faire passer votre application en production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>En savoir plus

Maintenant que vous connaissez les principes fondamentaux de l’appel des API web à partir de votre propre API web, vous trouverez peut-être ce didacticiel utile. Il décrit le code utilisé pour créer une API web protégée qui appelle des API web.

| Exemple | Plateforme | Description |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | API web ASP.NET Core 2.2, Bureau (WPF) | API web ASP.NET Core 2.2 appelant Microsoft Graph, lui-même appelé à partir d’une application WPF à l’aide de la plateforme d’identités Microsoft (v2.0) |
