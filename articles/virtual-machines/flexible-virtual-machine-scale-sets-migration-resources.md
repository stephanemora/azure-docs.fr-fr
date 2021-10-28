---
title: Effectuer la migration de déploiements et de ressources vers des groupes de machines virtuelles identiques dans une orchestration Flexible
description: Découvrez comment effectuer la migration de déploiements et de ressources vers des groupes de machines virtuelles identiques dans une orchestration Flexible.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/14/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: b6cdeff69c1d9a919651d68b937af1c7b328edbe
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257952"
---
# <a name="migrate-deployments-and-resources-to-virtual-machine-scale-sets-in-flexible-orchestration"></a>Effectuer la migration de déploiements et de ressources vers des groupes de machines virtuelles identiques dans une orchestration Flexible 

**S’applique à :** :heavy_check_mark: groupes identiques flexibles

Tout comme les groupes à haute disponibilité, les groupes de machines virtuelles identiques vous permettent de répartir les machines virtuelles entre plusieurs domaines d’erreur. Les groupes de machines virtuelles identiques avec orchestration Flexible vous permettent de combiner la scalabilité des [groupes de machines virtuelles identiques utilisant le mode d’orchestration Uniform](../virtual-machine-scale-sets/overview.md) avec les garanties de disponibilité régionale des [groupes à haute disponibilité](availability-set-overview.md). Cet article aborde certains points relatifs à la migration lorsque vous passez au mode d’orchestration Flexible pour les groupes de machines virtuelles identiques. 

## <a name="update-availability-set-deployments-templates-and-scripts"></a>Mettre à jour les modèles et les scripts de déploiement des groupes à haute disponibilité

Tout d’abord, vous devez créer un groupe de machines virtuelles identiques sans profil de mise à l’échelle automatique à l’aide d’[Azure CLI](flexible-virtual-machine-scale-sets-cli.md), d’[Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) ou d’un [modèle ARM](flexible-virtual-machine-scale-sets-rest-api.md). Le portail Azure permet uniquement de créer un groupe de machines virtuelles identiques avec un profil de mise à l’échelle automatique. Si vous ne souhaitez pas de profil de mise à l’échelle automatique mais souhaitez créer un groupe identique à l’aide du [portail Azure](flexible-virtual-machine-scale-sets-portal.md), vous pouvez définir la capacité initiale sur 0. 
 
Vous devez spécifier le nombre de domaines d’erreur pour le groupe de machines virtuelles identiques. Pour les déploiements régionaux (non zonaux), les groupes de machines virtuelles identiques offrent les mêmes garanties de domaine d’erreur que les groupes à haute disponibilité. Toutefois, vous pouvez effectuer un scale-up allant jusqu’à 1000 instances maximum. Pour les déploiements zonaux où vous répartissez des instances sur plusieurs zones de disponibilité, le nombre de domaines d’erreur doit être défini sur 1.

Les domaines de mise à jour sont désormais dépréciés dans le mode d’orchestration Flexible. La plupart des mises à jour de plateforme ayant des références SKU à usage général sont effectuées avec la fonctionnalité Migration dynamique et ne nécessitent pas le redémarrage de l’instance. Si la maintenance d’une plateforme nécessite le redémarrage des instances, les mises à jour sont appliquées domaine d’erreur par domaine d’erreur.  
    
L’orchestration Flexible pour les groupes de machines virtuelles identiques prend également en charge le déploiement d’instances sur plusieurs zones de disponibilité. Vous pouvez envisager de mettre à jour vos déploiements de machines virtuelles afin de les répartir sur plusieurs zones de disponibilité. 

La dernière étape de ce processus consiste à créer une machine virtuelle. Au lieu de spécifier un groupe à haute disponibilité, spécifiez le groupe de machines virtuelles identiques. Si vous le souhaitez, vous pouvez spécifier la zone de disponibilité ou le domaine d’erreur où la machine virtuelle doit être placée. 


## <a name="migrate-existing-availability-set-vms"></a>Effectuer la migration de machines virtuelles de groupe à haute disponibilité 

