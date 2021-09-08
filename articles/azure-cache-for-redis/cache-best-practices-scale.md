---
title: Bonnes pratiques concernant la mise à l’échelle d’Azure Cache pour Redis
titleSuffix: Azure Cache for Redis
description: Apprenez comment mettre à l’échelle Azure Cache pour Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 01fbebb06ba408232add1f0f05c5b715816e9e69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116240"
---
# <a name="scaling"></a>Mise à l'échelle

## <a name="scaling-under-load"></a>Mise à l’échelle sous charge

Lors de la mise à l’échelle d’un cache sous charge, configurez le paramètre maxmemory-reserved afin d’améliorer la réactivité du système. Pour plus d’informations, consultez [Configurer votre paramètre maxmemory-reserved](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting).

## <a name="scaling-clusters"></a>Mise à l’échelle de clusters

Essayez de réduire autant que possible les données dans le cache avant de mettre à l’échelle votre cache en cluster. La réduction des données permet de déplacer des volumes de données plus petits, ce qui réduit le temps nécessaire à l’opération de mise à l’échelle. Pour savoir quel est le meilleur moment pour la mise à l’échelle, consultez [Quand mettre à l’échelle ?](cache-how-to-scale.md#when-to-scale).

## <a name="scale-before-load-is-too-high"></a>Mise à l’échelle avant que la charge ne soit trop élevée

Commencez la mise à l’échelle avant que la charge du serveur ou l’utilisation de la mémoire ne soit trop élevée. Si elle est trop élevée, cela signifie que le serveur Redis est occupé. Le serveur Redis occupé ne dispose pas de suffisamment de ressources pour mettre à l’échelle et redistribuer les données.

## <a name="cache-sizes"></a>Tailles de cache

Si vous utilisez TLS et que vous disposez d’un grand nombre de connexions, envisagez une opération de scale-out afin de pouvoir distribuer la charge sur plus de cœurs. Certaines tailles de cache sont hébergées sur des machines virtuelles à quatre cœurs, voire plus.

Distribuez le chiffrement/déchiffrement TLS et les charges de travail de connexion/déconnexion TLS sur plusieurs cœurs, afin de réduire l’utilisation globale du processeur sur les machines virtuelles du cache. Pour plus d’informations, consultez les [informations sur les cœurs et les tailles de machines virtuelles](./cache-planning-faq.yml#azure-cache-for-redis-performance).
