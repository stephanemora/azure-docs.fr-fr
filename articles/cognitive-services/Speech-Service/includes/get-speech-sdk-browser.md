---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: c1c8fa44613499cba5a8a1e8c1505cdaa4b1ee53
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539976"
---
:::row:::
    :::column span="3":::
        Le kit de développement logiciel (SDK) Speech pour JavaScript est disponible sous forme de package npm. Consultez <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk </a> et le référentiel GitHub associé, <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Bien que le kit SDK Speech pour JavaScript soit disponible sous forme de package npm (ce qui permet aux navigateurs web clients et à Node.js de l’utiliser), tenez compte des diverses implications architecturales de chaque environnement. Par exemple, le <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">DOM (Document Object Model) </a> n’est pas disponible pour les applications côté serveur, tout comme le <a href="https://nodejs.org/api/fs.html" target="_blank">système de fichiers </a> ne l’est pas pour les applications côté client.

### <a name="nodejs-package-manager-npm"></a>Gestionnaire de package Node.js (NPM)

Pour installer le kit SDK Speech pour JavaScript, exécutez la commande `npm install` suivante.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Balise de script HTML

Vous pouvez également inclure directement une balise `<script>` dans l’élément HTMLs `<head>`, en vous basant sur le syndicat NPM <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">**JSDelivr**</a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Pour plus d’informations, consultez le <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Guide de démarrage rapide du Kit de développement logiciel (SDK) Speech du navigateur web </a>.
