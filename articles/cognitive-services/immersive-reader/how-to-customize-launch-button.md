---
title: Modifier le bouton de lancement du Lecteur immersif
titleSuffix: Azure Applied AI Services
description: Cet article vous montre comment personnaliser le bouton qui lance le Lecteur immersif.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 03/08/2021
ms.author: metang
ms.openlocfilehash: 17076cc98da20c714523b2cdde58ea259ad76653
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367061"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Comment personnaliser le bouton Lecteur immersif

Cet article montre comment personnaliser le bouton qui lance le Lecteur immersif pour répondre aux besoins de votre application.

## <a name="add-the-immersive-reader-button"></a>Ajouter le bouton Lecteur immersif

Le kit SDK du Lecteur immersif fournit le style par défaut du bouton qui lance le Lecteur immersif. Utilisez l'attribut de classe `immersive-reader-button` pour activer ce style.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Personnaliser le style du bouton

Utilisez l’attribut `data-button-style` pour définir le style du bouton. Les valeurs autorisées sont `icon`, `text` et `iconAndText`. La valeur par défaut est `icon`.

### <a name="icon-button"></a>Bouton icône

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Le rendu est le suivant :

![Ceci est le bouton de texte rendu](./media/button-icon.png)

### <a name="text-button"></a>Bouton texte

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Le rendu est le suivant :

![Ceci est le bouton de Lecteur immersif rendu.](./media/button-text.png)

### <a name="icon-and-text-button"></a>Bouton icône et texte

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Le rendu est le suivant :

![Bouton icône](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Personnaliser le texte du bouton

Configurez la langue et le texte de remplacement du bouton en utilisant l’attribut `data-locale`. La langue par défaut est l'anglais.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Personnaliser la taille de l’icône

La taille de l’icône du Lecteur immersif peut être configurée en utilisant l’attribut `data-icon-px-size`. La taille de l’icône est définie en pixels. La taille par défaut est de 20 px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [Guide de référence du SDK du Lecteur immersif](./reference.md)