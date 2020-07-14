---
title: Stocker les préférences utilisateur
titleSuffix: Azure Cognitive Services
description: Cet article vous indique comment stocker les préférences de l’utilisateur.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486868"
---
# <a name="how-to-store-user-preferences"></a>Stocker les préférences de l’utilisateur

Cet article vous indique comment stocker les préférences de l’utilisateur. Cette fonctionnalité est conçue comme un autre moyen de stocker les préférences de l’utilisateur dans le cas où l’utilisation de cookies n’est pas souhaitable ou possible.

## <a name="how-to-enable-storing-user-preferences"></a>Comment activer le stockage des préférences de l’utilisateur

Le paramètre `options` contient le rappel `onPreferencesChanged`. Cette fonction est appelée chaque fois que l’utilisateur modifie ses préférences (par exemple, il modifie la taille du texte, la couleur du thème, la police, etc.). Le paramètre `value` contient une chaîne qui représente les préférences actuelles de l’utilisateur. Cette chaîne peut être stockée à l’aide du mécanisme de votre choix.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Comment charger les préférences de l’utilisateur dans le Lecteur immersif

Transmettez les préférences de l’utilisateur au Lecteur immersif en utilisant le paramètre `preferences`. Voici un exemple tout simple pour stocker et charger les préférences de l’utilisateur :

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [Guide de référence du SDK du Lecteur immersif](./reference.md)