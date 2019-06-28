---
title: Mises à niveau automatiques d’images de système d’exploitation avec des groupes de machines virtuelles identiques Azure | Microsoft Docs
description: Découvrez comment mettre à niveau automatiquement l’image de système d’exploitation sur des instances de machines virtuelles dans un groupe identique
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: manayar
ms.openlocfilehash: 007f2801efed8da4964808056563418dec7f64d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328814"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Mises à niveau automatiques d’images de système d’exploitation de groupes de machines virtuelles identiques Azure

L’activation des mises à niveau automatiques des images de système d’exploitation pour un groupe identique facilite la gestion des mises à jour en appliquant automatiquement et en toute sécurité chaque mise à niveau du disque du système d’exploitation à toutes les instances dans le groupe identique.

La fonctionnalité de mise à niveau automatique du système d’exploitation présente les caractéristiques suivantes :

- Une fois configurée, la dernière image du système d’exploitation publiée par les éditeurs de l’image est automatiquement appliquée au groupe identique, sans aucune intervention de l’utilisateur.
- Elle effectue une mise à niveau propagée de lots d’instances chaque fois qu’une nouvelle image de plateforme est publiée par l’éditeur.
- Elle s’intègre avec les sondes d’intégrité d’application et l’[extension Intégrité de l’application](virtual-machine-scale-sets-health-extension.md).
- Elle fonctionne pour toutes les tailles de machine virtuelle et pour les images de plateforme tant Windows que Linux.
- Vous pouvez désactiver les mises à niveau automatiques à tout moment (les mises à niveau du système d’exploitation peuvent également être démarrées manuellement).
- Le disque du système d’exploitation d’une machine virtuelle est remplacé par le nouveau disque de système d’exploitation créé avec la dernière version de l’image. Les extensions configurées et les scripts de données personnalisés sont exécutés. Les disques de données persistantes sont conservés.
- Le [séquencement d’extensions](virtual-machine-scale-sets-extension-sequencing.md) est pris en charge.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Comment la mise à niveau automatique de l’image de système d’exploitation fonctionne-t-elle ?

Une mise à niveau fonctionne en remplaçant le disque du système d’exploitation d’une machine virtuelle par un nouveau disque créé à l’aide de la dernière version de l’image. Les extensions et scripts de données personnalisés configurés sont exécutés sur le disque du système d’exploitation, tandis que les disques de données persistantes sont conservés. Pour réduire au minimum le temps d’arrêt de l’application, les mises à niveau sont effectuées par lots, avec au maximum 20 % du groupe identique mis à niveau à la fois. Vous pouvez également intégrer une sonde d’intégrité d’application Azure Load Balancer ou une [extension Intégrité de l’application](virtual-machine-scale-sets-health-extension.md). Nous recommandons d’incorporer une pulsation de l’application et de valider la réussite de la mise à niveau pour chaque lot dans le processus de mise à niveau.

Le processus de mise à niveau se déroule comme suit :
1. Avant de commencer le processus de mise à niveau, l’orchestrateur vérifie qu’il n’y a pas plus de 20 % des instances dans tout le groupe identique qui présentent un état non sain (pour une raison ou une autre).
2. L’orchestrateur de mise à niveau identifie le lot d’instances de machines virtuelles à mettre à niveau, chaque lot devant compter au maximum 20 % du nombre total d’instances.
3. Le disque du système d’exploitation du lot sélectionné d’instances de machines virtuelles est remplacé par un nouveau disque de système d’exploitation créé à partir de la dernière image, et toutes les configurations et extensions spécifiées dans le modèle de groupe identique sont appliquées à l’instance mise à niveau.
4. Pour les groupes identiques configurés avec des sondes d’intégrité d’application ou l’extension Intégrité de l’application, la mise à niveau attend (jusqu’à 5 minutes) que l’instance passe à l’état sain avant de commencer la mise à niveau du lot suivant.
5. L’orchestrateur de mise à niveau suit également le pourcentage d’instances qui deviennent non saines après une mise à niveau. La mise à niveau s’arrête si plus de 20 % des instances mises à niveau passent à l’état non sain pendant le processus de mise à niveau.
6. Le processus ci-dessus se poursuit jusqu’à ce que toutes les instances dans le groupe identique aient été mises à niveau.

L’orchestrateur de mise à niveau du système d’exploitation du groupe identique vérifie l’intégrité de tout le groupe identique avant de procéder à la mise à niveau de chaque lot. Durant la mise à niveau d’un lot, il peut arriver que d’autres activités de maintenance planifiées ou non planifiées aient lieu en même temps et impactent l’intégrité des instances du groupe identique. Si c’est le cas et que plus de 20 % des instances du groupe identique passent à l’état non sain, la mise à niveau du groupe identique s’arrête à la fin du lot en cours.

## <a name="supported-os-images"></a>Images de système d’exploitation prises en charge
Seules certaines images de plateforme de système d’exploitation sont actuellement prises en charge. Les images personnalisées ne sont actuellement pas prises en charge.

