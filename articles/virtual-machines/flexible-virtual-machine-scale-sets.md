---
title: Orchestration flexible pour des groupes de machines virtuelles identiques dans Azure
description: Découvrez comment utiliser un mode d’orchestration flexible pour des groupes de machines virtuelles identiques dans Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/11/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: dc687c2f3d14c2da02fa3ce5b3a3357292977771
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648959"
---
# <a name="preview-flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>Préversion : Orchestration flexible pour des groupes de machines virtuelles identiques

**S’applique à :** :heavy_check_mark: groupes identiques flexibles

Des groupes de machines virtuelles identiques avec une orchestration flexible vous permettent de combiner la scalabilité des [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/overview.md) avec les garanties de disponibilité régionale des [groupes à haute disponibilité](availability-set-overview.md).

Les groupes identiques de machines virtuelles Azure vous permettent de créer et de gérer un groupe de machines virtuelles et disposant d’une charge équilibrée. Le nombre d’instances de machine virtuelle peut augmenter ou diminuer automatiquement en fonction d’une demande ou d’un calendrier défini. Les groupes identiques vous offrent les avantages suivants :
- Création et gestion faciles de plusieurs machines virtuelles
- Offre une haute disponibilité et une résilience d’application en répartissant les machines virtuelles entre les domaines d’erreur
- Permet une mise à l’échelle automatique de votre application en fonction des variations du besoin en ressources
- Fonctionne à grande échelle

Avec l’orchestration Flexible, Azure offre une expérience unifiée sur tout l’écosystème de machines virtuelles Azure. L’orchestration Flexible garantit une haute disponibilité (jusqu’à 1 000 machines virtuelles) en répartissant les machines virtuelles entre différents domaines d’erreur dans une région ou dans une zone de disponibilité. Vous pouvez ainsi effectuer un scale-out de votre application tout en conservant l’isolation du domaine d’erreur, essentielle pour exécuter des charges de travail avec état ou basées sur un quorum, y compris :
- Charges de travail basées sur un quorum
- Bases de données open source
- Applications avec état
- Services nécessitant une haute disponibilité et une grande échelle
- Services cherchant à combiner différents types de machines virtuelles ou à tirer parti à la fois de machines virtuelles spot et à la demande
- Applications d’un groupe à haute disponibilité existant

Apprenez-en davantage sur les différences entre les groupes identiques uniformes et les groupes identiques flexibles dans [Modes d’orchestration](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md).


> [!IMPORTANT]
> Les groupes de machines virtuelles identiques en mode d’orchestration Flexible sont actuellement en préversion publique. Une procédure de consentement est requise pour utiliser la fonctionnalité en préversion publique décrite ci-dessous.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="register-for-flexible-orchestration-mode"></a>S’inscrire au mode d’orchestration Flexible
Avant de pouvoir déployer des groupes de machines virtuelles identiques en mode d’orchestration Flexible, vous devez inscrire la fonctionnalité d’évaluation pour votre abonnement. L’inscription peut prendre plusieurs minutes. Vous pouvez utiliser le portail Azure, Azure PowerShell ou Azure CLI pour l’inscription.

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
1. Accédez à vos **Abonnements**.
1. Accédez à la page des informations de l’abonnement pour lequel vous souhaitez créer un groupe identique en mode d’orchestration Flexible en sélectionnant le nom de l’abonnement.
1. Dans le menu, sous **Paramètres**, sélectionnez **Fonctionnalités en préversion**.
1. Sélectionnez les quatre fonctionnalités d’orchestrateur à activer : *VMOrchestratorSingleFD*, *VMOrchestratorMultiFD*, *VMScaleSetFlexPreview* et *SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*.
1. Sélectionnez **Inscription**.

Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul. 

1. Dans le menu, sous **Paramètres**, sélectionnez **Fournisseurs de ressources**.
1. Sélectionnez `Microsoft.compute`.
1. Sélectionnez **Ré-inscrire**.


### <a name="azure-powershell"></a>Azure PowerShell
Utilisez l’applet de commande [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) pour activer la préversion pour votre abonnement.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMScaleSetFlexPreview -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview -ProviderNamespace Microsoft.Compute
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute
```

Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Utilisez [az feature register](/cli/azure/feature#az_feature_register) pour activer la préversion pour votre abonnement.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```

Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="get-started-with-flexible-orchestration-mode"></a>Démarrer avec le mode d’orchestration Flexible

