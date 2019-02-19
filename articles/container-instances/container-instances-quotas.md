---
title: Disponibilité des régions et quotas pour Azure Container Instances
description: La disponibilité des régions et les quotas par défaut du service Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/08/2019
ms.author: danlep
ms.openlocfilehash: 35e846aa5954e3714d301c9c75cf42b31961fdfe
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56160575"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Disponibilité des régions et quotas pour Azure Container Instances

Tous les services Azure incluent certains quotas et limites par défaut pour les ressources et fonctionnalités. Les sections suivantes détaillent les limites de ressources par défaut pour plusieurs ressources Azure Container Instances (ACI), ainsi que la disponibilité du service ACI dans les régions Azure.

## <a name="service-quotas-and-limits"></a>Quotas et limites du service

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Disponibilité des régions

Azure Container Instances est disponible dans les régions suivantes avec les limites de processeur et de mémoire spécifiées. Les valeurs sont à jour au moment de la publication. Pour des informations à jour, utilisez l’API [Liste des fonctionnalités](/rest/api/container-instances/listcapabilities/listcapabilities). Les limites de ressources et la disponibilité sont variables selon que vous utilisez Azure Container Instances avec un [réseau virtuel](container-instances-vnet.md) (préversion) ou avec des [ressources GPU](container-instances-gpu.md) (préversion).

| Lieu | SE | UC | Mémoire (Go) |
| -------- | -- | :---: | :-----------: |
| Canada Centre, USA Centre, USA Est 2 | Linux | 4 | 16 |
| USA Est, Europe Nord, Europe Ouest, USA Ouest, USA Ouest 2 | Linux | 4 | 14 |
| Japon Est | Linux | 2 | 8 |
| Australie Est, Asie Sud-Est | Linux | 2 | 7 |
| Inde Centre, Asie Est, USA Centre Nord, USA Centre Sud, Inde Sud | Linux | 2 | 3,5 |
| USA Est, Europe Ouest, USA Ouest |  Windows | 4 | 14 |
| Australie Est, Canada Centre, Inde Centre, Asie Est, USA Centre, USA Est 2, Japon Est, USA Centre Nord, Europe Nord, USA Centre Sud, Inde Sud, Asie Sud-Est, USA Ouest 2 |  Windows | 2 | 3,5 |

Les instances de conteneur créées dans les limites de ces ressources sont soumises à la disponibilité dans la région de déploiement. Quand une région a une charge importante, vous pouvez rencontrer un échec durant le déploiement des instances. Pour atténuer ce type d’échec de déploiement, essayez de déployer des instances avec des paramètres de mémoire et de processeur inférieurs, ou essayez d’effectuer le déploiement plus tard.

Informez l’équipe des régions supplémentaires nécessaires ou des limites de mémoire/processeur augmentées à l’adresse [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Pour plus d’informations sur la résolution des problèmes de déploiement d’instances de conteneur, consultez [Résoudre les problèmes de déploiement avec Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Étapes suivantes

Certains quotas et limites par défaut peuvent être augmentés. Pour demander une augmentation d’une ou de plusieurs ressources qui peuvent prendre en charge cette augmentation, veuillez envoyer une [demande de support Azure][azure-support] (sélectionnez « Quota » pour **Type de problème**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
