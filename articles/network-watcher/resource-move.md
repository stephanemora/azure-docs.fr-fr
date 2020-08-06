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
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388310"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Déplacement de ressources Azure Network Watcher d’une région à l’autre

## <a name="moving-the-network-watcher-resource"></a>Déplacement de la ressource Network Watcher
La ressource Network Watcher représente le service back-end pour Network Watcher et est entièrement gérée par Azure. Les clients n’ont pas besoin de la gérer. L’opération de déplacement n’est pas prise en charge sur cette ressource.

## <a name="moving-child-resources-of-network-watcher"></a>Déplacement de ressources enfants de Network Watcher
Le déplacement de ressources d’une région à l’autre n’est actuellement pas pris en charge pour les ressources enfants du type `*networkWatcher*`.

## <a name="next-steps"></a>Étapes suivantes
* Lisez la [vue d’ensemble de Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
* Consultez la [FAQ sur Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions).