Il n’existe actuellement aucun outil automatisé permettant de déplacer directement des instances existantes entre un groupe à haute disponibilité et un groupe de machines virtuelles identiques. Toutefois, il existe plusieurs stratégies permettant d’effectuer la migration d’instances existantes vers un groupe identique Flexible : 

### <a name="bluegreen-or-side-by-side-migration"></a>Migration bleu-vert ou côte à côte 

1. Placez les nouvelles instances de machines virtuelles du groupe identique avec une configuration similaire dans le même groupe de ressources, réseau virtuel, équilibreur de charge, etc. que les machines virtuelles du groupe de disponibilité. 
1. Effectuez la migration des données, du trafic réseau, etc. pour utiliser les nouvelles instances de groupe identique. 
1. Désallouez ou supprimez les machines virtuelles du groupe à haute disponibilité d’origine, en conservant les machines virtuelles du groupe identique qui s’exécutent pour votre application.

### <a name="replace-vm-instances"></a>Remplacer des instances de machine virtuelle 

1. Notez les paramètres de la machine virtuelle que vous souhaitez conserver (nom, ID NIC, système d’exploitation, ID des disques OS et de données, paramètres de configuration de machine virtuelle, emplacement du domaine d’erreur, etc.). 
1. Supprimez la machine virtuelle du groupe à haute disponibilité. Les cartes réseau et les disques de la machine virtuelle ne seront pas supprimés.  
1. Créez un nouvel objet de machine virtuelle à l’aide des paramètres de la machine virtuelle d’origine : 
    - ID NIC 
    - Disques OS et de données 
    - Emplacement du domaine d’erreur 
    - Autres paramètres de la machine virtuelle 


## <a name="update-uniform-scale-sets-deployment-templates-and-scripts"></a>Mettre à jour les scripts et les modèles de déploiement des groupes identiques Uniform

Mettez à jour les scripts et les modèles de déploiement des groupes de machines virtuelles identiques Uniform pour utiliser une orchestration Flexible. Pour cela, vous devez modifier les éléments suivants dans vos modèles : 

- Supprimez `LoadBalancerNATPool` (non valide pour le mode Flex) 
- Supprimez le paramètre de surapprovisionnement (non valide pour le mode Flex) 
- Supprimez `upgradePolicy` (pas encore valide pour le mode Flex) 
- Mettez à jour l’API Compute vers la version **2021-03-01** 
- Ajoutez le mode d’orchestration `flexible` 
- `platformFaultDomainCount` est obligatoire 
- `singlePlacementGroup`=false est obligatoire 
- Ajoutez l’API Réseau version **2021-11-01** ou ultérieure 
- Attribuez la valeur *true* au `configuration.properties.primary` IP (obligatoire pour les règles de trafic sortant)


## <a name="migrate-existing-uniform-scale-sets"></a>Effectuer la migration de groupes identiques Uniform existants 

Il n’existe actuellement aucun outil automatisé permettant de déplacer directement des instances existantes ou de mettre à niveau un groupe identique Uniform vers un groupe de machines virtuelles identiques Flexible. Toutefois, vous pouvez utiliser la stratégie suivante pour effectuer la migration des instances existantes vers un groupe identique Flexible :

### <a name="bluegreen-or-side-by-side-migration"></a>Migration bleu-vert ou côte à côte 

1. Placez le nouveau groupe identique en mode d’orchestration Flexible avec une configuration similaire dans le même groupe de ressources, réseau virtuel, équilibreur de charge, etc. que le groupe identique d’origine en mode d’orchestration Uniform.
1. Effectuez la migration des données, du trafic réseau, etc. pour utiliser les nouvelles instances de groupe identique. 
1. Supprimez ou effectuez un scale-down des machines virtuelles du groupe identique Uniform d’origine, en conservant les machines virtuelles du groupe identique qui s’exécutent pour votre application.


## <a name="flexible-scale-sets-considerations"></a>Considérations relatives aux groupes identiques Flexible 

