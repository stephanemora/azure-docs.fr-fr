---
title: Vue d’ensemble d’Azure Container Instances
description: Comprendre Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 11/30/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b5f97dac202edd5405c51b75581275166042ac38
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841453"
---
# <a name="azure-container-instances"></a>Azure Container Instances

Les conteneurs deviennent le meilleur moyen pour mettre en package, déployer et gérer des applications cloud. Azure Container Instances propose la façon la plus simple et rapide d’exécuter un conteneur dans Azure, sans avoir à gérer des machines virtuelles et sans avoir à adopter un service de niveau supérieur.

Azure Container Instances est une excellente solution pour les scénarios qui peuvent fonctionner dans des conteneurs isolés, y compris les applications basiques, automatiser des tâches et créer des travaux. Pour les scénarios où vous devez disposer d’orchestration de conteneur complète (détection de service dans plusieurs conteneurs, mise à l’échelle automatique et mises à niveau d’application coordonnées), nous vous recommandons le service [Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Temps de démarrage rapide

Les conteneurs offrent des avantages de démarrage conséquents sur les machines virtuelles. Azure Container Instances peut démarrer des conteneurs dans Azure en quelques secondes, sans avoir à configurer ni gérer des machines virtuelles.

## <a name="public-ip-connectivity-and-dns-name"></a>Connectivité IP publique et nom DNS

Azure Container Instances permet d’exposer vos conteneurs directement sur Internet avec une adresse IP publique et un nom de domaine complet. Lorsque vous créez une instance de conteneur, vous pouvez spécifier une étiquette de nom DNS personnalisée pour que votre application soit accessible à l’emplacement *customlabel*.*azureregion*.azurecontainer.io.

## <a name="hypervisor-level-security"></a>Sécurité au niveau de l’hyperviseur

D’un point de vue historique, les conteneurs ont offert l’isolation de dépendance d’application et la gouvernance des ressources, mais n’ont pas été considérés suffisamment renforcés pour une utilisation de plusieurs locataires hostile. Azure Container Instances garantie que votre application se retrouve aussi isolée dans un conteneur que dans une machine virtuelle.

## <a name="custom-sizes"></a>Tailles personnalisées

Les conteneurs sont généralement optimisés pour n’exécuter qu’une application, mais les besoins précis de ces applications peuvent grandement varier. Azure Container Instances permet une utilisation optimale en autorisant les spécifications exactes des cœurs d’unité centrale et de la mémoire. Vous payez pour ce dont vous avez besoin et vous êtes facturé immédiatement, pour pouvoir ajuster vos dépenses selon vos besoins.

Pour les travaux nécessitant beaucoup de ressources système, comme le machine learning, Azure Container Instances peut programmer des conteneurs Linux pour utiliser des [ressources GPU](container-instances-gpu.md) (préversion) NVIDIA Tesla.

## <a name="persistent-storage"></a>Stockage persistant

Pour récupérer et conserver des états avec Azure Container Instances, nous proposons le [montage direct des partages de fichiers Azure](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Conteneurs Windows et Linux

Azure Container Instances peut programmer des conteneurs Windows et Linux avec la même API. Spécifiez simplement le type de système d’exploitation lorsque vous créez vos [groupes de conteneurs](container-instances-container-groups.md).

Certaines fonctionnalités sont actuellement restreintes aux conteneurs Linux. Nous travaillons actuellement à proposer la parité des fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

Azure Container Instances prend en charge les images Windows basées sur des versions du canal de maintenance à long terme (LTSC). Les versions du canal semi-annuel (SAC) Windows telles que 1709 et 1803 ne sont pas prises en charge.

## <a name="co-scheduled-groups"></a>Groupes co-planifiés

Azure Container Instances prend en charge la planification de [groupes de plusieurs conteneurs](container-instances-container-groups.md) qui partagent un même hôte, réseau local, stockage et cycle de vie. Vous pouvez ainsi combiner votre conteneur d’application principal avec d’autres conteneurs à rôle d’assistance, comme les side-cars de journalisation.

## <a name="virtual-network-deployment-preview"></a>Déploiement de réseau virtuel (préversion)

Actuellement en préversion, cette fonctionnalité d’Azure Container Instances permet le [déploiement d’instances de conteneur dans un réseau virtuel Azure](container-instances-vnet.md). En déployant des instances de conteneur dans un sous-réseau au sein de votre réseau virtuel, elles peuvent communiquer en toute sécurité avec d’autres ressources dans le réseau virtuel, y compris celles qui sont en local (via une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ ExpressRoute](../expressroute/expressroute-introduction.md)).

> [!IMPORTANT]
> Certaines fonctionnalités d’Azure Container Instances sont en préversion et certaines [limitations s’appliquent](container-instances-vnet.md#preview-limitations). Les préversions sont à votre disposition, à la condition d’accepter les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de ces fonctionnalités sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="next-steps"></a>Étapes suivantes

Essayez de déployer un conteneur dans Azure avec une seule commande à l’aide de notre guide de démarrage rapide :

> [!div class="nextstepaction"]
> [Guide de démarrage rapide : créer son premier conteneur dans Azure Container Instances](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
