---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850666"
---
Le fait de placer les machines virtuelles dans une seule région réduit la distance physique entre les instances. Le fait de les placer dans une zone de disponibilité unique les rapproche également physiquement. Cependant, à mesure que l’empreinte Azure augmente, une seule zone de disponibilité peut s’étendre sur plusieurs centres de données physiques, ce qui peut entraîner une latence réseau qui peut affecter votre application. 

Pour que les machines virtuelles soient aussi proches que possible, avec la latence la plus faible possible, déployez-les dans un groupe de placements de proximité.

Le groupe de placements de proximité est un regroupement logique utilisé pour s’assurer que les ressources de calcul Azure se trouvent proches les unes des autres. Les groupes de placements de proximité sont utiles pour les charges de travail où une latence faible est requise.


- Latence faible entre les machines virtuelles autonomes.
- La latence faible entre les machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques. 
- Latence faible entre des machines virtuelles autonomes, des machines virtuelles dans plusieurs groupes à haute disponibilité ou plusieurs groupes identiques. Vous pouvez disposer de plusieurs ressources de calcul dans un seul groupe de placements pour créer une application multicouche. 
- Latence faible entre plusieurs couches d’application à l’aide de différents types de matériels. Par exemple, l’exécution du serveur principal à l’aide de la série M dans un groupe à haute disponibilité et du serveur frontal sur une instance de la série D dans un groupe identique, dans un seul groupe de placements de proximité.

> [!IMPORTANT]
> Les groupes de placements de proximité sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Les groupes de placements de proximité ne sont pas disponibles dans ces régions en préversion : **Japon Est**, **Australie Est** et **Inde Centre**.


## <a name="best-practices"></a>Bonnes pratiques 
- Pour une latence plus faible, utilisez des groupes de placements de proximité avec une mise en réseau accélérée. Pour plus d’informations, consultez les articles [Créer une machine virtuelle Windows avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Créer une machine virtuelle Linux avec mise en réseau accélérée](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Déployez toutes les tailles de machine virtuelle dans un seul modèle Resource Manager. Afin d’éviter tout matériel ne prenant pas en charge toutes les références SKU et toutes les tailles de machines virtuelles dont vous avez besoin, incluez toutes les couches application dans un seul modèle, afin qu’elles soient toutes déployées en même temps.
- Si vous créez un script pour votre déploiement à l’aide de PowerShell, de l’interface CLI ou du kit de développement logiciel (SDK), vous pouvez recevoir une erreur d’allocation `OverconstrainedAllocationRequest`. Dans ce cas, vous devez arrêter/désallouer toutes les machines virtuelles existantes et modifier la séquence dans le script de déploiement pour qu’il commence par la référence SKU/la taille de la machine virtuelle qui a échoué. 
- Lorsque vous réutilisez un groupe de placements existant dans lequel des machines virtuelles ont été supprimées, attendez que la suppression se termine complètement avant d’y ajouter des machines virtuelles.
- Si la latence est votre première priorité, placez les machines virtuelles dans un groupe de placements de proximité et l’ensemble de la solution dans une zone de disponibilité. Toutefois, si la résilience est votre priorité, répartissez vos instances sur plusieurs zones de disponibilité (un seul groupe de placements de proximité ne peut pas s’étendre sur plusieurs zones).