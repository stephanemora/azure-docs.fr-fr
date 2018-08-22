---
title: Prise en charge du gestionnaire de rendus Azure Batch
description: Utilisation d’Azure pour le rendu à l’aide de l’intégration du gestionnaire de rendus Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 798b2b457016856662f392af25d987788f73c242
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036720"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Utilisation d’Azure Batch avec des gestionnaires de groupe de rendus

Si vous utilisez un groupe de rendus local existant, il est fort probable qu’un gestionnaire de rendus contrôle la capacité du groupe de rendus et les travaux de rendu.

Azure fournit la prise en charge intégrée ou des modules complémentaires pour les gestionnaires de rendus courants. Vous pouvez ensuite ajouter et supprimer des machines virtuelles Azure, y compris les machines virtuelles avec une licence d’application de paiement à l’utilisation et les machines virtuelles de faible priorité.

Les gestionnaires de rendus suivants sont pris en charge :

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](http://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Utilisation d’Azure avec PipelineFX Qube

Les scripts et instructions permettant d’utiliser des machines virtuelles du pool Azure Batch comme des Workers Qube se trouvent dans le [référentiel GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Utilisation d’Azure avec Royal Render

Azure et Azure Batch sont intégrés dans Royal Render, ce qui vous permet d’étendre un groupe de rendus avec des machines virtuelles Azure. Pour obtenir un résumé, consultez les [fichiers d’aide](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Pour obtenir un exemple d’un client Royal Render utilisant l’intégration d’Azure, consultez le [témoignage client de Jellyfish Pictures](https://customers.microsoft.com/en-gb/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Utilisation d’Azure avec Thinkbox Deadline

Les scripts et instructions permettant d’utiliser des machines virtuelles du pool Azure Batch comme des esclaves Deadline se trouvent dans le [référentiel GitHub](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>Étapes suivantes

Essayez l’intégration Azure Batch de votre gestionnaire de rendus, en utilisant le plug-in et les instructions appropriés sur GitHub, le cas échéant.