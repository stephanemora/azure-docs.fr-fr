---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129064"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Les propriétés suivantes sont disponibles dans `extensionBundle` :

| Propriété | Description |
| -------- | ----------- |
| id | Espace de noms pour les offres groupées d’extension Microsoft Azure Functions. |
| version | Version de l’offre groupée à installer. Le runtime Functions récupère toujours la version autorisée maximale définie par la plage ou l’intervalle de version. La valeur de version ci-dessus autorise toutes les versions d’offre groupée jusqu’à 1.0.0 mais sans inclure la version 2.0.0. Pour plus d’informations, consultez la [notation d’intervalle de spécification de plages de versions](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Les versions d’offre groupées sont incrémentées à mesure que les packages contenus dans l’offre groupée changent. Des changements de version majeure se produisent lorsque les packages de l’offre groupée passent à la version majeure supérieure. Les changements de version majeure dans l’offre groupée coïncident généralement avec ceux du runtime Functions.  

L’ensemble actuel d’extensions installées par l’offre groupée par défaut est énuméré dans ce [fichier extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
