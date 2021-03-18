---
title: Groupes de placements de proximité
description: Découvrez l’utilisation des groupes de placement de proximité dans Azure.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: conceptual
ms.date: 3/07/2021
ms.reviewer: zivr
ms.openlocfilehash: 1a65a1e4ecd989f3a7c4968c424472c3c6dfe472
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559072"
---
# <a name="proximity-placement-groups"></a>Groupes de placements de proximité

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

Vous pouvez également déplacer une ressource existante dans un groupe de placement de proximité. Lors du déplacement d’une ressource vers un groupe de placement de proximité, vous devez commencer par arrêter (libérer) la ressource, car elle pourrait être redéployée dans un autre centre de données de la région afin de satisfaire à la contrainte de colocalisation. 

Dans le cas des groupes à haute disponibilité et de groupes de machines virtuelles identiques, vous devez définir le groupe de placement de proximité au niveau de la ressource plutôt que sur les machines virtuelles individuelles. 

Un groupe de placement de proximité constitue une contrainte de colocalisation plutôt qu’un mécanisme d’épinglage. Il est épinglé à un centre de données spécifique avec le déploiement de la première ressource à l’utiliser. Une fois que toutes les ressources utilisant le groupe de placement de proximité ont été arrêtées (désallouées) ou supprimées, elles ne sont plus épinglées. Par conséquent, lors de l’utilisation d’un groupe de placement de proximité avec plusieurs séries de machines virtuelles, il est important de spécifier tous les types requis au préalable dans un modèle quand c’est possible, ou de suivre une séquence de déploiement qui améliore vos chances de réussir le déploiement. Si votre déploiement échoue, redémarrez-le déploiement avec la taille de machine virtuelle qui a échoué en tant que première taille à déployer.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Ce qui se passe quand vous utilisez des groupes de placement de proximité 
Les groupes de placement de proximité offrent une colocalisation dans le même centre de données. Toutefois, du fait que les groupes de placement de proximité constituent une contrainte de déploiement supplémentaire, des échecs d’allocation peuvent se produire. Dans certains cas d’utilisation, des échecs d’allocation se produisent lors de l’utilisation de groupes de placement de proximité :

- Lorsque vous demandez la première machine virtuelle dans le groupe de placement de proximité, le centre de données est automatiquement sélectionné. Dans certains cas, une deuxième demande de référence (SKU) de machine virtuelle différente peut échouer si celle-ci n’existe pas dans ce centre de données. Dans ce cas, une erreur **OverconstrainedAllocationRequest** est retournée. Pour éviter cela, essayez de modifier l’ordre dans lequel vous déployez vos références (SKU) ou faites en sorte que les deux ressources soient déployées à l’aide d’un seul modèle ARM.
-   Dans le cas de charges de travail élastiques où vous ajoutez et supprimez des instances de machine virtuelle, l’existence d’une contrainte de groupe de placement de proximité sur votre déploiement peut entraîner un échec de satisfaction de la demande générant l’erreur **AllocationFailure**. 
- L’arrêt (désallocation) et le démarrage de vos machines virtuelles en fonction des besoins sont une autre façon d’obtenir l’élasticité. Étant donné que la capacité n’est pas conservée une fois que vous arrêtez (désallouez) une machine virtuelle, le redémarrage de celle-ci peut générer une erreur **AllocationFailure**.

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Maintenance planifiée et groupes de placement de proximité

Les événements de maintenance planifiée, tels que la désaffectation de matériel dans un centre de ressources Azure, sont susceptibles d’affecter l’alignement des ressources dans des groupes de placement de proximité. Les ressources peuvent être déplacées vers un autre centre de données, ce qui perturbe les attentes en matière de colocation et de latence associées au groupe de placement de proximité.

### <a name="check-the-alignment-status"></a>Vérifier l’état de l’alignement

Vous pouvez procéder comme suit pour vérifier l’état d’alignement de vos groupes de placement de proximité.


- L’état de colocation du groupe de placement de proximité peut être affiché à l’aide du portail, de l’interface CLI et de PowerShell.

    -   Dans PowerShell, l’état de colocalisation peut être obtenu avec la cmdlet Get-AzProximityPlacementGroup en ajoutant le paramètre facultatif « -ColocationStatus ».

    -   Sur l’interface CLI, l’état de colocalisation peut être obtenu avec `az ppg show` en ajoutant le paramètre facultatif « --include-colocation-status ».

