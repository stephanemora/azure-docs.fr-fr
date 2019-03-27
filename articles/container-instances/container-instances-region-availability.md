---
title: Disponibilité des ressources pour Azure Container Instances
description: Disponibilité des ressources de calcul et de mémoire pour le service Azure Container Instances dans différentes régions Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554872"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilité des ressources pour Azure Container Instances dans les régions Azure

Cet article décrit en détail la disponibilité des ressources de calcul et de mémoire pour Azure Container Instances dans les régions Azure. 

Les valeurs présentées sont les ressources maximales disponibles par déploiement d’un [groupe de conteneurs](container-instances-container-groups.md). Les valeurs sont à jour au moment de la publication. Pour des informations à jour, utilisez l’API [Liste des fonctionnalités](/rest/api/container-instances/listcapabilities/listcapabilities). 

> [!NOTE]
> Les groupes de conteneurs créés dans les limites de ces ressources sont soumis à la disponibilité dans la région de déploiement. Quand une région a une charge importante, vous pouvez rencontrer un échec durant le déploiement des instances. Pour atténuer ce type d’échec de déploiement, essayez de déployer des instances avec des paramètres de ressources inférieurs, ou essayez d’effectuer le déploiement plus tard.

Pour plus d’informations sur les quotas et autres limites de vos déploiements, voir [Quotas et limites pour Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilité – Générale

| Lieu | SE | UC | Mémoire (Go) |
| -------- | -- | :---: | :-----------: |
| Canada Centre, USA Centre, USA Est 2, USA Centre Sud | Linux | 4 | 16 |
| USA Est, Europe Nord, Europe Ouest, USA Ouest, USA Ouest 2 | Linux | 4 | 14 |
| Japon Est | Linux | 2 | 8 |
| Australie Est, Asie Sud-Est | Linux | 2 | 7 |
| Inde Centre, Asie Est, USA Centre Nord, USA Centre Sud, Inde Sud | Linux | 2 | 3,5 |
| USA Est, Europe Ouest, USA Ouest | Windows | 4 | 14 |
| Australie Est, Canada Centre, Inde Centre, Asie Est, USA Centre, USA Est 2, Japon Est, USA Centre Nord, Europe Nord, USA Centre Sud, Inde Sud, Asie Sud-Est, USA Ouest 2 | Windows | 2 | 3,5 |

## <a name="availability---virtual-network-deployment-preview"></a>Disponibilité – Déploiement de réseau virtuel (préversion)

Les régions et ressources suivantes sont disponibles pour un groupe de conteneurs déployé dans un [réseau virtuel Azure](container-instances-vnet.md) (préversion)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilité – Ressources GPU (préversion)

Les régions et ressources suivantes sont disponibles pour un groupe de conteneurs déployé avec [Ressources GPU](container-instances-gpu.md) (préversion)

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

Informez l’équipe si vous souhaitez voir des régions supplémentaires ou une disponibilité accrue des ressources sur le site [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Pour plus d’informations sur la résolution des problèmes de déploiement d’instances de conteneur, consultez [Résoudre les problèmes de déploiement avec Azure Container Instances](container-instances-troubleshooting.md).
