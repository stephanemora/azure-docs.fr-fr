---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878241"
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

L’ensemble actuel d’extensions installées par l’offre groupée par défaut est énuméré dans ce [fichier extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
