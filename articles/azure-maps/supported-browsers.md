---
title: Kit SDK Web pris en charge des navigateurs, Azure Maps | Microsoft Docs
description: En savoir plus sur les navigateurs pris en charge pour le Kit de développement Web Maps
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738386"
---
# <a name="web-sdk-supported-browsers"></a>Navigateurs pris en charge pour le kit SDK web

Le Kit de développement logiciel Azure Maps Web fournit une fonction d’assistance appelée [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Cette fonction détecte si un navigateur web a l’ensemble minimal de fonctionnalités de WebGL requises pour prendre en charge le chargement et le rendu du contrôle de carte. Voici un exemple montrant comment utiliser la fonction :

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Bureau

Le Kit de développement logiciel Azure Maps Web prend en charge les navigateurs de bureau suivants :

- Microsoft Edge (version actuelle et précédente)
- Google Chrome (versions actuelles et précédente)
- Mozilla Firefox (version actuelle et précédente)
- Apple Safari (Mac OS X) (version actuelle et précédente)

Voir aussi [ciblent les navigateurs hérités](#Target-Legacy-Browsers) plus loin dans cet article.

## <a name="mobile"></a>Mobile

Le Kit de développement logiciel Azure Maps Web prend en charge les navigateurs mobiles suivants :

- Android
  - Version actuelle de Chrome sur Android 6.0 et versions ultérieures
  - Chrome WebView sur Android 6.0 et versions ultérieures
- iOS
  - Safari mobile sur la version principale actuelle et précédente d’e/s
  - UIWebView et WKWebView sur la version principale actuelle et précédente d’e/s
  - Version actuelle de Chrome pour iOS

> [!TIP]
> Si vous incorporez un mappage à l’intérieur d’une application mobile à l’aide d’un contrôle WebView, vous préférerez peut-être utiliser le [package npm du SDK Web Azure Maps](https://www.npmjs.com/package/azure-maps-control) au lieu de référencer la version du SDK qui est hébergé sur Azure Content Delivery Réseau. Cette approche réduit le temps de chargement, car celui-ci est déjà se trouver sur le périphérique l’utilisateur et n’a pas besoin d’être téléchargées en cours d’exécution.

## <a name="nodejs"></a>Node.js

Les modules du SDK Web suivants sont également pris en charge dans Node.js :

- Module Services ([documentation](how-to-use-services-module.md) | [module npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Cible les navigateurs hérités

Vous pouvez souhaiter de cibler des navigateurs plus anciens qui ne prennent pas en charge WebGL ou qui ont des uniquement limitées de prise en charge. Dans ce cas, nous vous recommandons d’utiliser les services Azure Maps avec un contrôle de carte de l’open source comme [notice](https://leafletjs.com/). Voici un exemple :

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + notice" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + notice</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le Kit de développement logiciel Azure Maps Web :

> [!div class="nextstepaction"]
> [Commandes de la carte](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Module Services](how-to-use-services-module.md)