Les groupes de machines virtuelles identiques avec orchestration Flexible vous permettent de combiner la scalabilité des [groupes de machines virtuelles identiques utilisant le mode d’orchestration Uniform](../virtual-machine-scale-sets/overview.md) avec les garanties de disponibilité régionale des groupes à haute disponibilité. Voici les points clés à prendre en compte lorsque vous décidez d’utiliser le mode d’orchestration Flexible. 

### <a name="explicit-network-outbound-connectivity-required"></a>Connectivité réseau sortante explicite nécessaire 

Pour améliorer la sécurité réseau par défaut, les groupes de machines virtuelles identiques avec une orchestration flexible requièrent que les instances créées implicitement via le profil de mise à l’échelle automatique disposent d’une connectivité sortante définie explicitement à l’aide de l’une des méthodes suivantes : 

- Pour la plupart des scénarios, nous recommandons le service [NAT Gateway attaché au sous-réseau](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md).
- Pour les scénarios assortis d’exigences de sécurité élevées ou lors de l’utilisation d’un Pare-feu Azure ou d’une Appliance virtuelle réseau (NVA), vous pouvez spécifier un Itinéraire personnalisé défini par l’utilisateur en tant que tronçon suivant via le pare-feu. 
- Les instances se trouvent dans le pool principal d’un Équilibreur de charge Azure de Référence SKU standard. 
- Attachez une adresse IP publique à l’interface réseau de l’instance. 

Avec des machines virtuelles à instance unique et des groupes de machines virtuelles identiques avec orchestration uniforme, la connectivité sortante est fournie automatiquement. 

Les scénarios courants nécessitant une connectivité sortante explicite sont les suivants : 

- L’activation d’une machine virtuelle Windows nécessite que vous ayez défini une connectivité sortante à partir de l’instance de machine virtuelle vers le Service de gestion des clés d’activation Windows (KMS). Pour plus d’informations, consultez [Résoudre des problèmes liés à l’activation de machines virtuelles Windows Azure](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems).  
- Accédez aux comptes de stockage ou au Coffre de clés. La connectivité aux services Azure peut également être établie via une [Liaison privée](../private-link/private-link-overview.md). 

Pour plus d’informations sur la définition de connexions sortantes sécurisées, consultez [Accès sortant par défaut dans Azure](../virtual-network/ip-services/default-outbound-access.md).

> [!IMPORTANT]
> Vérifiez que vous disposez d’une connectivité réseau sortante explicite. Consultez [Réseaux virtuels et machines virtuelles dans Azure](../virtual-network/network-overview.md) pour en savoir plus à ce sujet, et veillez à suivre les [bonnes pratiques](../virtual-network/concepts-and-best-practices.md) Azure concernant les réseaux. 


### <a name="assign-fault-domain-during-vm-creation"></a>Attribuer un domaine d’erreur au moment de la création de la machine virtuelle
Vous pouvez choisir le nombre de domaines d’erreur pour le groupe identique avec orchestration Flexible. Par défaut, quand vous ajoutez une machine virtuelle à un groupe identique Flexible, Azure répartit uniformément les instances entre les domaines d’erreur. Il est recommandé de laisser Azure attribuer le domaine d’erreur. Cependant, pour des scénarios avancés ou de dépannage, vous pouvez remplacer ce comportement par défaut et spécifier le domaine d’erreur auquel appartiendra l’instance.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Nommage d’instance
Quand vous créez une machine virtuelle et l’ajoutez à un groupe identique Flexible, vous disposez d’un contrôle total sur les noms d’instance dans le cadre des règles de convention de nommage Azure. Quand des machines virtuelles sont ajoutées automatiquement au groupe identique par le biais d’une mise à l’échelle automatique, vous spécifiez un préfixe et Azure ajoute un numéro unique à la fin du nom. 

