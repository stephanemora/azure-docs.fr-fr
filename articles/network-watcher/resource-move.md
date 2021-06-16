---
title: Déplacer des ressources Azure Network Watcher | Microsoft Docs
description: Déplacer des ressources Azure Network Watcher d’une région à l’autre
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 273dcea805e3b6ec78dfc4bd14be616f6109c097
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112032066"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Déplacement de ressources Azure Network Watcher d’une région à l’autre

## <a name="moving-the-network-watcher-resource"></a>Déplacement de la ressource Network Watcher
La ressource Network Watcher représente le service back-end pour Network Watcher et est entièrement gérée par Azure. Les clients n’ont pas besoin de la gérer. L’opération de déplacement n’est pas prise en charge sur cette ressource.

## <a name="moving-child-resources-of-network-watcher"></a>Déplacement de ressources enfants de Network Watcher
Le déplacement de ressources d’une région à l’autre n’est actuellement pas pris en charge pour les ressources enfants du type `*networkWatcher*`.

## <a name="next-steps"></a>Étapes suivantes
* Lisez la [vue d’ensemble de Network Watcher](./network-watcher-monitoring-overview.md).
* Consultez la [FAQ sur Network Watcher](./frequently-asked-questions.yml).