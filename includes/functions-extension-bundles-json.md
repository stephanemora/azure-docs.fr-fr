---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381861"
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
| version | Version de l’offre groupée à installer. Le runtime Functions récupère toujours la version autorisée maximale définie par la plage ou l’intervalle de version. La valeur de version ci-dessus autorise toutes les versions d’offre groupée jusqu’à 1.0.0 mais sans inclure la version 2.0.0. Pour plus d’informations, consultez la [notation d’intervalle de spécification de plages de versions](/nuget/reference/package-versioning#version-ranges). |

Les versions d’offre groupées sont incrémentées à mesure que les packages contenus dans l’offre groupée changent. Des changements de version majeure se produisent lorsque les packages de l’offre groupée passent à la version majeure supérieure. Les changements de version majeure dans l’offre groupée coïncident généralement avec ceux du runtime Functions.  

L’ensemble actuel d’extensions installées par l’offre groupée par défaut est énuméré dans ce [fichier extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
