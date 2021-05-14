---
title: Modes d’orchestration pour les groupes de machines virtuelles identiques dans Azure
description: Découvrez comment utiliser les modes d’orchestration Flexible et Uniform pour les groupes de machines virtuelles identiques dans Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 19d0486461366f77a09b9e6a395e60a51efea853
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109754368"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Préversion : modes d’orchestration pour les groupes de machines virtuelles identiques dans Azure

Les groupes de machines virtuelles identiques fournissent un regroupement logique des machines virtuelles gérées par la plateforme. Avec les groupes identiques, vous créez un modèle de configuration de machine virtuelle, ajoutez ou supprimez automatiquement des instances supplémentaires en fonction de la charge de l’UC ou de la mémoire, puis effectuez une mise à niveau automatique vers la dernière version du système d’exploitation. Traditionnellement, les groupes identiques vous permettent de créer des machines virtuelles à l’aide d’un modèle de configuration de machine virtuelle fourni au moment de la création d’un groupe identique, et le groupe identique peut uniquement gérer des machines virtuelles qui sont créées implicitement en fonction du modèle de configuration.

Les modes d’orchestration de groupe identique vous permettent de mieux contrôler la façon dont les instances de machine virtuelle sont gérées par le groupe identique.

> [!IMPORTANT]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="scale-sets-with-uniform-orchestration"></a>Groupes identiques avec orchestration Uniform
Ce mode est optimisé pour les charges de travail sans état à grande échelle avec des instances identiques.

Les groupes de machines virtuelles identiques (VMSS, virtual machine scale set) avec orchestration Uniform utilisent un modèle ou un profil de machine virtuelle pour effectuer un scale-up jusqu’à la capacité souhaitée. Même s’il est possible de gérer ou personnaliser des instances de machine virtuelle individuelles, le mode Uniform utilise des instances de machine virtuelle identiques. Les instances de machine virtuelle Uniform individuelles sont exposées par le biais des commandes d’API de machine virtuelle VMSS. Les instances individuelles ne sont pas compatibles avec les commandes d’API de machine virtuelle Azure IaaS standard, les fonctionnalités de gestion Azure telles que les autorisations RBAC d’étiquetage des ressources d’Azure Resource Manager et les services Sauvegarde Azure et Azure Site Recovery. L’orchestration Uniform garantit une haute disponibilité du domaine d’erreur quand elle est configurée avec moins de 100 instances. L’orchestration Uniform est en disponibilité générale et prend en charge une gamme complète de fonctionnalités de gestion et d’orchestration des groupes identiques, y compris la mise à l’échelle automatique basée sur des métriques, la protection des instances et les mises à niveau automatiques de système d’exploitation.


## <a name="scale-sets-with-flexible-orchestration"></a>Groupes identiques avec orchestration Flexible
Obtenez une haute disponibilité à grande échelle avec un ou plusieurs types de machine virtuelle.

Avec l’orchestration Flexible, Azure offre une expérience unifiée sur tout l’écosystème de machines virtuelles Azure. L’orchestration Flexible garantit une haute disponibilité (jusqu’à 1 000 machines virtuelles) en répartissant les machines virtuelles entre différents domaines d’erreur dans une région ou dans une zone de disponibilité. Vous pouvez ainsi effectuer un scale-out de votre application tout en conservant l’isolation du domaine d’erreur, essentielle pour exécuter des charges de travail avec état ou basées sur un quorum, y compris :
- Charges de travail basées sur un quorum
- Bases de données open source
- Applications avec état
- Services nécessitant une haute disponibilité à grande échelle
- Services cherchant à combiner différents types de machines virtuelles ou à tirer parti à la fois de machines virtuelles spot et à la demande
- Applications d’un groupe à haute disponibilité existant