Les références SKU suivantes sont prises en charge (et d’autres sont régulièrement ajoutées) :

| Publisher               | Offre de système d’exploitation      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-centre-de-données    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-centre-de-données-avec-conteneurs |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Conditions requises pour la configuration de la mise à niveau automatique d’image de système d’exploitation

- La propriété *version* de l’image de plateforme doit être définie sur *latest*.
- Utilisez des sondes d’intégrité d’application ou l’[extension Intégrité de l’application](virtual-machine-scale-sets-health-extension.md) pour des groupes identiques autres que Service Fabric.
- Assurez-vous que les ressources externes spécifiées dans le modèle de groupe identique sont disponibles et à jour. Les ressources concernées sont l’URI SAS pour l’amorçage de la charge utile dans les propriétés d’extension de machine virtuelle et dans le compte de stockage, les références à des secrets dans le modèle, etc.

## <a name="configure-automatic-os-image-upgrade"></a>Configurer la mise à niveau automatique d’image de système d’exploitation
Pour configurer la mise à niveau automatique d’image de système d’exploitation, vérifiez que la propriété *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* est définie sur *true* dans la définition du modèle de groupe identique.

### <a name="rest-api"></a>API REST
L’exemple suivant montre comment activer les mises à niveau automatiques du système d’exploitation pour un modèle de groupe identique :

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilisez l’applet de commande [Update-AzVmss](/powershell/module/az.compute/update-azvmss) afin de vérifier l’historique de mise à niveau du système d’exploitation pour votre groupe identique. L’exemple suivant configure les mises à niveau automatiques pour le groupe identique nommé *myVMSS* dans le groupe de ressources appelé *myResourceGroup* :

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Utilisez [az vmss update](/cli/azure/vmss#az-vmss-update) afin de vérifier l’historique de mise à niveau du système d’exploitation pour votre groupe identique Utilisez Azure CLI 2.0.47 ou une version ultérieure. L’exemple suivant configure les mises à niveau automatiques pour le groupe identique nommé *myVMSS* dans le groupe de ressources appelé *myResourceGroup* :

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Utilisation de sondes d’intégrité d’application

Durant une mise à niveau du système d’exploitation, les instances de machine virtuelle dans un groupe identique sont mises à niveau par lot. La mise à niveau doit se poursuivre uniquement si l’application du client est intègre sur les instances de machine virtuelle mises à niveau. Nous recommandons que l’application envoie des signaux d’intégrité au moteur de mise à niveau du système d’exploitation pour le groupe identique. Par défaut, pendant les mises à niveau du système d’exploitation, la plateforme vérifie l’état d’alimentation des machines virtuelles et l’état de provisionnement des extensions pour déterminer l’intégrité d’une instance de machine virtuelle après sa mise à niveau. Lors de la mise à niveau du système d’exploitation d’une instance de machine virtuelle, le disque du système d’exploitation sur l’instance de machine virtuelle est remplacé par un nouveau disque basé sur la dernière version de l’image. Après la mise à niveau du système d’exploitation, les extensions configurées sont exécutées sur ces machines virtuelles. L’application est considérée comme saine uniquement quand toutes les extensions sur l’instance ont été correctement provisionnées.

Un groupe identique peut éventuellement être configuré avec des sondes d’intégrité d’application pour fournir à la plateforme des informations précises sur l’état actuel de l’application. Les sondes d’intégrité d’application sont des sondes d’équilibreur de charge personnalisées qui sont utilisées comme signal d’intégrité. L’application exécutée sur une instance de machine virtuelle d’un groupe identique peut répondre à des requêtes HTTP ou TCP externes en indiquant si elle est saine. Pour plus d’informations sur le fonctionnement des sondes d’équilibreur de charge personnalisées, consultez [Comprendre les sondes d’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md). L’utilisation d’une sonde d’intégrité d’application n’est pas obligatoire pour les groupes identiques Service Fabric, mais cela est recommandé. Pour les groupes identiques autres que Service Fabric, vous devez utiliser des sondes d’intégrité d’application Load Balancer ou l’[extension Intégrité de l’application](virtual-machine-scale-sets-health-extension.md).

Si le groupe identique est configuré pour utiliser plusieurs groupes de sélection élective, vous devez utiliser des sondes avec un [équilibreur de charge standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configuration d’une sonde d’équilibreur de charge personnalisée comme sonde d’intégrité d’application dans un groupe identique
La bonne pratique est de créer une sonde d’équilibreur de charge de manière explicite pour déterminer l’intégrité du groupe identique. Vous pouvez utiliser le même point de terminaison pour une sonde HTTP ou TCP existante, mais une sonde d’intégrité n’a pas forcément le même comportement qu’une sonde d’équilibreur de charge standard. Par exemple, une sonde d’équilibreur de charge standard signale un état non sain quand la charge sur l’instance est trop élevée, mais cela ne détermine pas forcément l’intégrité de l’instance durant une mise à niveau automatique du système d’exploitation. Configurez la sonde pour que le taux de sondage élevé soit inférieur à deux minutes.

La sonde d’équilibreur de charge peut être référencée dans la propriété *networkProfile* du groupe identique et être associée à un équilibreur de charge interne ou public, comme suit :

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Lors de l’utilisation de mises à niveau automatiques du système d’exploitation avec Service Fabric, la nouvelle image du système d’exploitation est déployée, un domaine de mise à jour après l’autre, pour maintenir la haute disponibilité des services en cours d’exécution dans Service Fabric. Pour utiliser les mises à niveau automatiques du système d’exploitation dans Service Fabric, votre cluster doit être configuré pour utiliser le niveau de durabilité Silver ou une version supérieure. Pour plus d’informations sur les caractéristiques de durabilité des clusters Service Fabric, voir [cette documentation](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Tenir à jour les informations d’identification
Si votre groupe identique utilise des informations d’identification pour accéder aux ressources externes (par exemple, quand une extension de machine virtuelle configurée utilise un jeton SAP pour le compte de stockage), assurez-vous que les informations d’identification sont à jour. Si les informations d’identification, y compris les certificats et les jetons, ont expiré, la mise à niveau échoue, et le premier lot de machines virtuelles reste dans un état d’échec.

Pour récupérer les machines virtuelles et réactiver la mise à niveau automatique du système d’exploitation après un échec d’authentification des ressources, effectuez les étapes recommandées suivantes :

* Regénérez le jeton (ou d’autres informations d’identification) qui a été passé à vos extensions.
* Vérifiez que toutes les informations d’identification utilisées pour les communications entre les machines virtuelles et les entités externes sont tenues à jour.
* Mettez à jour chaque extension dans le modèle de groupe identique avec les nouveaux jetons.
* Déployez le groupe identique mis à jour. Cette opération met à jour toutes les instances de machine virtuelle, y compris celles en échec.

## <a name="using-application-health-extension"></a>Utilisation de l’extension Intégrité de l’application
L’extension Intégrité de l’application est déployée à l’intérieur d’une instance de groupe de machines virtuelles identiques et rend compte de l’intégrité des machines virtuelles à partir de l’instance de groupe identique. Vous pouvez configurer l’extension pour sonder un point de terminaison d’application et mettre à jour l’état de l’application sur cette instance. Cet état de l’instance est vérifié par Azure pour déterminer si une instance est éligible pour des opérations de mise à niveau.

Étant donné que l’extension rend compte de l’intégrité à partir d’une machine virtuelle, elle peut être utilisée dans les situations où les sondes externes telles que les sondes d’intégrité d’application (qui utilisent des [sondes](../load-balancer/load-balancer-custom-probe-overview.md) Azure Load Balancer personnalisées) ne peuvent pas être utilisées.

Il existe plusieurs façons de déployer l’extension Intégrité de l’application dans vos groupes identiques, comme cela est expliqué dans les exemples de [cet article](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Obtenir l’historique des mises à niveau automatiques d’image de système d’exploitation
Vous pouvez vérifier l’historique de la dernière mise à niveau du système d’exploitation effectuée dans un groupe identique à l’aide d’Azure PowerShell, d’Azure CLI 2.0 ou des API REST. Vous pouvez obtenir l’historique des cinq dernières tentatives de mise à niveau du système d’exploitation au cours des deux derniers mois.

### <a name="rest-api"></a>API REST
L’exemple suivant utilise l’[API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) pour vérifier l’état du groupe identique nommé *myVMSS* dans le groupe de ressources appelé *myResourceGroup* :

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

L’appel de Get retourne des propriétés similaires à l’exemple de sortie suivant :

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Utilisez l’applet de commande [Get-AzVmss](/powershell/module/az.compute/get-azvmss) afin de vérifier l’historique de mise à niveau du système d’exploitation pour votre groupe identique. L’exemple suivant montre comment vérifier l’état de la mise à niveau du système d’exploitation pour un groupe identique nommé *myVMSS* dans le groupe de ressources appelé *myResourceGroup* :

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Utilisez [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) afin de vérifier l’historique de mise à niveau du système d’exploitation pour votre groupe identique Utilisez Azure CLI 2.0.47 ou une version ultérieure. L’exemple suivant montre comment vérifier l’état de la mise à niveau du système d’exploitation pour un groupe identique nommé *myVMSS* dans le groupe de ressources appelé *myResourceGroup* :

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Comment obtenir la dernière version d’une image de système d’exploitation de plateforme ?

Vous pouvez obtenir les versions d’image des références SKU prises en charge pour la mise à niveau automatique du système d’exploitation en utilisant les exemples ci-dessous :

### <a name="rest-api"></a>API REST
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Déployer les mises à niveau avec un modèle

Vous pouvez utiliser des modèles pour déployer un groupe identique avec les mises à niveau automatiques du système d’exploitation pour les images prises en charge comme [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres exemples d’utilisation des mises à niveau automatiques du système d’exploitation avec des groupes identiques, consultez le [dépôt GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
