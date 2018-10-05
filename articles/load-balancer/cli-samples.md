---
title: Exemples Azure CLI pour Load Balancer | Microsoft Docs
description: Exemples d’interface de ligne de commande Azure
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 41e80e04051a24fd32086c61c65bc3eec1564c57
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094928"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Exemples Azure CLI pour Load Balancer

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| | |
|-|-|
| [Équilibrer le trafic sur les machines virtuelles pour la haute disponibilité](./scripts/load-balancer-linux-cli-sample-nlb.md) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge. |
| [Équilibrer la charge de machines virtuelles entre des zones de disponibilité](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Crée trois machines virtuelles dans différentes zones de disponibilité au sein d’une région et un équilibreur de charge standard avec une adresse IP frontend redondante dans une zone. Cette configuration d’équilibreur de charge vous permet de mieux protéger vos applications et vos données dans l’éventualité (peu probable) d’une défaillance ou d’une perte d’un centre de données entier. |
|[Équilibrer la charge de machines virtuelles dans une zone de disponibilité spécifique](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Crée trois machines virtuelles, un équilibreur de charge standard avec une adresse IP frontend zonale qui permet d’aligner le chemin de données et les ressources dans une zone unique d’une région donnée.|
| [Équilibrer la charge de plusieurs sites web sur des machines virtuelles](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Crée deux machines virtuelles avec plusieurs configurations IP, jointes à un groupe à haute disponibilité Azure, accessible via Azure Load Balancer. |
| | |