> [!IMPORTANT]
> Les groupes de machines virtuelles identiques en mode d’orchestration Flexible sont actuellement en préversion publique. Une procédure de consentement est requise pour utiliser la fonctionnalité en préversion publique décrite ci-dessous.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Quels changements le mode d’orchestration Flexible apporte-t-il ?
L’un des principaux avantages de l’orchestration Flexible est qu’elle fournit des fonctionnalités d’orchestration sur des machines virtuelles Azure IaaS standard et non sur des machines virtuelles enfants de groupe identique. Vous pouvez donc utiliser toutes les API de machine virtuelle standard pour la gestion d’instances avec orchestration Flexible, plutôt que les API de machine virtuelle VMSS que vous utilisez avec l’orchestration Uniform. Pendant la période de préversion, la gestion des instances avec une orchestration Flexible et une orchestration Uniform diffère par plusieurs points. D’une manière générale, nous vous recommandons d’utiliser les API de machine virtuelle Azure IaaS standard dans la mesure du possible. Cette section met en avant des exemples de bonnes pratiques pour la gestion des instances de machine virtuelle avec une orchestration Flexible.

### <a name="assign-fault-domain-during-vm-creation"></a>Attribuer un domaine d’erreur au moment de la création de la machine virtuelle
Vous pouvez choisir le nombre de domaines d’erreur pour le groupe identique avec orchestration Flexible. Par défaut, quand vous ajoutez une machine virtuelle à un groupe identique Flexible, Azure répartit uniformément les instances entre les domaines d’erreur. Il est recommandé de laisser Azure attribuer le domaine d’erreur. Cependant, pour des scénarios avancés ou de dépannage, vous pouvez remplacer ce comportement par défaut et spécifier le domaine d’erreur auquel appartiendra l’instance.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Nommage d’instance
Quand vous créez une machine virtuelle et l’ajoutez à un groupe identique Flexible, vous disposez d’un contrôle total sur les noms d’instance dans le cadre des règles de convention de nommage Azure. Quand des machines virtuelles sont ajoutées automatiquement au groupe identique par le biais d’une mise à l’échelle automatique, vous spécifiez un préfixe et Azure ajoute un numéro unique à la fin du nom.

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

### <a name="scale-sets-vm-batch-operations"></a>Opérations de traitement par lots de machines virtuelles de groupe identique
Utilisez les commandes de machine virtuelle standard pour démarrer, arrêter, redémarrer et supprimer des instances, plutôt que les API de machine virtuelle VMSS. Les opérations de traitement par lots de machine virtuelle VMSS (tout démarrer, tout arrêter, tout réinitialiser, etc.) ne sont pas utilisées avec le mode d’orchestration Flexible.

### <a name="monitor-application-health"></a>Superviser l’intégrité de l’application
Avec la supervision du fonctionnement de l’application, votre application fournit à Azure une pulsation pour déterminer si votre application est saine ou non. Azure peut remplacer automatiquement les instances de machine virtuelle non saines. Pour les instances de groupe identique Flexibles, vous devez installer et configurer l’extension Intégrité de l’application sur la machine virtuelle. Pour les instances de groupe identique Uniform, vous pouvez utiliser l’extension Intégrité de l’application ou mesurer l’intégrité avec une sonde d’intégrité personnalisée Azure Load Balancer.

### <a name="list-scale-sets-vm-api-changes"></a>Modifications de l’API de machine virtuelle qui liste les instances d’un groupe identique
VMSS (Virtual Machine Scale Sets) vous permet de lister les instances appartenant au groupe identique. Avec l’orchestration Flexible, la commande de machine virtuelle VMSS de liste retourne une liste d’ID de machines virtuelles d’un groupe identique. Vous pouvez ensuite appeler les commandes de machine virtuelle VMSS GET pour obtenir plus d’informations sur la façon dont le groupe identique fonctionne avec l’instance de machine virtuelle. Pour obtenir toutes les informations de la machine virtuelle, utilisez les commandes de machine virtuelle GET standard ou [Azure Resource Graph](../governance/resource-graph/overview.md).

### <a name="retrieve-boot-diagnostics-data"></a>Récupérer les données de diagnostics de démarrage
Utilisez les API et commandes de machine virtuelle standard pour récupérer les captures d’écran et données de diagnostic de démarrage d’instance. Les commandes et API de diagnostic de démarrage de machine virtuelle VMSS ne sont pas utilisées avec les instances en mode d’orchestration Flexible.

### <a name="vm-extensions"></a>Extensions de machine virtuelle
Utilisez les extensions ciblant les machines virtuelles standard plutôt que celles ciblant les instances en mode d’orchestration Uniform.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Comparaison du mode d’orchestration Flexible, du mode d’orchestration Uniform et des groupes à haute disponibilité
Le tableau suivant compare le mode d’orchestration Flexible, le mode d’orchestration Uniform et les groupes à haute disponibilité selon leurs fonctionnalités.

