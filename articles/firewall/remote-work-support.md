---
title: Prise en charge du travail à distance par le pare-feu Azure
description: Cet article explique comment le pare-feu Azure peut prendre en charge vos exigences de travail à distance.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289406"
---
# <a name="azure-firewall-remote-work-support"></a>Prise en charge du travail à distance par le pare-feu Azure

Le Pare-feu Azure est un service de sécurité réseau informatique managé qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu avec état intégral, doté d’une haute disponibilité intégrée et d’une scalabilité illimitée dans le cloud. 

## <a name="firewall-rules"></a>Règles de pare-feu

Vous pouvez utiliser le pare-feu Azure pour sécuriser l’accès RDP entrant de votre infrastructure de bureau virtuel (VDI) à votre réseau virtuel Azure à l’aide des [règles DNAT](rule-processing.md) du pare-feu Azure. Windows Virtual Desktop (WVD) ne vous oblige pas à ouvrir un accès entrant à votre réseau virtuel. Toutefois, vous devez autoriser un ensemble de connexions réseau sortantes pour les machines virtuelles WVD qui s’exécutent dans votre réseau virtuel. Pour plus d’informations, consultez [Qu’est-ce que Windows Virtual Desktop ?](../virtual-desktop/overview.md#requirements)

Vous pouvez configurer cet accès sortant à l’aide des règles d’application du pare-feu Azure. Pour plus d’informations, consultez [Didacticiel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md)

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur [Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).