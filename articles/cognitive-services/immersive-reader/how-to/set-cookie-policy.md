---
title: Définir la stratégie de cookie du lecteur immersif
titleSuffix: Azure Cognitive Services
description: Cet article vous explique comment définir la stratégie de cookie pour le lecteur immersif.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946028"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Guide pratique pour définir la stratégie de cookie pour le lecteur immersif

Par défaut, le lecteur immersif désactive l’utilisation des cookies. Si vous activez l’utilisation des cookies, le lecteur immersif peut utiliser des cookies pour gérer les préférences de l’utilisateur et suivre l’utilisation des fonctionnalités. Si vous activez l’utilisation des cookies dans le lecteur immersif, tenez compte des exigences pour la stratégie de conformité des cookies de l’Union européenne. Il incombe à l’application hôte d’obtenir les éventuels consentements nécessaires de l’utilisateur conformément à la stratégie de conformité des cookies de l’Union européenne.

La stratégie de cookie peut être définie via les [options](../reference.md#options) du lecteur immersif. Pour plus d’informations, consultez [enum CookiePolicy](../reference.md#cookiepolicy-enum).

## <a name="enable-cookie-usage"></a>Activer l’utilisation des cookies

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Désactiver l’utilisation des cookies

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Étapes suivantes

* Consulter le [Guide de démarrage rapide Node.js](../quickstart-nodejs.md) pour voir ce que vous pouvez faire d’autre avec le SDK Lecteur immersif en utilisant Node.js
* Consulter le [didacticiel Python](../tutorial-python.md) pour voir ce que vous pouvez faire d’autre avec le kit SDK Lecteur immersif à l’aide de Python
* Consulter le [didacticiel Swift](../tutorial-ios-picture-immersive-reader.md) pour voir ce que vous pouvez faire d’autre avec le kit SDK Lecteur immersif à l’aide de Swift
* Explorer le [SDK Lecteur Immersif](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](../reference.md)