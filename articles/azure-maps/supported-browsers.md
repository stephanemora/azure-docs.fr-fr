---
title: Prise en charge des navigateurs web - Azure Maps | Microsoft Docs
description: En savoir plus sur les navigateurs web pris en charge pour le Kit de développement Web Maps
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 2678fa7c9290c7ec0a27288e7739fde4bb0870fd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501270"
---
# <a name="supported-web-browsers"></a>Navigateurs web pris en charge

Le Kit de développement logiciel Azure Maps Web fournit une fonction d’assistance [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) pour détecter si un navigateur web a la valeur minimale des fonctionnalités de WebGL pour prendre en charge le chargement et le rendu du contrôle de carte requises. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Add your map code here.
}
```

Le Kit de développement logiciel Azure Maps Web prend en charge les navigateurs web suivants.

## <a name="desktop"></a>Bureau

- La version actuelle et précédente de Microsoft Edge 
- La version actuelle et précédente de Chrome 
- La version actuelle et précédente de Firefox 
- La version actuelle et précédente de Safari (Mac OS X) 

Voir aussi [ciblent les navigateurs hérités](#Target-Legacy-Browsers).

## <a name="mobile"></a>Mobile

-  Android
    * Version actuelle de Chrome sur Android 6.0 +
    * WebView chrome sur Android 6.0 +
- iOS
    * Safari mobile sur la version principale actuelle et précédente d’e/s
    * UIWebView et WKWebView sur la version principale actuelle et précédente d’e/s
    * Version actuelle de Chrome pour iOS

> [!TIP]
> Si vous incorporez un mappage à l’intérieur d’une vue de l’application mobile un contrôle WebView, vous trouverez peut-être à l’aide de la [package npm du SDK Web Azure Maps](https://www.npmjs.com/package/azure-maps-control) préférable à référencer la version hébergée de CDN du SDK. Cela permet de réduire le temps de chargement car le Kit de développement sera déjà se trouver sur le périphérique l’utilisateur et n’avez pas besoin d’être téléchargées lors de l’exécution.

## <a name="nodejs"></a>Node.js

Les modules du SDK Web suivants sont également pris en charge dans Node.js :

- Module Services ([documentation](how-to-use-services-module.md) | [module npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Cible les navigateurs hérités

Si vous avez besoin cibler des navigateurs web plus anciens qui ne peuvent pas prendre en charge ou sont limitée à la prise en charge pour WebGL, il est recommandé d’utiliser les Services de cartes Azure en combinaison avec un contrôle de carte de l’open source comme [notice](https://leafletjs.com/). 

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + notice" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + notice</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>