---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: lajanuar
ms.custom: devx-track-js
ms.openlocfilehash: 676c621c7e5e3eafcef30be1812049a4ac5744ff
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525528"
---
:::row:::
    :::column span="3":::
        Le kit de développement logiciel (SDK) Speech pour JavaScript est disponible sous forme de package npm. Consultez <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk </a> et le référentiel GitHub associé, <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Bien que le kit SDK Speech pour JavaScript soit disponible sous forme de package npm (ce qui permet à Node.js et aux navigateurs web clients de l’utiliser), tenez compte des diverses implications architecturales de chaque environnement. Par exemple, le <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">DOM (Document Object Model) </a> n’est pas disponible pour les applications côté serveur, tout comme le <a href="https://nodejs.org/api/fs.html" target="_blank">système de fichiers </a> ne l’est pas pour les applications côté client.

### <a name="nodejs-package-manager-npm"></a>Gestionnaire de package Node.js (NPM)

Pour installer le kit SDK Speech pour JavaScript, exécutez la commande `npm install` suivante.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Pour plus d’informations, consultez le <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Guide de démarrage rapide du kit de développement logiciel (SDK) Speech Node.js </a>.