### <a name="list-scale-sets-vm-api-changes"></a>Modifications de l’API de machine virtuelle qui liste les instances d’un groupe identique
VMSS (Virtual Machine Scale Sets) vous permet de lister les instances appartenant au groupe identique. Avec l’orchestration Flexible, la commande de machine virtuelle VMSS de liste retourne une liste d’ID de machines virtuelles d’un groupe identique. Vous pouvez ensuite appeler les commandes de machine virtuelle VMSS GET pour obtenir plus d’informations sur la façon dont le groupe identique fonctionne avec l’instance de machine virtuelle. Pour obtenir toutes les informations de la machine virtuelle, utilisez les commandes de machine virtuelle GET standard ou [Azure Resource Graph](../governance/resource-graph/overview.md).


### <a name="query-instances-for-power-state"></a>Instances de requête pour l’état d’alimentation
La méthode recommandée consiste à utiliser Azure Resource Graph pour interroger toutes les machines virtuelles d’un groupe de machines virtuelles identiques. Azure Resource Graph fournit des fonctionnalités de requête efficaces pour les ressources Azure à grande échelle, sur les différents abonnements.

```
| where type =~ 'Microsoft.Compute/virtualMachines'
| where properties.virtualMachineScaleSet contains "demo"
| extend powerState = properties.extended.instanceView.powerState.code
| project name, resourceGroup, location, powerState
| order by resourceGroup desc, name desc
```

L’interrogation des ressources avec [Azure Resource Graph](../governance/resource-graph/overview.md) représente un moyen pratique et efficace d’interroger des ressources Azure et de réduire les appels d’API au fournisseur de ressources. Azure Resource Graph est un cache cohérent à terme. Il se peut que les ressources nouvelles ou mises à jour n’y soient pas reflétées pendant une période allant jusqu’à 60 secondes. Vous pouvez :
- Lister les machines virtuelles d’un groupe de ressources ou d’un abonnement.
- Utiliser l’option de développement pour récupérer la vue d’instance (attribution de domaine d’erreur, états d’alimentation et de provisionnement) pour toutes les machines virtuelles de votre abonnement.
- Utiliser les commandes et l’API de machine virtuelle Get pour obtenir un modèle et une vue d’instance pour une seule instance.


### <a name="scale-sets-vm-batch-operations"></a>Opérations de traitement par lots des machines virtuelles de groupe identique
Utilisez les commandes de machine virtuelle standard pour démarrer, arrêter, redémarrer et supprimer des instances, plutôt que les API de machine virtuelle VMSS. Les opérations de traitement par lots de machine virtuelle VMSS (tout démarrer, tout arrêter, tout réinitialiser, etc.) ne sont pas utilisées avec le mode d’orchestration Flexible.


### <a name="monitor-application-health"></a>Superviser l’intégrité de l’application
Avec la supervision du fonctionnement de l’application, votre application fournit à Azure une pulsation pour déterminer si votre application est saine ou non. Azure peut remplacer automatiquement les instances de machine virtuelle non saines. Pour les instances de groupe identique Flexibles, vous devez installer et configurer l’extension Intégrité de l’application sur la machine virtuelle. Pour les instances de groupe identique Uniform, vous pouvez utiliser l’extension Intégrité de l’application ou mesurer l’intégrité avec une sonde d’intégrité personnalisée Azure Load Balancer.


### <a name="retrieve-boot-diagnostics-data"></a>Récupérer les données de diagnostics de démarrage
Utilisez les API et commandes de machine virtuelle standard pour récupérer les captures d’écran et données de diagnostic de démarrage d’instance. Les commandes et API de diagnostic de démarrage de machine virtuelle VMSS ne sont pas utilisées avec les instances en mode d’orchestration Flexible.


### <a name="vm-extensions"></a>Extensions de machine virtuelle
Utilisez les extensions ciblant les machines virtuelles standard plutôt que celles ciblant les instances en mode d’orchestration Uniform.
















## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Découvrez les différences que présentent les modes d’orchestration Uniform et Flexible au niveau de l’API.](../virtual-machine-scale-sets/orchestration-modes-api-comparison.md)