---
title: Configurer la traduction
titleSuffix: Azure Cognitive Services
description: Cet article explique comment configurer les différentes options de traduction.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: bb90cb3a86acb6a94fa92c33d78ec596659e105f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608697"
---
# <a name="how-to-configure-translation"></a>Configurer la traduction

Cet article explique comment configurer les différentes options de traduction dans le Lecteur immersif.

## <a name="configure-translation-language"></a>Configurer la langue de traduction

Le paramètre `options` contient tous les indicateurs qui peuvent être utilisés pour configurer la traduction. Définissez le paramètre `language` sur la langue dans laquelle vous souhaitez traduire. Pour obtenir la liste complète des langues prises en charge, consultez [Prise en charge linguistique](./language-support.md).

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Traduire automatiquement le document au moment du chargement

Définissez `autoEnableDocumentTranslation` sur `true` pour activer la traduction automatique du document intégral lors de son chargement dans le Lecteur immersif.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Activer automatiquement la traduction des mots

Définissez `autoEnableWordTranslation` sur `true` pour activer la traduction des mots seuls.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [Guide de référence du SDK du Lecteur immersif](./reference.md)