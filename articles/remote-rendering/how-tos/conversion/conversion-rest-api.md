---
title: API REST de conversion de ressources
description: Décrit comment convertir une ressource via l’API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 4f8ac72e2b598a6c7631d691cc1bfb82cdba7599
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530261"
---
# <a name="use-the-model-conversion-rest-api"></a>Utiliser l’API REST de conversion de modèle

Le service de [conversion de modèle](model-conversion.md) est contrôlé via une [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Cette API permet de créer des conversions, d’obtenir des propriétés de conversion et de lister les conversions existantes.

## <a name="rest-api-reference"></a>Référence d’API REST

La documentation de référence sur l’API REST de Remote Rendering est disponible [ici](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering), et les définitions de Swagger sont disponibles [ici](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Dans le [dépôt d’exemples ARR](https://github.com/Azure/azure-remote-rendering), dans le dossier *Scripts*, nous fournissons un script PowerShell appelé *Conversion.ps1*, qui illustre l’utilisation de notre service. Le script et sa configuration sont décrits ici : [Exemples de scripts PowerShell](../../samples/powershell-example-scripts.md). Nous fournissons également des kits SDK pour [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java et Python.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser Stockage Blob Azure pour une conversion de modèle](blob-storage.md)
- [Conversion de modèle](model-conversion.md)
