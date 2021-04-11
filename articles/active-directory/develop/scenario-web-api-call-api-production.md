---
title: Passer en production une API web appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment passer en production une API web qui appelle des API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675870"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Une API web qui appelle des API web : Passer en production

Après avoir acquis un jeton pour appeler des API web, voici quelques aspects à prendre en compte lors du déplacement de votre application vers la production.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les principes fondamentaux de l’appel des API web à partir de votre propre API web, vous trouverez peut-être le didacticiel suivant utile. Il décrit le code utilisé pour créer une API web protégée qui appelle des API web.

| Exemple | Plateforme | Description |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph), chapitre 1 | API web ASP.NET Core, Bureau (WPF) | L’API web ASP.NET Core appelle Microsoft Graph, que vous appelez à partir d’une application WPF à l’aide de la plateforme d’identités Microsoft. |