- Pour chaque groupe de placement de proximité, une propriété **état de colocation** fournit le résumé de l’état d’alignement actuel des ressources groupées. 

    - **Aligned** : la ressource se trouve dans la même enveloppe de latence du groupe de placement de proximité.

    - **Unknown** : au moins l’une des ressources de machines virtuelles ont été libérées. Une fois que celles-ci ont été redémarrées, l’état doit revenir à **Aligned**.

    - **Not Aligned** : au moins une ressource n'est pas alignée avec le groupe de placement de proximité. Les ressources spécifiques qui ne sont pas alignées sont également appelées séparément dans la section liée à l’appartenance.

- Pour les groupes à haute disponibilité, vous pouvez voir des informations sur l’alignement des machines virtuelles individuelles sur la page Vue d’ensemble du groupe à haute disponibilité.

- Pour les groupes identiques, vous pouvez consulter les informations relatives à l’alignement des instances individuelles dans l’onglet **Instances** de la page **Vue d’ensemble** du groupe identique. 


### <a name="re-align-resources"></a>Réaligner les ressources 

Si un groupe de placement de proximité est `Not Aligned`, vous pouvez arrêter/libérer, puis redémarrer les ressources affectées. Si la machine virtuelle se trouve dans un groupe à haute disponibilité ou un groupe identique, toutes les machines virtuelles dans le groupe à haute disponibilité ou le groupe identique doivent être arrêtées/libérées avant leur redémarrage.

En cas d’échec d’affectation en raison de contraintes de déploiement, il se peut que vous deviez d’abord arrêter/libérer toutes les ressources du groupe de placement de proximité affecté (y compris les ressources alignées), puis les redémarrer pour restaurer l’alignement.

## <a name="best-practices"></a>Meilleures pratiques 
- Pour une latence plus faible, utilisez des groupes de placements de proximité avec une mise en réseau accélérée. Pour plus d’informations, consultez les articles [Créer une machine virtuelle Windows avec mise en réseau accélérée](../virtual-network/create-vm-accelerated-networking-cli.md) ou [Créer une machine virtuelle Linux avec mise en réseau accélérée](../virtual-network/create-vm-accelerated-networking-powershell.md).
- Déployez toutes les tailles de machine virtuelle dans un modèle unique. Afin d’éviter tout matériel ne prenant pas en charge toutes les références SKU et toutes les tailles de machines virtuelles dont vous avez besoin, incluez toutes les couches application dans un seul modèle, afin qu’elles soient toutes déployées en même temps.
- Si vous créez un script pour votre déploiement à l’aide de PowerShell, de l’interface CLI ou du kit de développement logiciel (SDK), vous pouvez recevoir une erreur d’allocation `OverconstrainedAllocationRequest`. Dans ce cas, vous devez arrêter/désallouer toutes les machines virtuelles existantes et modifier la séquence dans le script de déploiement pour qu’il commence par la référence SKU/la taille de la machine virtuelle qui a échoué. 
- Lorsque vous réutilisez un groupe de placements existant dans lequel des machines virtuelles ont été supprimées, attendez que la suppression se termine complètement avant d’y ajouter des machines virtuelles.
- Si la latence est votre première priorité, placez les machines virtuelles dans un groupe de placements de proximité et l’ensemble de la solution dans une zone de disponibilité. Toutefois, si la résilience est votre priorité, répartissez vos instances sur plusieurs zones de disponibilité (un seul groupe de placements de proximité ne peut pas s’étendre sur plusieurs zones).

## <a name="next-steps"></a>Étapes suivantes

Déployez une machine virtuelle dans un groupe de placement de proximité avec [Azure CLI](./linux/proximity-placement-groups.md) ou [PowerShell](./windows/proximity-placement-groups.md).

Découvrez comment [tester le temps de réponse du réseau](../virtual-network/virtual-network-test-latency.md).

Découvrez comment [optimiser le débit du réseau](../virtual-network/virtual-network-optimize-network-bandwidth.md).  

Découvrez comment [utiliser des groupes de placement de proximité avec des applications SAP](./workloads/sap/sap-proximity-placement-scenarios.md).