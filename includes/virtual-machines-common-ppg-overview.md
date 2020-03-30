---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73171103"
---
Le fait de placer les machines virtuelles dans une seule région réduit la distance physique entre les instances. Le fait de les placer dans une zone de disponibilité unique les rapproche également physiquement. Cependant, à mesure que l’empreinte Azure augmente, une seule zone de disponibilité peut s’étendre sur plusieurs centres de données physiques, ce qui peut entraîner une latence réseau qui peut affecter votre application. 

Pour que les machines virtuelles soient aussi proches que possible, avec la latence la plus faible possible, déployez-les dans un groupe de placements de proximité.

Le groupe de placements de proximité est un regroupement logique utilisé pour s’assurer que les ressources de calcul Azure se trouvent proches les unes des autres. Les groupes de placements de proximité sont utiles pour les charges de travail où une latence faible est requise.


- Latence faible entre les machines virtuelles autonomes.
- La latence faible entre les machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques. 
- Latence faible entre des machines virtuelles autonomes, des machines virtuelles dans plusieurs groupes à haute disponibilité ou plusieurs groupes identiques. Vous pouvez disposer de plusieurs ressources de calcul dans un seul groupe de placements pour créer une application multicouche. 
- Latence faible entre plusieurs couches d’application à l’aide de différents types de matériels. Par exemple, l’exécution du serveur principal à l’aide de la série M dans un groupe à haute disponibilité et du serveur frontal sur une instance de la série D dans un groupe identique, dans un seul groupe de placements de proximité.


![Graphique des groupes de placement de proximité](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Utilisation des groupes de placements de proximité 

Un groupe de placement de proximité est un nouveau type de ressource dans Azure. Vous devez en créer un avant de l’utiliser avec d’autres ressources. Une fois créé, il peut être utilisé avec des machines virtuelles, des groupes à haute disponibilité ou des groupes de machines virtuelles identiques. Vous spécifiez un groupe de placement de proximité lors de la création de ressources de calcul fournissant l’ID de groupe de placement de proximité. 

Vous pouvez également déplacer une ressource existante dans un groupe de placement de proximité. Lors du déplacement d’une ressource vers un groupe de placement de proximité, vous devez commencer par arrêter (désallouer) la ressource, car elle pourrait être redéployée dans un autre centre de données de la région afin de satisfaire à la contrainte de colocalisation. 

Dans le cas des groupes à haute disponibilité et de groupes de machines virtuelles identiques, vous devez définir le groupe de placement de proximité au niveau de la ressource plutôt que sur les machines virtuelles individuelles. 

Un groupe de placement de proximité est une contrainte de colocalisation plutôt qu’un mécanisme d’épinglage. Il est épinglé à un centre de données spécifique avec le déploiement de la première ressource à l’utiliser. Une fois que toutes les ressources utilisant le groupe de placement de proximité ont été arrêtées (désallouées) ou supprimées, elles ne sont plus épinglées. Par conséquent, lors de l’utilisation d’un groupe de placement de proximité avec plusieurs séries de machines virtuelles, il est important de spécifier tous les types requis au préalable dans un modèle quand c’est possible, ou de suivre une séquence de déploiement qui améliore vos chances de réussir le déploiement. Si votre déploiement échoue, redémarrez-le déploiement avec la taille de machine virtuelle qui a échoué en tant que première taille à déployer.


## <a name="best-practices"></a>Meilleures pratiques 
- Pour une latence plus faible, utilisez des groupes de placements de proximité avec une mise en réseau accélérée. Pour plus d’informations, consultez les articles [Créer une machine virtuelle Windows avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Créer une machine virtuelle Linux avec mise en réseau accélérée](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Déployez toutes les tailles de machine virtuelle dans un modèle unique. Afin d’éviter tout matériel ne prenant pas en charge toutes les références SKU et toutes les tailles de machines virtuelles dont vous avez besoin, incluez toutes les couches application dans un seul modèle, afin qu’elles soient toutes déployées en même temps.
- Si vous créez un script pour votre déploiement à l’aide de PowerShell, de l’interface CLI ou du kit de développement logiciel (SDK), vous pouvez recevoir une erreur d’allocation `OverconstrainedAllocationRequest`. Dans ce cas, vous devez arrêter/désallouer toutes les machines virtuelles existantes et modifier la séquence dans le script de déploiement pour qu’il commence par la référence SKU/la taille de la machine virtuelle qui a échoué. 
- Lorsque vous réutilisez un groupe de placements existant dans lequel des machines virtuelles ont été supprimées, attendez que la suppression se termine complètement avant d’y ajouter des machines virtuelles.
- Si la latence est votre première priorité, placez les machines virtuelles dans un groupe de placements de proximité et l’ensemble de la solution dans une zone de disponibilité. Toutefois, si la résilience est votre priorité, répartissez vos instances sur plusieurs zones de disponibilité (un seul groupe de placements de proximité ne peut pas s’étendre sur plusieurs zones).