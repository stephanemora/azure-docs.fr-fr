---
title: Mises à niveau automatiques d’images de système d’exploitation avec des groupes de machines virtuelles identiques Azure | Microsoft Docs
description: Découvrez comment mettre à niveau automatiquement l’image de système d’exploitation sur des instances de machines virtuelles dans un groupe identique
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: cf25d08fc9a0e1ae458d350be93af31447928ecb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069452"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Mises à niveau automatiques d’images de système d’exploitation de groupes de machines virtuelles identiques Azure

La mise à niveau automatique de l’image de système d’exploitation est une fonctionnalité des groupes de machines virtuelles identiques Azure, qui met automatiquement à niveau toutes les instances de machines virtuelles avec l’image de système d’exploitation la plus récente.

La fonctionnalité de mise à niveau automatique du système d’exploitation présente les caractéristiques suivantes :

- Une fois configurée, la dernière image du système d’exploitation publiée par les éditeurs de l’image est automatiquement appliquée au groupe identique, sans aucune intervention de l’utilisateur.
- Elle effectue une mise à niveau propagée de lots d’instances chaque fois qu’une nouvelle image de plateforme est publiée par l’éditeur.
- Elle s’intègre avec une sonde d’intégrité d’application.
- Elle fonctionne pour toutes les tailles de machine virtuelle et pour les images de plateforme tant Windows que Linux.
- Vous pouvez désactiver les mises à niveau automatiques à tout moment (les mises à niveau du système d’exploitation peuvent également être démarrées manuellement).
- Le disque du système d’exploitation d’une machine virtuelle est remplacé par le nouveau disque de système d’exploitation créé avec la dernière version de l’image. Les extensions configurées et les scripts de données personnalisés sont exécutés. Les disques de données persistantes sont conservés.
- Le chiffrement de disque Azure (en préversion) n’est actuellement pas pris en charge.  

## <a name="how-does-automatic-os-image-upgrade-work"></a>Comment la mise à niveau automatique de l’image de système d’exploitation fonctionne-t-elle ?

Une mise à niveau fonctionne en remplaçant le disque du système d’exploitation d’une machine virtuelle par un nouveau disque créé à l’aide de la dernière version de l’image. Les extensions et scripts de données personnalisés configurés sont exécutés, tandis que les disques de données persistantes sont conservés. Pour réduire au minimum le temps d’arrêt de l’application, les mises à niveau sont effectuées par lots de machines, avec au maximum 20 % du groupe identique étant mis à niveau à tout moment. Vous avez également la possibilité d’intégrer une sonde d’intégrité d’application Azure Load Balancer. Il est vivement recommandé d’incorporer une pulsation de l’application et de valider la réussite de la mise à niveau pour chaque lot dans le processus de mise à niveau. Les étapes d’exécution sont les suivantes : 

1. Avant de commencer le processus de mise à niveau, l’orchestrateur vérifie que pas plus de 20 % des instances ne sont défectueuses. 
2. Il identifie le lot d’instances de machines virtuelles à mettre à niveau, chaque lot comptant au maximum 20 % du nombre total d’instances.
3. Il met à niveau de l’image de système d’exploitation de ce lot d’instances de machines virtuelles.
4. Si le client a configuré des sondes d’intégrité d’application, la mise à niveau attend pendant jusqu’à 5 minutes que les sondes indiquent un état sain avant de passer à la mise à niveau du lot suivant. 
5. S’il reste des instances à mettre à niveau, la mise à niveau revient à l’étape 1 pour le lot suivant. Sinon, la mise à niveau est terminée.

L’orchestrateur de mise à niveau du système d’exploitation du groupe identique vérifie l’intégrité de toutes les instances de machines virtuelles avant de procéder à la mise à niveau de chaque lot. Pendant la mise à niveau d’un lot, il peut arriver que d’autres opérations de maintenance planifiées ou non planifiées aient lieu en même temps dans les centres de données Azure et impactent la disponibilité de vos machines virtuelles. Vous pouvez donc avoir plus de 20 % des instances qui sont temporairement arrêtées. Dans ce cas, à la fin du lot en cours, la mise à niveau du groupe identique s’arrête.

