---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep
ms.custom: include file
ms.openlocfilehash: c12fff63cdb7241d89e7511a3dac2ff9c1363ae6
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49346102"
---
## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement
* **Abonnement Azure** : pour déployer un plus grand nombre d’instances de calcul intensif, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

* **Tarification et disponibilité** : ces tailles de machines virtuelles sont proposées uniquement au niveau tarifaire Standard. Pour connaître la disponibilité dans les différentes régions Azure, voir [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/) . 
* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs dans votre abonnement Azure à partir de la valeur par défaut. Votre abonnement peut également limiter le nombre de cœurs, que vous pouvez déployer dans certaines familles de taille de machine virtuelle, dont la série H. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitement. (Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.)
  
  > [!NOTE]
  > Si vous avez des besoins de capacité à grande échelle, contactez le support Azure. Les quotas d’Azure sont des limites de crédit et non des garanties de capacité. Quel que soit votre quota, vous êtes facturé uniquement pour les cœurs que vous utilisez.
  > 
  > 
* **Réseau virtuel** : un [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) Azure n’est pas requis pour utiliser les instances qui nécessitent beaucoup de ressources système. Cependant, pour bon nombre de scénarios de déploiement, vous avez besoin d’au moins un réseau virtuel Azure cloud ou d’une connexion de site à site si vous devez accéder à des ressources locales. Si nécessaire, créez un réseau virtuel avant de déployer les instances. L’ajout de machines virtuelles nécessitant beaucoup de ressources système à un réseau virtuel dans un groupe d’affinités n’est pas pris en charge.
* **Redimensionnement** : en raison de leur matériel spécialisé, vous pouvez uniquement redimensionner les instances nécessitant beaucoup de ressources système qui appartiennent à la même famille de taille (série H ou série A nécessitant beaucoup de ressources système). Par exemple, vous pouvez redimensionner une machine virtuelle de la série H uniquement d’une seule taille en une autre de cette même série. Le redimensionnement d’une taille ne nécessitant pas beaucoup de ressources système en une taille nécessitant beaucoup de ressources système n’est pas pris en charge.  

## <a name="rdma-capable-instances"></a>Instances prenant en charge RDMA
Un sous-ensemble d’instances nécessitant beaucoup de ressources système (H16r, H16mr, A8 et A9) offre une interface réseau pour la connectivité par accès direct à la mémoire à distance (RDMA). Les tailles sélectionnées de la série N désignées par « r », telles que NC24r, prennent également en charge l’accès RDMA. Cette interface s’ajoute à l’interface réseau Azure standard disponible pour d’autres tailles de machine virtuelle. 
  
Cette interface permet aux instances prenant en charge l’accès RDMA de communiquer sur un réseau InfiniBand (IB), opérant à des vitesses FDR pour les machines virtuelles H16r, H16mr et celles de la série N compatibles RDMA, et à des vitesses QDR pour les machines virtuelles A8 et A9. Ces fonctionnalités RDMA peuvent améliorer l’extensibilité et les performances de certaines applications MPI (Message Passing Interface).

> [!NOTE]
> Dans Azure, IP over IB n’est pas pris en charge. Seul RDMA over IB est pris en charge.
>

