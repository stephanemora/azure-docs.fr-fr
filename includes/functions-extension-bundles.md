---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442269"
---
Le moyen le plus simple d’installer les extensions de liaison consiste à activer les [offres groupées d’extension](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Avec les offres groupées activées, un ensemble prédéfini de packages d’extension sont automatiquement installés.

Pour activer les offres groupées d’extension, ouvrez le fichier *host.json* fichiers et mettez à jour son contenu pour correspondre au code suivant :

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```