## <a name="supported-os-images"></a>Images de système d’exploitation prises en charge
Seules certaines images de plateforme de système d’exploitation sont actuellement prises en charge. Vous ne pouvez pas utiliser des images personnalisées que vous avez créées vous-même. 

Les références SKU suivantes sont actuellement prises en charge (d’autres seront ajoutées ultérieurement) :
    
| Publisher               | Offre de système d’exploitation      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stable             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-centre-de-données    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-centre-de-données-avec-conteneurs |

* La prise en charge de ces images est en cours de déploiement et sera disponible sous peu dans toutes les régions Azure. 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Conditions requises pour la configuration de la mise à niveau automatique d’image de système d’exploitation

- La propriété *version* de l’image de plateforme doit être définie sur *latest*.
- Utilisez des sondes d’intégrité d’application pour des groupes identiques autres que Service Fabric.
- Vérifiez que les ressources auxquelles le modèle de groupe identique fait référence sont disponibles et tenues à jour. 
  L’URI Exa.SAS pour l’amorçage de la charge utile dans les propriétés d’extension de machine virtuelle et dans le compte de stockage fait référence à des clés secrètes dans le modèle. 

## <a name="configure-automatic-os-image-upgrade"></a>Configurer la mise à niveau automatique d’image de système d’exploitation
Pour configurer la mise à niveau automatique d’image de système d’exploitation, vérifiez que la propriété *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* est définie sur *true* dans la définition du modèle de groupe identique. 

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

L’exemple suivant utilise Azure CLI (version 2.0.47 ou ultérieure) pour configurer des mises à niveau automatiques pour le groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
La prise en charge de la configuration de cette propriété via Azure PowerShell sera bientôt déployée.

## <a name="using-application-health-probes"></a>Utilisation de sondes d’intégrité d’application 

Durant une mise à niveau du système d’exploitation, les instances de machine virtuelle dans un groupe identique sont mises à niveau par lot. La mise à niveau doit se poursuivre uniquement si l’application du client est intègre sur les instances de machine virtuelle mises à niveau. Nous recommandons que l’application envoie des signaux d’intégrité au moteur de mise à niveau du système d’exploitation pour le groupe identique. Par défaut, pendant les mises à niveau du système d’exploitation, la plateforme vérifie l’état d’alimentation des machines virtuelles et l’état de provisionnement des extensions pour déterminer l’intégrité d’une instance de machine virtuelle après sa mise à niveau. Lors de la mise à niveau du système d’exploitation d’une instance de machine virtuelle, le disque du système d’exploitation sur l’instance de machine virtuelle est remplacé par un nouveau disque basé sur la dernière version de l’image. Après la mise à niveau du système d’exploitation, les extensions configurées sont exécutées sur ces machines virtuelles. L’application est considérée comme intègre uniquement quand toutes les extensions sur une machine virtuelle ont été correctement provisionnées. 

Un groupe identique peut éventuellement être configuré avec des sondes d’intégrité d’application pour fournir à la plateforme des informations précises sur l’état actuel de l’application. Les sondes d’intégrité d’application sont des sondes d’équilibreur de charge personnalisées qui sont utilisées comme signal d’intégrité. L’application exécutée sur une instance de machine virtuelle d’un groupe identique peut répondre à des demandes HTTP ou TCP externes en indiquant si elle est intègre. Pour plus d’informations sur le fonctionnement des sondes d’équilibreur de charge personnalisées, consultez [Comprendre les sondes d’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md). L’utilisation d’une sonde d’intégrité d’application n’est pas obligatoire pour les mises à niveau automatiques du système d’exploitation, mais cela est fortement recommandé.

