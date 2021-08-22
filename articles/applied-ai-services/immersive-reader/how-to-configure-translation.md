---
title: Configurer la traduction
titleSuffix: Azure Applied AI Services
description: Cet article explique comment configurer les différentes options de traduction.
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: e8fb21ac286997601ae8896616826651bad8b1f7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524844"
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