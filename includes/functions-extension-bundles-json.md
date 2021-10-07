---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 23e75e2cf2e097e08c0b6bf3c4b0b8c9842a9184
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609748"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

Les propriétés suivantes sont disponibles dans `extensionBundle` :

| Propriété | Description |
| -------- | ----------- |
| id | Espace de noms pour les offres groupées d’extension Microsoft Azure Functions. |
| version | Version de l’offre groupée à installer. Le runtime Functions récupère toujours la version autorisée maximale définie par la plage ou l’intervalle de version. La valeur de version ci-dessus autorise toutes les versions de bundle jusqu’à 2.0.0 mais sans inclure la version 3.0.0. Pour plus d’informations, consultez la [notation d’intervalle de spécification de plages de versions](/nuget/reference/package-versioning#version-ranges). |