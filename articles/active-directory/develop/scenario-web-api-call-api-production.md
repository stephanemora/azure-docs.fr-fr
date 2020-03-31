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
ms.openlocfilehash: 474f771f007666179295f4502108acee88d1dc33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701720"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Une API web qui appelle des API web : Passer en production

Une fois que vous avez acquis un jeton pour appeler des API web, vous pouvez faire passer votre application en production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>En savoir plus

Maintenant que vous connaissez les principes fondamentaux de l’appel des API web à partir de votre propre API web, vous trouverez peut-être le didacticiel suivant utile. Il décrit le code utilisé pour créer une API web protégée qui appelle des API web.

| Exemple | Plateforme | Description |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | API web ASP.NET Core 2.2, Bureau (WPF) | API web ASP.NET Core 2.2 appelant Microsoft Graph, que vous appelez à partir d’une application WPF à l’aide de la plateforme d’identités Microsoft (v2.0). |
