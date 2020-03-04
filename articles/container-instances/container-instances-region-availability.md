---
title: Disponibilité des ressources par région
description: Disponibilité des ressources de calcul et de mémoire pour le service Azure Container Instances dans différentes régions Azure.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: f429a165fe26cc9fc7aa973231f5a77163feef4a
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525301"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilité des ressources pour Azure Container Instances dans les régions Azure

Cet article décrit en détail la disponibilité des ressources de calcul, de mémoire et de stockage d’Azure Container Instances dans les régions Azure et du système d’exploitation cible. 

Les valeurs présentées sont les ressources maximales disponibles par déploiement d’un [groupe de conteneurs](container-instances-container-groups.md). Les valeurs sont à jour au moment de la publication. 

> [!NOTE]
> Les groupes de conteneurs créés dans les limites de ces ressources sont soumis à la disponibilité dans la région de déploiement. Quand une région a une charge importante, vous pouvez rencontrer un échec durant le déploiement des instances. Pour atténuer ce type d’échec de déploiement, essayez de déployer des instances avec des paramètres de ressources inférieurs, ou essayez d’effectuer le déploiement plus tard.

Pour plus d’informations sur les quotas et autres limites de vos déploiements, voir [Quotas et limites pour Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilité – Générale

Les régions et les ressources maximales suivantes sont accessibles aux groupes de conteneurs Linux et de conteneurs Windows Server 2016 [pris en charge](container-instances-faq.md#what-windows-base-os-images-are-supported).

| Régions | Système d''exploitation | Utilisation maximale du processeur | Mémoire max. (GB) | Stockage (Go) |
| -------- | -- | :---: | :-----------: | :---: |
| Brésil Sud, Canada Centre, Inde Centre, USA Centre, Asie Est, USA Est, USA Est 2, Europe Nord, USA Centre Sud, Asie Sud-Est, Inde du Sud, Royaume-Uni Sud, Europe Ouest, USA Ouest, USA Ouest 2 | Linux | 4 | 16 | 50 |
| Australie Est, Japon Est | Linux | 2 | 8 | 50 |
| Centre-Nord des États-Unis | Linux | 2 | 3,5 | 50 |
| Brésil Sud, Japon Est, Europe Ouest | Windows | 4 | 16 | 20 |
| USA Est, USA Ouest | Windows | 4 | 14 | 20 |
| Australie Est, Canada Centre, Inde Centre, USA Centre, Asie Est, USA Est 2, USA Centre Nord, Europe Nord, USA Centre Sud, Asie Sud-Est, Inde Sud, Royaume-Uni Sud, USA Ouest 2 | Windows | 2 | 3,5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilité - Déploiements Windows Server 2019 LTSC, 1809 (préversion)

Les régions et les ressources maximales suivantes sont accessibles aux groupes de conteneurs Windows Server 2019 (préversion).

| Régions | Système d''exploitation | Utilisation maximale du processeur | Mémoire max. (GB) | Stockage (Go) |
| -------- | -- | :---: | :-----------: | :---: |
| Australie Est, Brésil Sud, Canada Centre, Inde Centre, USA Centre, Asie Est, USA Est, Japon Est, USA Centre Nord, Europe Nord, USA Centre Sud, Asie Sud-Est, Inde Sud, Royaume-Uni Sud, Europe Ouest | Windows | 4 | 16 | 20 |
| USA Est 2, USA Ouest 2 | Windows | 2 | 3,5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Disponibilité – Déploiement de réseau virtuel

Les régions et les ressources maximales suivantes sont accessibles à un groupe de conteneurs déployé dans un [réseau virtuel Azure](container-instances-vnet.md).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilité – Ressources GPU (préversion)

Les régions et les ressources maximales suivantes sont accessibles à un groupe de conteneurs déployé avec [Ressources GPU](container-instances-gpu.md) (préversion).

> [!IMPORTANT]
> Les ressources GPU sont disponibles sur demande uniquement. Vous pouvez demander l’accès aux ressources GPU en ouvrant une [demande de support Azure][azure-support].

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

Informez l’équipe si vous souhaitez voir des régions supplémentaires ou une disponibilité accrue des ressources sur le site [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Pour plus d’informations sur la résolution des problèmes de déploiement d’instances de conteneur, consultez [Résoudre les problèmes de déploiement avec Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest