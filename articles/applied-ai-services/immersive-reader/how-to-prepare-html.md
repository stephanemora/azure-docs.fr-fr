---
title: Comment préparer du contenu HTML pour un Lecteur immersif
titleSuffix: Azure Applied AI Services
description: Découvrez comment lancer le Lecteur immersif en utilisant HTML, JavaScript, Python, Android ou iOS. Lecteur immersif utilise des techniques éprouvées pour améliorer la compréhension de la lecture pour les apprenants d’une langue, les lecteurs débutants et les étudiants présentant des différences d’apprentissage.
author: erhopf
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 4efadfaed4980afd035f36d530f87a971eeb4964
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524127"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>Comment préparer du contenu HTML pour un Lecteur immersif

Cet article vous montre comment structurer votre code HTML et récupérer le contenu afin qu’il puisse être utilisé par le Lecteur immersif.

## <a name="prepare-the-html-content"></a>Préparer le contenu HTML

Placez le contenu que vous souhaitez afficher dans le Lecteur immersif à l’intérieur d’un élément conteneur. Assurez-vous que l’élément conteneur a un `id` unique. Le Lecteur immersif fournit la prise en charge des éléments HTML de base. Consultez la [référence](reference.md#html-support) pour plus d’informations.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Recevoir le contenu HTML dans JavaScript

Utilisez l’`id` de l’élément conteneur pour récupérer le contenu HTML dans votre code JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Lancer le Lecteur immersif avec votre contenu HTML

Lors de l’appel de `ImmersiveReader.launchAsync`, définissez la propriété `mimeType` du bloc sur `text/html` pour activer l’affichage HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [Guide de référence du SDK du Lecteur immersif](reference.md)