Si le groupe identique est configuré pour utiliser plusieurs groupes de sélection élective, vous devez utiliser des sondes avec un [équilibreur de charge standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configuration d’une sonde d’équilibreur de charge personnalisée comme sonde d’intégrité d’application dans un groupe identique
La bonne pratique est de créer une sonde d’équilibreur de charge de manière explicite pour déterminer l’intégrité du groupe identique. Vous pouvez utiliser le même point de terminaison pour une sonde HTTP ou TCP existante, mais sachez qu’une sonde d’intégrité peut avoir un comportement différent de celui d’une sonde d’équilibreur de charge standard. Par exemple, une sonde d’équilibreur de charge standard peut signaler un état non intègre si la charge sur l’instance est trop élevée, alors que cela ne détermine pas forcément l’intégrité de l’instance durant une mise à niveau automatique du système d’exploitation. Configurez la sonde pour que le taux de sondage élevé soit inférieur à deux minutes.

La sonde d’équilibreur de charge peut être référencée dans la propriété *networkProfile* du groupe identique et être associée à un équilibreur de charge interne ou public, comme suit :

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> Lors de l’utilisation de mises à niveau automatiques du système d’exploitation avec Service Fabric, la nouvelle image du système d’exploitation est déployée, un domaine de mise à jour après l’autre, pour maintenir la haute disponibilité des services en cours d’exécution dans Service Fabric. Pour plus d’informations sur les caractéristiques de durabilité des clusters Service Fabric, voir [cette documentation](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Tenir à jour les informations d’identification
Si votre groupe identique utilise des informations d’identification pour accéder aux ressources externes (par exemple, quand une extension de machine virtuelle est configurée et utilise un jeton SAP pour le compte de stockage), vous devez vous assurer que les informations d’identification sont tenues à jour. Si les informations d’identification, y compris les certificats et les jetons, ont expiré, la mise à niveau échoue, et le premier groupe de machines virtuelles reste dans un état d’échec.

Pour récupérer les machines virtuelles et réactiver la mise à niveau automatique du système d’exploitation après un échec d’authentification des ressources, effectuez les étapes recommandées suivantes :

* Regénérez le jeton (ou d’autres informations d’identification) qui a été passé à vos extensions.
* Vérifiez que toutes les informations d’identification utilisées pour les communications entre les machines virtuelles et les entités externes sont tenues à jour.
* Mettez à jour chaque extension dans le modèle de groupe identique avec les nouveaux jetons.
* Déployez le groupe identique mis à jour. Cette opération met à jour toutes les instances de machine virtuelle, y compris celles en échec. 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Obtenir l’historique des mises à niveau automatiques d’image de système d’exploitation 
Vous pouvez vérifier l’historique de la dernière mise à niveau du système d’exploitation effectuée dans un groupe identique à l’aide d’Azure PowerShell, d’Azure CLI 2.0 ou des API REST. Vous pouvez obtenir l’historique des cinq dernières tentatives de mise à niveau du système d’exploitation au cours des deux derniers mois.

### <a name="azure-powershell"></a>Azure PowerShell
L’exemple suivant utilise Azure PowerShell pour vérifier l’état du groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
L’exemple suivant utilise Azure CLI (version 2.0.47 ou ultérieure) pour vérifier l’état du groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>API REST
L’exemple suivant utilise l’API REST pour vérifier l’état de la mise à niveau pour le groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
Reportez-vous à la documentation de cette API ici : https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory.

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

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Comment obtenir la dernière version d’une image de système d’exploitation de plateforme ? 

Vous pouvez obtenir les versions d’image des références SKU prises en charge pour la mise à niveau automatique du système d’exploitation en utilisant les exemples ci-dessous : 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Déployer les mises à niveau avec un modèle

Vous pouvez utiliser le modèle suivant pour déployer un groupe identique qui utilise les mises à niveau automatiques. Consultez <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>Mises à niveau propagées automatiques (Ubuntu 16.04-LTS)</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres exemples d’utilisation des mises à niveau automatiques du système d’exploitation avec des groupes identiques, consultez le [dépôt GitHub des fonctionnalités en préversion](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