Commencez à utiliser le mode d’orchestration flexible pour vos groupes identiques via le [portail Azure](flexible-virtual-machine-scale-sets-portal.md), [Azure CLI](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) ou un [modèle ARM](flexible-virtual-machine-scale-sets-rest-api.md). 


## <a name="add-instances-with-autoscaling-or-manually"></a>Ajouter des instances avec mise à l’échelle automatique ou manuellement
Les groupes de machines virtuelles identiques avec une orchestration flexible fonctionnent comme une fine couche d’orchestration pour gérer plusieurs machines virtuelles. Il existe plusieurs façons d’ajouter des machines virtuelles à gérer par le groupe identique :

- **Définir le nombre d’instances**

    Lorsque vous créez le groupe identique avec une orchestration flexible, définissez un profil de machine virtuelle ou un modèle à utiliser pour effectuer un scale-out. Vous pouvez ensuite définir le paramètre de capacité afin d’augmenter ou de réduire le nombre d’instances de machine virtuelle gérées par le groupe identique. 

- **Mise à l’échelle automatique à l’aide de métriques ou d’une planification** 

    Vous pouvez également configurer des règles de mise à l’échelle automatique afin d’augmenter ou de réduire la capacité en fonction de métriques ou d’une planification. Consultez [Groupes de machines virtuelles identiques avec mise à l’échelle automatique](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md). 

- **Spécifier un groupe identique lors de la création d’une machine virtuelle**

    Lorsque vous créez une machine virtuelle, vous pouvez spécifier qu’elle est ajoutée à un groupe de machines virtuelles identiques. Une machine virtuelle ne peut être ajoutée à un groupe identique qu’au moment de la création de la machine virtuelle.

Le mode d’orchestration flexible peut être utilisé avec les références SKU de machine virtuelle qui prennent en charge les [mises à jour de préservation de la mémoire ou la migration dynamique](../virtual-machines/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), qui comprend 90 % de toutes les machines virtuelles IaaS déployées dans Azure. Globalement, cela comprend des familles de tailles à usage général, comme les machines virtuelles des séries B, D, E et F. Actuellement, le mode flexible ne peut pas orchestrer des références SKU ou familles de machines virtuelles qui ne prennent pas en charge les mises à jour de préservation de la mémoire, notamment des machines virtuelles des séries G, H, L, M et N. Vous pouvez utiliser l’[API des références SKU de ressources de calcul](/rest/api/compute/resource-skus/list) pour déterminer si une référence SKU de machine virtuelle spécifique est prise en charge.

```azurecli-interactive
az vm list-skus -l eastus --size standard_d2s_v3 --query "[].capabilities[].[name, value]" -o table
```

## <a name="explicit-network-outbound-connectivity-required"></a>Connectivité réseau sortante explicite requise 

Pour améliorer la sécurité réseau par défaut, les groupes de machines virtuelles identiques avec une orchestration flexible requièrent que les instances créées implicitement via le profil de mise à l’échelle automatique disposent d’une connectivité sortante définie explicitement à l’aide de l’une des méthodes suivantes : 

- Pour la plupart des scénarios, nous recommandons le service [NAT Gateway attaché au sous-réseau](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md).
- Pour les scénarios assortis d’exigences de sécurité élevées ou lors de l’utilisation d’un Pare-feu Azure ou d’une Appliance virtuelle réseau (NVA), vous pouvez spécifier un Itinéraire personnalisé défini par l’utilisateur en tant que tronçon suivant via le pare-feu. 
- Les instances se trouvent dans le pool principal d’un Équilibreur de charge Azure de Référence SKU standard. 
- Attachez une adresse IP publique à l’interface réseau de l’instance. 

Avec des machines virtuelles à instance unique et des groupes de machines virtuelles identiques avec orchestration uniforme, la connectivité sortante est fournie automatiquement. 

Les scénarios courants nécessitant une connectivité sortante explicite sont les suivants : 

- L’activation d’une machine virtuelle Windows nécessite que vous ayez défini une connectivité sortante à partir de l’instance de machine virtuelle vers le Service de gestion des clés d’activation Windows (KMS). Pour plus d’informations, consultez [Résoudre des problèmes liés à l’activation de machines virtuelles Windows Azure](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems).  
- Accédez aux comptes de stockage ou au Coffre de clés. La connectivité aux services Azure peut également être établie via une [Liaison privée](../private-link/private-link-overview.md). 

