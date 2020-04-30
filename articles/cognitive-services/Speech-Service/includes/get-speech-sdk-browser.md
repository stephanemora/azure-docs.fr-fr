---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399881"
---
:::row:::
    :::column span="3":::
        Le Kit de développement logiciel (SDK) JavaScript Speech est disponible sous la forme d’un package npm. Consultez <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span></a> et son référentiel GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-Speech-SDK-js<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Bien que le Kit de développement logiciel (SDK) JavaScript Speech soit disponible en tant que package npm, les navigateurs web clients et Node.js peuvent l’utiliser. Tenez compte des diverses implications architecturales de chaque environnement. Par exemple, le <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">DOM (Document Object Model) <span class="docon docon-navigate-external x-hidden-focus"></span></a> n’est pas disponible pour les applications côté serveur, tout comme le <a href="https://nodejs.org/api/fs.html" target="_blank">système de fichiers <span class="docon docon-navigate-external x-hidden-focus"></span></a> ne l’est pas pour les applications côté client.

### <a name="nodejs-package-manager-npm"></a>Gestionnaire de package Node.js (NPM)

Pour installer le Kit de développement logiciel (SDK) JavaScript Speech, exécutez la commande `npm install` suivante.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Balise de script HTML

Vous pouvez également inclure directement une balise `<script>` dans l’élément HTMLs `<head>`, en vous basant sur le syndicat NPM <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">**JSDelivr** <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Pour plus d’informations, consultez le <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Guide de démarrage rapide du Kit de développement logiciel (SDK) Speech du navigateur web <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