| Fonctionnalité | Prise en charge par l’orchestration Flexible (préversion) | Prise en charge par l’orchestration Uniform (disponibilité générale) | Prise en charge par les groupes à haute disponibilité (disponibilité générale) |
|-|-|-|-|
|         Type de machine virtuelle  | Machine virtuelle Azure IaaS standard (Microsoft.compute /virtualmachines)  | Machines virtuelles spécifiques à un groupe identique (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Machine virtuelle Azure IaaS standard (Microsoft.compute /virtualmachines)  |
|         Références SKU prises en charge  |            Série D, Série E, Série F, Série A, Série B, Intel, AMD  |            Toutes les références SKU  |            Toutes les références SKU  |
|         Zones de disponibilité  |            Spécification facultative de toutes les instances appartenant à une même zone de disponibilité |            Spécification des instances appartenant à 1, 2 ou 3 zones de disponibilité  |            Non pris en charge  |
|         Contrôle total sur la machine virtuelle, les cartes réseau et les disques  |            Oui  |            Contrôle limité avec l’API de machine virtuelle VMSS  |            Oui  |
|         Mise à l’échelle automatique  |            Non  |            Oui  |            Non  |
|         Attribution d’une machine virtuelle à un domaine d’erreur spécifique  |            Oui  |             Non   |            Non  |
|         Suppression des cartes réseau et des disques en cas de suppression des instances de machine virtuelle  |            Non  |            Oui  |            Non  |
|         Stratégie de mise à niveau (groupes de machines virtuelles identiques) |            Non  |            Automatique, propagée, manuelle  |            N/A  |
|         Mises à jour automatiques de système d’exploitation (groupes de machines virtuelles identiques) |            Non  |            Oui  |            N/A  |
|         Mise à jour corrective de sécurité intégrée (« in-guest »)  |            Oui  |            Non  |            Oui  |
|         Notifications d’arrêt (groupes de machines virtuelles identiques) |            Non  |            Oui  |            N/A  |
|         Réparation d’instance (groupes de machines virtuelles identiques) |            Non  |            Oui   |            N/A  |
|         Mise en réseau accélérée  |            Oui  |            Oui  |            Oui  |
|         Instances spot et tarifs   |            Oui, vous pouvez avoir des instances spot et des instances à priorité normale  |            Oui, les instances doivent être toutes des instances spot ou toutes des instances normales  |            Non, des instances à priorité normale uniquement  |
|         Différents systèmes d’exploitation  |            Oui, Linux et Windows peuvent résider dans le même groupe identique Flexible |            Non, les instances ont le même système d’exploitation  |               Oui, Linux et Windows peuvent résider dans le même groupe identique Flexible |
|         Supervision de l’intégrité de l’application  |            Extension Intégrité de l’application  |            Extension Intégrité de l’application ou sonde Azure Load Balancer  |            Extension Intégrité de l’application  |
|         Disques UltraSSD   |            Oui  |            Oui, pour les déploiements zonaux uniquement  |            Non  |
|         Infiniband   |            Non  |            Oui, groupe de placement unique seulement  |            Oui  |
|         Accélérateur d’écriture   |            Non  |            Oui  |            Oui  |
|         Groupes de placement de proximité   |            Oui  |            Oui  |            Oui  |
|         Hôtes dédiés Azure   |            Non  |            Oui  |            Oui  |
|         Équilibreur de charge logiciel De base   |            Non  |            Oui  |            Oui  |
|         Azure Load Balancer - SKU Standard |            Oui  |            Oui  |            Oui  |
|         Application Gateway  |            Non  |            Oui  |            Oui  |
|         Contrôle de la maintenance   |            Non  |            Oui  |            Oui  |
|         Liste des machines virtuelles d’un groupe  |            Oui  |            Oui  |            Oui, liste des machines virtuelles d’un groupe à haute disponibilité  |
|         Alertes Azure  |            Non  |            Oui  |            Oui  |
|         Insights de machine virtuelle  |            Non  |            Oui  |            Oui  |
|         Sauvegarde Azure  |            Oui  |            Oui  |            Oui  |
|         Azure Site Recovery  |     Non  |            Non  |            Oui  |
|         Ajout d’une machine virtuelle existante à un groupe/suppression du groupe  |            Non  |            Non  |            Non  |


## <a name="register-for-flexible-orchestration-mode"></a>S’inscrire au mode d’orchestration Flexible
Avant de pouvoir déployer des groupes de machines virtuelles identiques en mode d’orchestration Flexible, vous devez inscrire la fonctionnalité d’évaluation pour votre abonnement. L’inscription peut prendre plusieurs minutes. Vous pouvez utiliser les commandes Azure PowerShell ou Azure CLI suivantes pour l’inscription.

### <a name="azure-portal"></a>Portail Azure
Accédez à la page de détails de l’abonnement pour lequel vous souhaitez créer un groupe identique en mode d’orchestration flexible, puis sélectionnez Fonctionnalités d’évaluation dans le menu. Sélectionnez les deux fonctionnalités d’orchestrateur à activer : _VMOrchestratorSingleFD_ et _VMOrchestratorMultiFD_, puis appuyez sur le bouton Registre. L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes.

![Inscription de fonctionnalité.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul. Accédez à l’onglet Fournisseurs de ressources pour votre abonnement, sélectionnez Microsoft.compute, puis cliquez sur Réinscrire.

![Réinscrire](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell
Utilisez l’applet de commande [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) pour activer la préversion pour votre abonnement.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute
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

Démarrez avec le mode d’orchestration Flexible pour vos groupes identiques avec le portail Azure, Azure CLI, Terraform ou l’API REST.

### <a name="azure-portal"></a>Portail Azure

Créez un groupe de machines virtuelles identiques en mode d’orchestration Flexible avec le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans la barre de recherche, recherchez et sélectionnez **Groupes de machines virtuelles identiques**.
1. Sélectionnez **Créer** dans la page **Groupes de machines virtuelles identiques**.
1. Dans la page **Créer un groupe de machines virtuelles identiques**, consultez la section **Orchestration**.
1. Sous **Mode d’orchestration**, sélectionnez l’option **Flexible**.
1. Spécifiez la valeur **Nombre de domaines d’erreur**.
1. Terminez la création de votre groupe identique. Pour plus d’informations sur la création d’un groupe identique, consultez [Créer un groupe identique dans le portail Azure](quick-create-portal.md#create-virtual-machine-scale-set).

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Mode d’orchestration dans le portail au moment de la création d’un groupe identique":::

Ensuite, ajoutez une machine virtuelle au groupe identique en mode d’orchestration Flexible.

1. Dans la barre de recherche, recherchez et sélectionnez **Machines virtuelles**.
1. Sélectionnez **Ajouter** dans la page **Machines virtuelles**.
1. Dans l’onglet **Informations de base**, consultez la section **Détails de l’instance**.
1. Ajoutez votre machine virtuelle au groupe identique en mode d’orchestration Flexible en sélectionnant le groupe identique sous **Options de disponibilité**. Vous pouvez ajouter la machine virtuelle à un groupe identique dans la même région, la même zone et le même groupe de ressources.
1. Terminez la création de votre machine virtuelle.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Ajouter une machine virtuelle au groupe identique en mode d’orchestration Flexible":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Créez un groupe de machines virtuelles identiques Flexible à l’aide d’Azure CLI. L’exemple suivant illustre la création d’un groupe identique Flexible avec un nombre de domaines d’erreur défini sur 3. Une machine virtuelle est créée, puis ajoutée au groupe identique Flexible.

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"
vmname="myVM"
rg="my-resource-group"

az group create -n "$rg" -l $location
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS
```

### <a name="terraform"></a>Terraform
Créez un groupe de machines virtuelles identiques Flexible à l’aide de Terraform. Ce processus nécessite le **fournisseur Terraform Azurerm v2.15.0** ou une version ultérieure. Prenez note des paramètres suivants :
- Si aucune zone n’est spécifiée, le paramètre `platform_fault_domain_count` peut avoir la valeur 1, 2 ou 3 selon la région.
- Quand une zone est spécifiée, le paramètre `the fault domain count` peut avoir la valeur 1.
- Le paramètre `single_placement_group` doit avoir la valeur `false` pour les groupes de machines virtuelles identiques en mode Flexible.
- Si vous effectuez un déploiement régional, il est inutile de spécifier le paramètre `zones`.

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>API REST

1. Créez un groupe identique vide. Les paramètres suivants sont requis :
    - Version d’API 2019-12-01 (ou supérieure)
    - Le groupe de placement unique doit avoir la valeur `false` pour la création d’un groupe identique Flexible

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Ajoutez des machines virtuelles au groupe identique.
    1. Attribuez la propriété `virtualMachineScaleSet` au groupe identique que vous avez créé. Vous devez spécifier la propriété `virtualMachineScaleSet` au moment de la création de la machine virtuelle.
    1. Vous pouvez utiliser la fonction de modèle Azure Resource Manager **copy()** pour créer plusieurs machines virtuelles simultanément. Consultez [Itération sur la ressource](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) dans les modèles Azure Resource Manager.

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Pour obtenir un exemple complet, consultez le [modèle de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-vmss-flexible-orchestration-mode).


## <a name="frequently-asked-questions"></a>Forum aux questions

**Quelle capacité de mise à l’échelle l’orchestration Flexible prend-elle en charge ?**

Vous pouvez ajouter jusqu’à 1 000 machines virtuelles à un groupe identique en mode d’orchestration Flexible.

**En quoi l’orchestration Flexible est-elle différente des groupes à haute disponibilité ou de l’orchestration Uniform ?**

| Attribut de disponibilité  | Orchestration Flexible  | Orchestration Uniform  | Groupes à haute disponibilité  |
|-|-|-|-|
| Déploiement entre plusieurs zones de disponibilité  | Non  | Oui  | Non  |
| Garanties de disponibilité de domaine d’erreur au sein d’une région  | Oui, jusqu’à 1 000 instances peuvent être réparties sur un maximum de 3 domaines d’erreur dans la région. Le nombre maximal de domaines d’erreur varie selon la région  | Oui, jusqu’à 100 instances  | Oui, jusqu’à 200 instances  |
| Groupes de placement  | Le mode Flexible utilise toujours plusieurs groupes de placement (singlePlacementGroup = false)  | Vous pouvez choisir un groupe de placement unique ou plusieurs groupes de placement | N/A  |
| Domaines de mise à jour  | Aucun. La maintenance et les mises à jour de l’ordinateur hôte sont effectuées domaine d’erreur par domaine d’erreur  | Jusqu’à 5 domaines de mise à jour  | Jusqu’à 20 domaines de mise à jour  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Résoudre les problèmes de groupes identiques avec orchestration Flexible
Trouvez la solution adaptée à votre scénario de dépannage.

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Cause :** L’abonnement n’est pas inscrit à la préversion publique du mode d’orchestration Flexible.

**Solution :** Suivez les instructions plus haut pour inscrire l’abonnement à la préversion publique du mode d’orchestration Flexible.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Cause :** Le paramètre `platformFaultDomainCount` n’est pas valide pour la région ou la zone sélectionnée.

**Solution :** Sélectionnez une valeur `platformFaultDomainCount` valide. Pour les déploiements zonaux, la valeur `platformFaultDomainCount` maximale est 1. Pour les déploiements régionaux où aucune zone n’est spécifiée, la valeur `platformFaultDomainCount` maximale varie selon la région. Consultez les scripts de l’article [Gestion de la disponibilité des machines virtuelles](../virtual-machines/availability.md) pour déterminer le nombre maximal de domaines d’erreur par région.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Cause :** Tentative de suppression d’un groupe identique en mode d’orchestration Flexible associé à une ou plusieurs machines virtuelles.

**Solution :** Supprimez toutes les machines virtuelles associées au groupe identique en mode d’orchestration Flexible. Vous pourrez alors supprimer le groupe identique.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Cause :** L’abonnement est inscrit à la préversion du mode d’orchestration Flexible, mais le paramètre `singlePlacementGroup` a la valeur *True*.

**Solution :** Le paramètre `singlePlacementGroup` doit être défini sur *False*.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Découvrez comment déployer votre application sur un groupe identique.](virtual-machine-scale-sets-deploy-app.md)
