---
title: Disponibilité des ressources par région
description: Disponibilité des ressources de calcul et de mémoire pour le service Azure Container Instances dans différentes régions Azure.
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: aef66a9fdbe73ccd4da79ce972b7beb061e9fe35
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533471"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilité des ressources pour Azure Container Instances dans les régions Azure

Cet article décrit en détail la disponibilité des ressources de calcul et de mémoire pour Azure Container Instances dans les régions Azure. 

Les valeurs présentées sont les ressources maximales disponibles par déploiement d’un [groupe de conteneurs](container-instances-container-groups.md). Les valeurs sont à jour au moment de la publication. 

> [!NOTE]
> Les groupes de conteneurs créés dans les limites de ces ressources sont soumis à la disponibilité dans la région de déploiement. Quand une région a une charge importante, vous pouvez rencontrer un échec durant le déploiement des instances. Pour atténuer ce type d’échec de déploiement, essayez de déployer des instances avec des paramètres de ressources inférieurs, ou essayez d’effectuer le déploiement plus tard.

Pour plus d’informations sur les quotas et autres limites de vos déploiements, voir [Quotas et limites pour Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilité – Générale

Les régions et ressources suivantes sont accessibles aux groupes de conteneurs dotés de Linux et de conteneurs Windows Server 2016 [pris en charge](container-instances-faq.md#what-windows-base-os-images-are-supported).

| Location | OS | UC | Mémoire (Go) |
| -------- | -- | :---: | :-----------: |
| Canada Centre, Inde Centre, USA Centre, Asie Est, USA Est, USA Est 2, Europe Nord, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, USA Ouest | Linux | 4 | 16 |
| Europe Ouest, USA Ouest 2 | Linux | 4 | 14 |
| Australie Est, Japon Est | Linux | 2 | 8 |
| USA Centre Nord, Inde Sud | Linux | 2 | 3,5 |
| Europe Ouest | Windows | 4 | 16 |
| USA Est, USA Ouest | Windows | 4 | 14 |
| Australie Est, Canada Centre, Inde Centre, USA Centre, Asie Est, USA Est 2, Japon Est, USA Centre Nord, Europe Nord, USA Centre Sud, Asie Sud-Est, Inde Sud, Royaume-Uni Sud, USA Ouest 2 | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilité - Déploiements Windows Server 2019 LTSC, 1809 (préversion)

Les régions et ressources suivantes sont accessibles aux groupes de conteneurs dotés de conteneurs Windows Server 2019 (préversion).

| Location | OS | UC | Mémoire (Go) |
| -------- | -- | :---: | :-----------: |
| Asie Sud-Est, Europe Nord, Europe Ouest, USA Centre, USA Est, USA Ouest, USA Ouest 2 | Windows | 4 | 16 |
| USA Est 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Disponibilité – Déploiement de réseau virtuel (préversion)

Les régions et ressources suivantes sont accessibles à un groupe de conteneurs déployé dans un [Réseau virtuel Azure](container-instances-vnet.md) (préversion).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilité – Ressources GPU (préversion)

Les régions et ressources suivantes sont accessibles à un groupe de conteneurs déployé avec [Ressources GPU](container-instances-gpu.md) (préversion).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

Informez l’équipe si vous souhaitez voir des régions supplémentaires ou une disponibilité accrue des ressources sur le site [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Pour plus d’informations sur la résolution des problèmes de déploiement d’instances de conteneur, consultez [Résoudre les problèmes de déploiement avec Azure Container Instances](container-instances-troubleshooting.md).
