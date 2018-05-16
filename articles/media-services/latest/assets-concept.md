---
title: Éléments multimédias dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les éléments multimédias et comment ils sont utilisés par Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Éléments multimédias

Un **élément multimédia** contient des fichiers numériques (y compris vidéo, audio, images, collections de miniatures, légendes et fichiers de sous-titres) et les métadonnées associées à ces fichiers. Une fois les fichiers numériques chargés dans un élément multimédia, ils peuvent être utilisés dans des flux de travail de diffusion et d’encodage Media Services.

Un élément multimédia est mappé à un conteneur d’objets blob dans le [compte de stockage Azure](storage-account-concept.md) et les fichiers de l’élément multimédia sont stockés en tant qu’objets blob de blocs dans ce conteneur. Vous pouvez interagir avec les fichiers d’éléments multimédias dans les conteneurs à l’aide des clients SDK Stockage.

Azure Media Services prend en charge les niveaux d’objet blob lorsque le compte utilise le stockage à usage général v2 (GPv2). Avec GPv2, vous pouvez déplacer les fichiers vers un stockage froid. Le stockage froid est approprié pour archiver les fichiers mezzanine lorsqu’ils ne sont plus nécessaires (par exemple, une fois qu’ils ont été encodés.)

Dans Media Services v3, l’entrée de travail peut être créée à partir d’éléments multimédias ou d’URL HTTP(s). Pour créer un élément multimédia qui peut être utilisé comme entrée pour votre travail, consultez [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).

Consultez également les articles sur les [comptes de stockage dans Media Services](storage-account-concept.md) et les [transformations et travaux](transform-concept.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Diffuser un fichier](stream-files-dotnet-quickstart.md)