Pour plus d’informations sur la définition de connexions sortantes sécurisées, consultez [Accès sortant par défaut dans Azure](https://aka.ms/defaultoutboundaccess).


## <a name="assign-fault-domain-during-vm-creation"></a>Attribuer un domaine d’erreur au moment de la création de la machine virtuelle
Vous pouvez choisir le nombre de domaines d’erreur pour le groupe identique avec orchestration Flexible. Par défaut, quand vous ajoutez une machine virtuelle à un groupe identique Flexible, Azure répartit uniformément les instances entre les domaines d’erreur. Il est recommandé de laisser Azure attribuer le domaine d’erreur. Cependant, pour des scénarios avancés ou de dépannage, vous pouvez remplacer ce comportement par défaut et spécifier le domaine d’erreur auquel appartiendra l’instance.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

## <a name="instance-naming"></a>Nommage d’instance
Quand vous créez une machine virtuelle et l’ajoutez à un groupe identique Flexible, vous disposez d’un contrôle total sur les noms d’instance dans le cadre des règles de convention de nommage Azure. Quand des machines virtuelles sont ajoutées automatiquement au groupe identique par le biais d’une mise à l’échelle automatique, vous spécifiez un préfixe et Azure ajoute un numéro unique à la fin du nom. 

## <a name="list-scale-sets-vm-api-changes"></a>Modifications de l’API de machine virtuelle qui liste les instances d’un groupe identique
VMSS (Virtual Machine Scale Sets) vous permet de lister les instances appartenant au groupe identique. Avec l’orchestration Flexible, la commande de machine virtuelle VMSS de liste retourne une liste d’ID de machines virtuelles d’un groupe identique. Vous pouvez ensuite appeler les commandes de machine virtuelle VMSS GET pour obtenir plus d’informations sur la façon dont le groupe identique fonctionne avec l’instance de machine virtuelle. Pour obtenir toutes les informations de la machine virtuelle, utilisez les commandes de machine virtuelle GET standard ou [Azure Resource Graph](../governance/resource-graph/overview.md).


## <a name="query-instances-for-power-state"></a>Instances de requête pour l’état d’alimentation
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


## <a name="scale-sets-vm-batch-operations"></a>Opérations de traitement par lots de machines virtuelles de groupe identique
Utilisez les commandes de machine virtuelle standard pour démarrer, arrêter, redémarrer et supprimer des instances, plutôt que les API de machine virtuelle VMSS. Les opérations de traitement par lots de machine virtuelle VMSS (tout démarrer, tout arrêter, tout réinitialiser, etc.) ne sont pas utilisées avec le mode d’orchestration Flexible.


## <a name="monitor-application-health"></a>Superviser l’intégrité de l’application
Avec la supervision du fonctionnement de l’application, votre application fournit à Azure une pulsation pour déterminer si votre application est saine ou non. Azure peut remplacer automatiquement les instances de machine virtuelle non saines. Pour les instances de groupe identique Flexibles, vous devez installer et configurer l’extension Intégrité de l’application sur la machine virtuelle. Pour les instances de groupe identique Uniform, vous pouvez utiliser l’extension Intégrité de l’application ou mesurer l’intégrité avec une sonde d’intégrité personnalisée Azure Load Balancer.


## <a name="retrieve-boot-diagnostics-data"></a>Récupérer les données de diagnostics de démarrage
Utilisez les API et commandes de machine virtuelle standard pour récupérer les captures d’écran et données de diagnostic de démarrage d’instance. Les commandes et API de diagnostic de démarrage de machine virtuelle VMSS ne sont pas utilisées avec les instances en mode d’orchestration Flexible.


## <a name="vm-extensions"></a>Extensions de machine virtuelle
Utilisez les extensions ciblant les machines virtuelles standard plutôt que celles ciblant les instances en mode d’orchestration Uniform.


## <a name="features"></a>Fonctionnalités
Le tableau suivant répertorie les fonctionnalités du mode d’orchestration flexible et les liens vers la documentation appropriée.

| Fonctionnalité | Prise en charge par l’orchestration Flexible (préversion) |
|-|-|
| Type de machine virtuelle | Machine virtuelle Azure IaaS standard (Microsoft.compute /virtualmachines) |
| Nombre maximal d’instances (avec garantie de disponibilité de domaine d’erreur) | 1 000 |
| Références SKU prises en charge | Série D, Série E, Série F, Série A, Série B, Intel, AMD |
| Zones de disponibilité | Spécification facultative de toutes les instances appartenant à une même zone de disponibilité |
| Domaine d’erreur – Diffusion maximale (Azure va diffuser les instances au maximum) | Oui |
| Domaine d’erreur – Diffusion fixe | 2 ou 3 domaines d’erreur (en fonction du nombre maximal de domaines d’erreur par région), 1 domaine d’erreur pour les déploiements zonaux |
| Mettre à jour les domaines | Déconseillé, maintenance de plateforme effectuée domaine par domaine |
| Contrat SLA de disponibilité | Aucun (pendant la préversion) |
| Contrôle total sur la machine virtuelle, les cartes réseau et les disques | Oui |
| Attribuer une machine virtuelle à un domaine d’erreur spécifique | Oui |
| Mise en réseau accélérée | Non (pendant la préversion) |
| Mise à jour corrective de sécurité intégrée (« in-guest ») | Oui |
| Instances spot et tarifs  | Oui, vous pouvez avoir des instances spot et des instances à priorité normale |
| Différents systèmes d’exploitation | Oui, Linux et Windows peuvent résider dans le même groupe identique Flexible |
| Supervision de l’intégrité de l’application | Extension Intégrité de l’application |
| Disques UltraSSD  | Oui |
| Groupes de placement de proximité  | Oui, lisez la [Documentation sur les groupes de placement de proximité](../virtual-machine-scale-sets/proximity-placement-groups.md) |
| Azure Load Balancer - SKU Standard | Oui |
| Liste des machines virtuelles d’un groupe | Oui |
| Sauvegarde Azure | Oui |
| Notifications d’arrêt (groupes de machines virtuelles identiques) | Oui, lisez la [Documentation sur les notifications d’arrêt](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md) |
| Réparation d’instance (groupes de machines virtuelles identiques) | Oui, lisez la [documentation sur la réparation d’instance](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md) |
| Mise à l’échelle automatique | Oui |
| Supprimer les cartes réseau et les disques lors de la suppression d’instances de machine virtuelle | Oui |
| Stratégie de mise à niveau (groupes de machines virtuelles identiques) | Non |
| Mises à jour automatiques du système d’exploitation basées sur une image | Non |
| Infiniband  | Non |
| Accélérateur d’écriture  | Non |
| Hôtes dédiés Azure  | Non |
| Équilibreur de charge logiciel De base  | Non |
| Application Gateway | Oui |
| Contrôle de la maintenance  | Non |
| Alertes Azure | Non |
| Insights de machine virtuelle | Non |
| Azure Site Recovery |  Oui, via PowerShell |
| Service Fabric | Non |
| Azure Kubernetes Service (AKS) | Non |
| Machines virtuelles approuvées (préversion) | Oui |
| Mises à jour d'extensions automatiques | Oui |


### <a name="unsupported-parameters"></a>Paramètres non pris en charge

Les paramètres de groupe de machines virtuelles identiques suivants ne sont pas pris en charge pendant la préversion publique de l’orchestration de groupe de machines virtuelles identiques :
- Groupe de placement unique : vous devez choisir `singlePlacementGroup=False`.
- Déploiement multi-zone : les déploiements peuvent être régionaux ou toutes les machines virtuelles d’une même zone
- Déploiement à l’aide de références (SKU) de spécialité : familles de machines virtuelles des séries G, H, L, M, N
- Surapprovisionnement de VMSS
- Mises à niveau automatiques du système d’exploitation basées sur une image
- Intégrité de l’application via une sonde d’intégrité SLB : utiliser l’extension d’intégrité d’application sur les instances
- Stratégie de mise à niveau de groupe de machines virtuelles identiques : doit être null ou vide
- Déploiement sur Azure Dedicated Host
- Disques non managés
- Stratégie de scale-in de groupe de machines virtuelles identiques
- Protection d’instance de groupe de machines virtuelles identiques
- Mise en réseau accélérée
- Basic Load Balancer
- Réacheminement de port via un pool NAT Standard Load Balancer : vous pouvez configurer des règles NAT sur des instances spécifiques


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Résoudre les problèmes de groupes identiques avec orchestration Flexible
Trouvez la solution adaptée à votre scénario de dépannage.

<!-- error -->
### <a name="invalidparameter-parameter-virtualmachineprofile-is-not-allowed"></a>InvalidParameter. Le paramètre « virtualMachineProfile » n’est pas autorisé.

```
InvalidParameter. Parameter 'virtualMachineProfile' is not allowed.
```

**Cause :** L’abonnement n’est pas inscrit à la préversion publique du mode d’orchestration Flexible.

**Solution :** Vous devez inscrire la fonctionnalité VMScaleSetPublicPreview pour votre abonnement. Suivez les instructions ci-dessus pour vous inscrire à la préversion publique du mode d’orchestration flexible.


<!-- error -->
### <a name="badrequest-creating-a-virtual-machine-with-a-public-ip-address-via-networkinterfaceconfigurations"></a>BadRequest. Création d’une machine virtuelle avec une adresse IP publique via NetworkInterfaceConfigurations

```
BadRequest. Creating a Virtual Machine with a Public IP Address via NetworkInterfaceConfigurations during the Public Preview of VM NetworkInterfaceConfigurations initially requires the feature 'Microsoft.Compute/SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview'.
```

**Cause :** L’abonnement n’est pas inscrit à la préversion publique du mode d’orchestration Flexible.

**Solution :**  Vous devez inscrire la fonctionnalité `SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview` pour votre abonnement. Suivez les instructions ci-dessus pour vous inscrire à la préversion publique du mode d’orchestration flexible.


<!-- error -->
### <a name="invalidparameter-the-value-false-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-true"></a>InvalidParameter. La valeur « False » n’est pas autorisée pour le paramètre « singlePlacementGroup ». Seule la valeur True est autorisée

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Cause :** L’abonnement n’est pas inscrit à la préversion publique du mode d’orchestration Flexible.

**Solution :** Suivez les instructions plus haut pour inscrire l’abonnement à la préversion publique du mode d’orchestration Flexible.


<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-2-must-fall-in-the-range-1-to-1"></a>InvalidParameter. Le nombre de domaines d’erreur spécifié, 2, doit s’inscrire dans la plage de 1 à 1.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Cause :** Le paramètre `platformFaultDomainCount` n’est pas valide pour la région ou la zone sélectionnée.

**Solution :** Sélectionnez une valeur `platformFaultDomainCount` valide. Pour les déploiements zonaux, la valeur `platformFaultDomainCount` maximale est 1. Pour les déploiements régionaux où aucune zone n’est spécifiée, la valeur `platformFaultDomainCount` maximale varie selon la région. Consultez les scripts de l’article [Gestion de la disponibilité des machines virtuelles](../virtual-machines/availability.md) pour déterminer le nombre maximal de domaines d’erreur par région.


<!-- error -->
### <a name="operationnotallowed-deletion-of-virtual-machine-scale-set-is-not-allowed-as-it-contains-one-or-more-vms-please-delete-or-detach-the-vms-before-deleting-the-virtual-machine-scale-set"></a>OperationNotAllowed. La suppression du groupe de machines virtuelles identiques n’est pas autorisée, car il contient une ou plusieurs machines virtuelles. Supprimez ou détachez la ou les machines virtuelles avant de supprimer le groupe de machines virtuelles identiques.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Cause :** Tentative de suppression d’un groupe identique en mode d’orchestration Flexible associé à une ou plusieurs machines virtuelles.

**Solution :** Supprimez toutes les machines virtuelles associées au groupe identique en mode d’orchestration Flexible. Vous pourrez alors supprimer le groupe identique.


<!-- error -->
### <a name="invalidparameter-the-value-true-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-false"></a>InvalidParameter. La valeur « True » n’est pas autorisée pour le paramètre « singlePlacementGroup ». Seule la valeur False est autorisée.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Cause :** L’abonnement est inscrit à la préversion du mode d’orchestration Flexible, mais le paramètre `singlePlacementGroup` a la valeur *True*.

**Solution :** Le paramètre `singlePlacementGroup` doit être défini sur *False*.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Mode d’orchestration flexible pour vos groupes identiques avec le portail Azure.](flexible-virtual-machine-scale-sets-portal.md)